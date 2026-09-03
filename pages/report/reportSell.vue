<template>
  <div>
    <h2 class="page-title mb-4">ລາຍງານຫ້ອງ</h2>

    <v-card class="pa-4" elevation="1">
      <v-row class="align-center mb-2" no-gutters>
        <v-col cols="12" sm="6" md="4">
          <v-text-field
            v-model="search"
            placeholder="ຄົ້ນຫາ"
            prepend-inner-icon="mdi-magnify"
            outlined
            dense
            hide-details
          ></v-text-field>
        </v-col>

        <v-spacer></v-spacer>

        <v-col cols="auto">
          <ReportCreate @created="handleCreated"></ReportCreate>
        </v-col>
      </v-row>

      <v-data-table
        :headers="headers"
        :items="reports"
        :search="search"
        :loading="loading"
        class="report-table"
      >
        <template v-slot:item.no="{ item }">
          {{ reports.indexOf(item) + 1 }}
        </template>

        <!-- backend only gives status 0/1, tenant name comes from a separate call -->
        <template v-slot:item.lessee="{ item }">
          {{ item.lessee || '-' }}
        </template>

        <template v-slot:item.leaseTime="{ item }">
          {{ item.leaseTime || '-' }}
        </template>

        <template v-slot:item.overdue="{ item }">
          <span :class="{ 'red--text font-weight-bold': item.overdue > 0 }">
            {{ item.overdue > 0 ? `ຄ້າງ ${item.overdue} ເດືອນ` : 'ບໍ່ຄ້າງ' }}
          </span>
        </template>

        <template v-slot:item.payment="{ item }">
          <v-chip
            :color="item.status === 1 ? 'success' : 'error'"
            text-color="white"
            small
            label
            style="cursor: pointer"
            @click="togglePaid(item)"
          >
            {{ item.status === 1 ? 'Paid' : 'Unpaid' }}
          </v-chip>
        </template>

        <template v-slot:item.details="{ item }">
          <v-btn
            text
            small
            color="primary"
            @click="viewDetail(item)"
            class="detail-btn"
          >
            ລາຍລະອຽດ
          </v-btn>
        </template>

        <template v-slot:item.manage="{ item }">
          <v-btn
            icon
            small
            color="primary"
            @click="openManage(item)"
            title="ຈັດການ"
          >
            <v-icon small>mdi-pencil</v-icon>
          </v-btn>
        </template>

        <template v-slot:item.calculate="{ item }">
          <v-btn
            icon
            small
            color="warning"
            @click="calculate(item)"
            title="ຄິດໄລ່"
          >
            <v-icon small>mdi-calculator</v-icon>
          </v-btn>
        </template>

        <template v-slot:no-data>
          <v-alert type="info" text>
            ບໍ່ມີຂໍ້ມູນລາຍງານ
          </v-alert>
        </template>
      </v-data-table>
    </v-card>

    <ReportUpdate
      :visible="editVisible"
      :room-label="selectedReport.name || ''"
      :tenant="selectedReport"
      @close="editVisible = false"
      @save="saveReport"
    />

    <ReportView
      :visible="viewVisible"
      :bill="selectedBill"
      @close="viewVisible = false"
    />

    <!-- ຄິດໄລ່ຄ່າເຊົ່າ -->
    <v-dialog v-model="calcDialog" max-width="480">
      <v-card class="calc-card">
        <div class="calc-header">
          <span class="calc-title">ຄິດໄລ່ຄ່າເຊົ່າ</span>
          <v-btn icon small @click="calcDialog = false">
            <v-icon small>mdi-close</v-icon>
          </v-btn>
        </div>
        <p class="calc-room">ຫ້ອງເລກທີ {{ calcForm.name }}</p>

        <v-card-text class="calc-body">
          <div class="calc-list-header">
            <span>ລາຍການ</span>
            <span>ຈຳນວນ</span>
          </div>

          <div
            v-for="line in calcLineItems"
            :key="line.key"
            class="calc-line"
          >
            <span class="calc-line-label">{{ line.label }}</span>
            <div class="calc-line-input">
              <input
                v-model.number="calcForm[line.key]"
                type="number"
                min="0"
              />
              <span class="calc-unit">LAK</span>
            </div>
          </div>

          <div class="calc-total-row">
            <span>ລວມທັງໝົດ</span>
            <span class="calc-total-value">{{ formatCurrency(calcTotal) }} LAK</span>
          </div>
        </v-card-text>

        <v-card-actions class="calc-actions">
          <v-btn outlined class="print-btn" @click="printBill">
            <v-icon small left>mdi-printer</v-icon>
            ພິມ
          </v-btn>
          <v-btn color="primary" class="save-bill-btn" :loading="savingBill" @click="saveCalculation">
            ບັນທຶກໃບບິນ
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import ReportCreate from '~/components/Report/Create.vue'
import ReportUpdate from '~/components/Report/Update.vue'
import ReportView from '~/components/Report/View.vue'

