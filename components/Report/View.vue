<template>
  <v-dialog v-model="show" max-width="880" scrollable>
    <v-card class="view-card">
      <div class="view-header">
        <div>
          <h3 class="view-title">ໃບບິນຄ່າເຊົ່າ - ຫ້ອງ {{ bill.room }}</h3>
          <p class="view-subtitle">ເລກໃບບິນ: {{ bill.invoiceNo || 'xxxxxxxx' }}</p>
        </div>
        <v-chip
          :color="isPaid(bill.status) ? 'success' : 'error'"
          text-color="white"
          small
          label
          class="status-chip"
        >
          {{ isPaid(bill.status) ? 'ຊຳລະແລ້ວ' : 'ຄ້າງຊຳລະ' }}
        </v-chip>
      </div>

      <v-divider class="header-divider"></v-divider>

      <v-card-text class="view-body">
        <!-- Row 1: Tenant info / Bill info -->
        <v-row>
          <v-col cols="12" md="6">
            <p class="section-label">ຂໍ້ມູນຜູ້ເຊົ່າ</p>

            <div class="info-line">
              <span class="info-label">ຊື່ ແລະ ນາມສະກຸນ</span>
              <span class="info-value">{{ bill.tenantName || '-' }}</span>
            </div>
            <div class="info-line">
              <span class="info-label">ເບີໂທລະສັບ</span>
              <span class="info-value">{{ bill.phone || '-' }}</span>
            </div>
            <div class="info-line">
              <span class="info-label">ຫ້ອງແຖວ / ໂຄງການສັນຍາ</span>
              <span class="info-value">
                ຫ້ອງ {{ bill.room }} ເລີ່ມວັນທີ {{ bill.contractStart || '-' }} - {{ bill.contractEnd || '-' }}
              </span>
            </div>
          </v-col>

          <v-col cols="12" md="6">
            <p class="section-label">ຂໍ້ມູນໃບບິນ</p>

            <div class="info-line">
              <span class="info-label">ວັດຄິດໄລ່</span>
              <span class="info-value">{{ bill.billingMonth || '-' }}</span>
            </div>
            <div class="info-line">
              <span class="info-label">ຮູບແບບການຊຳລະ</span>
              <span class="info-value">{{ bill.paymentMethod || 'ລາຍເດືອນ' }}</span>
            </div>
            <div class="info-line">
              <span class="info-label">ສະຖານະ</span>
              <span
                class="info-value"
                :class="isPaid(bill.status) ? 'text-success' : 'text-error'"
              >
                {{ isPaid(bill.status) ? 'ຊຳລະແລ້ວ' : 'ຄ້າງຊຳລະ' }}
              </span>
            </div>
            <div class="info-line">
              <span class="info-label">ເລກໃບບິນຮັບເງິນ</span>
              <span class="info-value">{{ bill.receiptNo || 'xxxxxxxx' }}</span>
            </div>
          </v-col>
        </v-row>

        <v-divider class="my-4"></v-divider>

        <!-- Row 2: Cost breakdown -->
        <p class="section-label">ລາຍລະອຽດຄ່າໃຊ້ຈ່າຍ</p>

        <div class="cost-list-header">
          <span>ລາຍການ</span>
        </div>

        <div v-for="line in costLines" :key="line.key" class="cost-line">
          <span>{{ line.label }}</span>
          <span>{{ formatCurrency(bill[line.key]) }} LAK</span>
        </div>

        <div class="cost-total-line">
          <span>ຍອດລວມທັງໝົດ</span>
          <span>{{ formatCurrency(billTotal) }} LAK</span>
        </div>

        <v-divider class="my-4"></v-divider>

        <!-- Row 3: Payment history -->
        <p class="section-label">ປະຫວັດການຊຳລະ</p>

        <div
          v-for="(entry, idx) in bill.paymentHistory || []"
          :key="idx"
          class="history-line"
        >
          <span>{{ entry.label }}</span>
          <span :class="isPaid(bill.status) ? 'text-success' : 'text-error'">
            {{ isPaid(bill.status) ? `ຊຳລະແລ້ວ ${entry.date || ''}` : 'ຄ້າງຊຳລະ' }}
          </span>
        </div>

        <p v-if="!bill.paymentHistory || !bill.paymentHistory.length" class="no-history">
          ບໍ່ມີປະຫວັດການຊຳລະ
        </p>
      </v-card-text>

      <v-card-actions class="view-actions">
        <v-spacer></v-spacer>
        <v-btn color="#064d8d" dark @click="close">ປິດແທັບ</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  name: 'ReportView',
  props: {
    visible: {
      type: Boolean,
      default: false,
    },
    bill: {
      type: Object,
      default: () => ({}),
    },
  },
  data() {
    return {
      // Match the fields returned by the report page after normalizing the API payload.
      costLines: [
        { key: 'roomPrice', label: 'ຄ່າຫ້ອງແຖວ' },
        { key: 'waterPrice', label: 'ຄ່ານ້ຳ' },
        { key: 'electricityPrice', label: 'ຄ່າໄຟ' },
        { key: 'wasteFees', label: 'ຄ່າຂີ້ເຫຍື້ອ' },
      ],
    }
  },
  computed: {
    show: {
      get() {
        return this.visible
      },
      set(val) {
        if (!val) this.close()
      },
    },
    billTotal() {
      // Prefer a total the backend already computed; otherwise sum the lines ourselves.
      if (this.bill.total != null) return this.bill.total
      return this.costLines.reduce((sum, line) => {
        return sum + (Number(this.bill[line.key]) || 0)
      }, 0)
    },
  },
  methods: {
    formatCurrency(value) {
      return Number(value || 0).toLocaleString()
    },
    isPaid(status) {
      return status === 1 || status === '1' || status === 'paid' || status === 'Paid'
    },
    close() {
      this.$emit('close')
    },
  },
}
</script>

