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
          <ReportCreate @created="addReport"></ReportCreate>
        </v-col>
      </v-row>

      <v-data-table
        :headers="headers"
        :items="reports"
        :search="search"
        class="report-table"
      >
        <template v-slot:item.no="{ item }">
          {{ reports.indexOf(item) + 1 }}
        </template>

        <template v-slot:item.payment="{ item }">
          <v-chip
            v-if="isPaymentStatus(item.payment)"
            :color="item.payment === 'Paid' ? 'success' : 'error'"
            text-color="white"
            small
            label
          >
            {{ item.payment }}
          </v-chip>
          <span v-else>{{ formatCurrency(item.payment) }}</span>
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
      :room-label="selectedReport.room || ''"
      :tenant="selectedReport"
      @close="editVisible = false"
      @save="saveReport"
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
        <p class="calc-room">ຫ້ອງເລກທີ {{ calcForm.room }}</p>

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
                :value="formatAmount(calcForm[line.key])"
                type="text"
                min="0"
                inputmode="numeric"
                @input="onCalcInput(line.key, $event.target.value)"
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
          <v-btn color="#064D8D" dark class="save-bill-btn" @click="saveCalculation">
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

export default {
  name: 'ReportSellPage',
  components: {
    ReportCreate,
    ReportUpdate,
  },
  data() {
    return {
      search: '',
      headers: [
        { text: 'ລຳດັບ', value: 'no', sortable: false, width: '70' },
        { text: 'ຫ້ອງ', value: 'room', sortable: false },
        { text: 'ຜູ້ເຊົ່າ', value: 'lessee', sortable: false },
        { text: 'ສັນຍານເຊົ່າ', value: 'leaseContract', sortable: false },
        { text: 'ຊຳລະ', value: 'payment', sortable: false },
        { text: 'ລາຍລະອຽດ', value: 'details', sortable: false, align: 'center', width: '130' },
        { text: 'ຈັດການ', value: 'manage', sortable: false, align: 'center', width: '80' },
        { text: 'ຄິດໄລ່', value: 'calculate', sortable: false, align: 'center', width: '90' },
      ],
      reports: [
        {
          room: 'A101',
          lessee: 'ນາງ ສຸລິຍະ',
          leaseContract: 'LC-001',
          payment: 'Paid',
          details: 'ເຊົ່າ 1 ອາທິດ',
          roomRent: 1500000,
          waterFee: 0,
          electricityFee: 0,
          garbageFee: 0,
          total: null,
        },
        {
          room: 'A102',
          lessee: 'ທ່ານ ສົມສະຫຼີ',
          leaseContract: 'LC-002',
          payment: 'Unpaid',
          details: 'ເຊົ່າ 2 ອາທິດ',
          roomRent: 1500000,
          waterFee: 0,
          electricityFee: 0,
          garbageFee: 0,
          total: null,
        },
      ],
      editVisible: false,
      selectedReport: {},
      selectedIndex: -1,

      // --- calculation dialog state ---
      calcDialog: false,
      calcIndex: -1,
      calcForm: {
        room: '',
        roomRent: 0,
        waterFee: 0,
        electricityFee: 0,
        garbageFee: 0,
      },

      // Labels shown in the bill's "ລາຍການ" list — order matches the mockup
      calcLineItems: [
        { key: 'roomRent', label: 'ຄ່າຫ້ອງເຊົ່າ' },
        { key: 'waterFee', label: 'ຄ່ານ້ຳ' },
        { key: 'electricityFee', label: 'ຄ່າໄຟ' },
        { key: 'garbageFee', label: 'ຄ່າຂີ້ເຫຍື້ອ' },
      ],
    }
  },
  computed: {
    // Central formula — change here if your real calculation differs
    // (e.g. add a service fee or late-payment penalty).
    calcTotal() {
      return this.calcLineItems.reduce((sum, line) => {
        const rawValue = String(this.calcForm[line.key] ?? '0').replace(/,/g, '')
        return sum + (Number(rawValue) || 0)
      }, 0)
    },
  },
  methods: {
    formatAmount(value) {
      if (value === null || value === undefined || value === '') return ''
      const numeric = Number(String(value).replace(/,/g, ''))
      if (Number.isNaN(numeric)) return ''
      return numeric.toLocaleString()
    },

    onCalcInput(key, value) {
      const digits = String(value || '').replace(/[^\d]/g, '')
      this.$set(this.calcForm, key, digits ? Number(digits).toLocaleString() : '')
    },

    formatCurrency(value) {
      return `${Number(value || 0).toLocaleString()} ₭`
    },

    isPaymentStatus(value) {
      return value === 'Paid' || value === 'Unpaid'
    },

    addReport(report) {
      this.reports.push(report)
    },

    viewDetail(item) {
      item.showDetails = true
    },

    openManage(item) {
      this.selectedIndex = this.reports.indexOf(item)
      this.selectedReport = { ...item }
      this.editVisible = true
    },

    saveReport(report) {
      if (this.selectedIndex > -1) {
        Object.assign(this.reports[this.selectedIndex], report)
      }
      this.editVisible = false
      this.selectedIndex = -1
    },

    calculate(item) {
      this.calcIndex = this.reports.indexOf(item)
      this.calcForm = {
        room: item.room,
        roomRent: item.roomRent || 0,
        waterFee: item.waterFee || 0,
        electricityFee: item.electricityFee || 0,
        garbageFee: item.garbageFee || 0,
      }
      this.calcDialog = true
    },

    saveCalculation() {
      if (this.calcIndex > -1) {
        Object.assign(this.reports[this.calcIndex], {
          roomRent: Number(String(this.calcForm.roomRent || '0').replace(/,/g, '')),
          waterFee: Number(String(this.calcForm.waterFee || '0').replace(/,/g, '')),
          electricityFee: Number(String(this.calcForm.electricityFee || '0').replace(/,/g, '')),
          garbageFee: Number(String(this.calcForm.garbageFee || '0').replace(/,/g, '')),
          total: this.calcTotal,
          calculated: true,
        })
      }
      this.calcDialog = false
      this.calcIndex = -1
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
  color: #064D8D;
}

.calc-room {
  margin: 2px 0 0;
  font-size: 14px;
  color: #064D8D;
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