// Adjust this if your project already has a configured axios instance
// (this.$axios via @nuxtjs/axios) or a wrapped api client.
const API_BASE = '/admin/report'
const FETCH_API_BASE = '/api/admin/report'

export default {
  name: 'ReportSellPage',
  components: {
    ReportCreate,
    ReportUpdate,
    ReportView,
  },
  data() {
    return {
      search: '',
      loading: false,
      headers: [
        { text: 'ລຳດັບ', value: 'no', sortable: false, width: '70' },
        { text: 'ຫ້ອງ', value: 'name', sortable: false },
        { text: 'ຜູ້ເຊົ່າ', value: 'lessee', sortable: false },
        { text: 'ໄລຍະສັນຍາ', value: 'leaseTime', sortable: false },
        { text: 'ຄ້າງຊຳລະ', value: 'overdue', sortable: false },
        { text: 'ຊຳລະ', value: 'payment', sortable: false },
        { text: 'ລາຍລະອຽດ', value: 'details', sortable: false, align: 'center', width: '130' },
        { text: 'ຈັດການ', value: 'manage', sortable: false, align: 'center', width: '80' },
        { text: 'ຄິດໄລ່', value: 'calculate', sortable: false, align: 'center', width: '90' },
      ],
      // Populated from GET /api/admin/report/unpaid, one row per room:
      // { roomId, name, leaseTime, overdue, status, lessee }
      reports: [],

      editVisible: false,
      selectedReport: {},
      selectedIndex: -1,

      viewVisible: false,
      selectedBill: {},

      // --- calculation dialog state ---
      calcDialog: false,
      calcRoomId: null,
      savingBill: false,
      calcForm: {
        name: '',
        roomPrice: 0,
        waterPrice: 0,
        electricityPrice: 0,
        wasteFees: 0,
      },

      // Field names now match the backend's bill payload
      // (roomPrice, electricityPrice, waterPrice, wasteFees)
      calcLineItems: [
        { key: 'roomPrice', label: 'ຄ່າຫ້ອງເຊົ່າ' },
        { key: 'waterPrice', label: 'ຄ່ານ້ຳ' },
        { key: 'electricityPrice', label: 'ຄ່າໄຟ' },
        { key: 'wasteFees', label: 'ຄ່າຂີ້ເຫຍື້ອ' },
      ],
    }
  },
  computed: {
    calcTotal() {
      return this.calcLineItems.reduce((sum, line) => {
        return sum + (Number(this.calcForm[line.key]) || 0)
      }, 0)
    },
  },
  created() {
    this.fetchReports()
  },
  methods: {
    formatCurrency(value) {
      return `${Number(value || 0).toLocaleString()} ₭`
    },

    // GET /api/admin/report/unpaid -> { roomId, name, leaseTime, overdue, status }
    // Tenant name isn't included in this endpoint, so we enrich each row
    // with a follow-up call to /rooms/:id/info.
    async fetchReports() {
      this.loading = true
      try {
        const { data } = await this.$axios.$get
          ? { data: await this.$axios.$get(`${API_BASE}/unpaid`) }
          : { data: await (await fetch(`${FETCH_API_BASE}/unpaid`)).json() }

        const rows = data.data || []

        const enriched = await Promise.all(
          rows.map(async (row) => {
            let lessee = ''
            try {
              const info = await this.fetchRoomInfo(row.roomId)
              if (info && info.tenant) {
                lessee = `${info.tenant.name || ''} ${info.tenant.lastname || ''}`.trim()
              }
            } catch (e) {
              // no tenant assigned yet, or info lookup failed - leave blank
            }
            return { ...row, lessee }
          })
        )

        this.reports = enriched
      } catch (err) {
        console.error('Failed to load report list', err)
      } finally {
        this.loading = false
      }
    },

    async fetchRoomInfo(roomId) {
      const res = this.$axios.$get
        ? await this.$axios.$get(`${API_BASE}/rooms/${roomId}/info`)
        : await (await fetch(`${FETCH_API_BASE}/rooms/${roomId}/info`)).json()
      return res.data
    },

    // PUT /api/admin/report/rooms/:id/status  { status: 0 | 1 }
    // Note: flipping Not paid -> Paid resets the bill price detail server-side.
    async togglePaid(item) {
      const newStatus = item.status === 1 ? 0 : 1
      try {
        if (this.$axios.$put) {
          await this.$axios.$put(`${API_BASE}/rooms/${item.roomId}/status`, {
            status: newStatus,
          })
        } else {
          await fetch(`${FETCH_API_BASE}/rooms/${item.roomId}/status`, {
            method: 'PUT',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ status: newStatus }),
          })
        }
        item.status = newStatus
        if (newStatus === 1) item.overdue = 0
      } catch (err) {
        console.error('Failed to update status', err)
      }
    },

    handleCreated(payload = {}) {
      // const normalized = {
      //   roomId: payload.roomId || payload.id || Date.now(),
      //   name: payload.name || payload.room || payload.roomName || 'Unknown room',
      //   lessee: payload.lessee || [payload.firstName, payload.lastName].filter(Boolean).join(' ') || '-',
      //   leaseTime: payload.leaseTime || [payload.dateStart, payload.dateEnd].filter(Boolean).join(' - ') || '—',
      //   overdue: Number(payload.overdue || 0),
      //   status: Number(payload.status ?? 0),
      //   roomPrice: Number(payload.roomPrice ?? payload.payment ?? 0),
      //   waterPrice: Number(payload.waterPrice || 0),
      //   electricityPrice: Number(payload.electricityPrice || 0),
      //   wasteFees: Number(payload.wasteFees || 0),
      //   total: Number(payload.total ?? payload.roomPrice ?? payload.payment ?? 0),
      //   ...payload,
      // }

      // const exists = this.reports.some(
      //   (row) =>
      //     row.roomId === normalized.roomId ||
      //     (row.name === normalized.name && row.lessee === normalized.lessee)
      // )

      // if (!exists) {
      //   this.reports = [normalized, ...this.reports]
      // }

      this.fetchReports()
    },

    async viewDetail(item) {
      try {
        const info = await this.fetchRoomInfo(item.roomId)
        const tenant = info?.tenant || {}
        const priceDetail = info?.priceDetail || {}

        this.selectedBill = {
          room: info?.roomName || info?.name || item.name || item.roomId,
          status: info?.status ?? item.status ?? 0,
          tenantName: tenant.name
            ? `${tenant.name} ${tenant.lastname || ''}`.trim()
            : info?.tenantName || '-',
          phone: tenant.tel || tenant.phone || info?.phone || '-',
          roomPrice: priceDetail.roomPrice ?? priceDetail.roomRent ?? 0,
          waterPrice: priceDetail.waterPrice ?? 0,
          electricityPrice: priceDetail.electricityPrice ?? 0,
          wasteFees: priceDetail.wasteFees ?? 0,
          total: info?.total ?? priceDetail.total ?? 0,
        }

        this.viewVisible = true
      } catch (err) {
        console.error('Failed to load room info', err)
      }
    },

    async openManage(item) {
      this.selectedIndex = this.reports.indexOf(item)
      try {
        const info = await this.fetchRoomInfo(item.roomId)
        const tenant = info.tenant || {}
        this.selectedReport = {
          ...item,
          name: tenant.name || '',
          lastname: tenant.lastname || '',
          tel: tenant.phone || '',
          startDate: info.startDate || '',
          endDate: info.endDate || '',
        }
        this.editVisible = true
      } catch (err) {
        console.error('Failed to load tenant/lease details', err)
      }
    },

    // ReportUpdate should emit { name, lastname, tel, link, startDate, endDate }
    // Uses POST for new tenant/lease records and PUT for existing records.
    async saveReport(payload) {
      const item = this.reports[this.selectedIndex]
      if (!item) return

      try {
        const tenantBody = {
          name: payload.name,
          lastname: payload.lastname,
          tel: payload.tel,
          link: payload.link,
        }
        const leaseBody = {
          startDate: payload.startDate,
          endDate: payload.endDate,
        }

        const info = await this.fetchRoomInfo(item.roomId)
        await this.request(info.tenant ? 'put' : 'post', `${API_BASE}/rooms/${item.roomId}/tenant`, tenantBody)
        await this.request(info.startDate ? 'put' : 'post', `${API_BASE}/rooms/${item.roomId}/lease`, leaseBody)

        await this.fetchReports()
      } catch (err) {
        console.error('Failed to save tenant/lease', err)
      } finally {
        this.editVisible = false
        this.selectedIndex = -1
      }
    },

    calculate(item) {
      this.calcRoomId = item.roomId
      this.calcForm = {
        name: item.name,
        roomPrice: item.roomPrice || 0,
        waterPrice: item.waterPrice || 0,
        electricityPrice: item.electricityPrice || 0,
        wasteFees: item.wasteFees || 0,
      }
      this.calcDialog = true
    },

    async request(method, url, body) {
      const axiosMethod = this.$axios[`$${method}`]
      if (axiosMethod) return axiosMethod.call(this.$axios, url, body)
      return fetch(url.replace(API_BASE, FETCH_API_BASE), {
        method: method.toUpperCase(),
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(body),
      })
    },

    // POST /api/admin/report/rooms/:id/bill
    // { roomPrice, electricityPrice, waterPrice, wasteFees } -> { ...same, total }
    async saveCalculation() {
      if (!this.calcRoomId) return
      this.savingBill = true

      const body = {
        roomPrice: this.calcForm.roomPrice,
        electricityPrice: this.calcForm.electricityPrice,
        waterPrice: this.calcForm.waterPrice,
        wasteFees: this.calcForm.wasteFees,
      }

      try {
        const response = this.$axios.$post
          ? await this.$axios.$post(`${API_BASE}/rooms/${this.calcRoomId}/bill`, body)
          : await (
              await fetch(`${FETCH_API_BASE}/rooms/${this.calcRoomId}/bill`, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(body),
              })
            ).json()
          const bill = response.data || response

        // Use the server-computed total rather than recalculating client-side
        const row = this.reports.find((r) => r.roomId === this.calcRoomId)
        if (row) {
          Object.assign(row, body, { total: bill.total })
        }
      } catch (err) {
        console.error('Failed to save bill', err)
      } finally {
        this.savingBill = false
        this.calcDialog = false
        this.calcRoomId = null
      }
    },

    printBill() {
      // Hook this up to your real bill/invoice print view or PDF generator.
      window.print()
    },
  },
}
</script>