<style scoped>
.view-card {
  padding: 24px 28px 14px;
}

.view-header {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
}

.view-title {
  margin: 0;
  font-size: 20px;
  font-weight: 700;
  color: #111827;
}

.view-subtitle {
  margin: 4px 0 0;
  font-size: 13px;
  color: #6b7280;
}

.status-chip {
  font-weight: 600;
  border-radius: 6px;
}

.header-divider {
  margin: 16px 0 0;
}

.view-body {
  padding-top: 20px !important;
}

.section-label {
  font-size: 15px;
  font-weight: 700;
  color: #111827;
  margin-bottom: 14px;
}

.info-line {
  display: flex;
  flex-direction: column;
  margin-bottom: 14px;
}

.info-label {
  font-size: 14px;
  font-weight: 700;
  color: #111827;
  margin-bottom: 2px;
}

.info-value {
  font-size: 14px;
  color: #4b5563;
  font-weight: 400;
}

.text-success {
  color: #16a34a !important;
}

.text-error {
  color: #dc2626 !important;
}

.cost-list-header {
  font-weight: 700;
  color: #111827;
  border-bottom: 1px solid #e5e7eb;
  padding-bottom: 8px;
  margin-bottom: 4px;
}

.cost-line {
  display: flex;
  justify-content: space-between;
  padding: 10px 0;
  font-size: 14px;
  color: #374151;
}

.cost-total-line {
  display: flex;
  justify-content: space-between;
  padding: 14px 0;
  margin-top: 4px;
  border-top: 1px solid #e5e7eb;
  font-weight: 700;
  font-size: 15px;
  color: #111827;
}

.history-line {
  display: flex;
  justify-content: space-between;
  padding: 8px 0;
  font-size: 14px;
  color: #374151;
}

.no-history {
  font-size: 13px;
  color: #9ca3af;
}

.view-actions {
  padding: 12px 0 4px;
}
</style>