<style scoped>
.page-title {
  font-weight: 600;
}

.detail-btn {
  text-transform: none !important;
  font-weight: 600;
}

.report-table >>> thead tr th {
  background-color: #064d8d !important;
  color: #ffffff !important;
}

.calc-card {
  padding: 20px 24px 16px;
}

.calc-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.calc-title {
  font-size: 18px;
  font-weight: 700;
  color: #111827;
}

.calc-room {
  margin: 2px 0 0;
  font-size: 14px;
  color: #1976d2;
  font-weight: 600;
}

.calc-body {
  padding: 16px 0 0 !important;
}

.calc-list-header {
  display: flex;
  justify-content: space-between;
  font-weight: 600;
  color: #374151;
  padding-bottom: 8px;
  border-bottom: 1px solid #e5e7eb;
  margin-bottom: 8px;
}

.calc-line {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px 0;
}

.calc-line-label {
  color: #374151;
  font-size: 14px;
}

.calc-line-input {
  display: flex;
  align-items: center;
  border: 1px solid #d1d5db;
  border-radius: 6px;
  padding: 0 10px;
  width: 180px;
}

.calc-line-input input {
  border: none;
  outline: none;
  width: 100%;
  padding: 8px 4px;
  font-size: 14px;
  text-align: right;
}

.calc-unit {
  font-size: 12px;
  color: #9ca3af;
  margin-left: 6px;
}

.calc-total-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-weight: 700;
  padding: 14px 0;
  margin-top: 6px;
  border-top: 1px solid #e5e7eb;
}

.calc-total-value {
  color: #111827;
  font-size: 15px;
}

.calc-actions {
  padding: 4px 0 8px;
  gap: 12px;
}

.print-btn {
  flex: 1;
  text-transform: none !important;
}

.save-bill-btn {
  flex: 1.4;
  text-transform: none !important;
}
</style>