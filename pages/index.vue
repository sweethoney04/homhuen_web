<template>
  <div class="dashboard-page">
    <h2 class="page-title">ພາບລວມ</h2>

    <div v-if="loading" class="state-wrap">
      <v-progress-circular indeterminate color="primary"></v-progress-circular>
      <p>ກຳລັງໂຫຼດຂໍ້ມູນ...</p>
    </div>

    <v-alert v-else-if="error" type="error" text>
      ບໍ່ສາມາດໂຫຼດຂໍ້ມູນ Dashboard ໄດ້. {{ error }}
      <v-btn text small color="error" @click="fetchDashboard">ລອງໃໝ່</v-btn>
    </v-alert>

    <template v-else>

    <!-- Row 1: 4 top stat cards -->
    <v-row class="stat-row" no-gutters>
      <v-col
        v-for="stat in topStats"
        :key="stat.label"
        cols="12"
        sm="6"
        md="3"
        class="stat-col"
      >
        <v-card class="stat-card" outlined>
          <p class="stat-label">{{ stat.label }}</p>
          <p class="stat-value">{{ stat.value }}</p>
        </v-card>
      </v-col>
    </v-row>

    <!-- Row 2: 3 finance stat cards -->
    <v-row class="stat-row" no-gutters>
      <v-col
        v-for="stat in financeStats"
        :key="stat.label"
        cols="12"
        sm="6"
        md="4"
        class="stat-col"
      >
        <v-card class="stat-card" outlined>
          <p class="stat-label">{{ stat.label }}</p>
          <p class="stat-value">{{ stat.value }}</p>
        </v-card>
      </v-col>
    </v-row>

    <!-- Row 3: occupancy donut + room type progress -->
    <v-row class="stat-row" no-gutters>
      <v-col cols="12" md="6" class="stat-col">
        <v-card class="panel-card" outlined>
          <p class="panel-title">ອັດຕາການເຂົ້າພັກ</p>

          <div class="donut-wrap">
            <svg viewBox="0 0 140 140" class="donut-svg">
              <circle
                cx="70"
                cy="70"
                r="55"
                fill="none"
                stroke="#e5e7eb"
                stroke-width="18"
              />
              <circle
                cx="70"
                cy="70"
                r="55"
                fill="none"
                stroke="#8fd19e"
                stroke-width="18"
                :stroke-dasharray="donutVacantDash"
                :stroke-dashoffset="donutOccupiedLength"
                transform="rotate(-90 70 70)"
              />
              <circle
                cx="70"
                cy="70"
                r="55"
                fill="none"
                stroke="#1e3a5f"
                stroke-width="18"
                :stroke-dasharray="donutOccupiedDash"
                stroke-dashoffset="0"
                transform="rotate(-90 70 70)"
              />
            </svg>

            <div class="donut-legend">
              <div class="legend-line">
                <span class="dot dot-occupied"></span>
                <span>ຫ້ອງມີຄົນເຂົ້າພັກ</span>
                <span class="legend-pct">{{ occupancy.occupiedPct }}%</span>
              </div>
              <div class="legend-line">
                <span class="dot dot-vacant"></span>
                <span>ຫ້ອງບໍ່ມີຄົນເຂົ້າພັກ</span>
                <span class="legend-pct">{{ occupancy.vacantPct }}%</span>
              </div>
            </div>
          </div>
        </v-card>
      </v-col>

      <v-col cols="12" md="6" class="stat-col">
        <v-card class="panel-card" outlined>
          <p class="panel-title">ປະເພດຫ້ອງ</p>

          <div
            v-for="type in roomTypes"
            :key="type.label"
            class="room-type-row"
          >
            <div class="room-type-header">
              <span>{{ type.label }}</span>
              <span>{{ type.count }} ຫ້ອງ</span>
            </div>
            <div class="progress-track">
              <div
                class="progress-fill"
                :style="{ width: roomTypePercent(type) + '%' }"
              ></div>
            </div>
          </div>
        </v-card>
      </v-col>
    </v-row>

    <!-- Row 4: revenue bar chart -->
    <v-row class="stat-row" no-gutters>
      <v-col cols="12" class="stat-col">
        <v-card class="panel-card" outlined>
          <div class="chart-header">
            <p class="panel-title">ລາຍໄດ້ຍ້ອນຫຼັງ 6 ເດືອນ</p>
            <span class="range-badge">6 months</span>
          </div>

          <div class="bar-chart">
            <div class="bar-chart-axis">
              <span
                v-for="tick in chartTicks"
                :key="tick"
                class="axis-label"
              >
                {{ formatAxisTick(tick) }}
              </span>
            </div>

            <div class="bar-chart-bars">
              <div class="chart-grid" aria-hidden="true">
                <span
                  v-for="tick in chartTicks"
                  :key="`grid-${tick}`"
                  class="chart-grid-line"
                  :style="{ top: gridLinePosition(tick) + '%' }"
                ></span>
              </div>
              <div
                v-for="item in revenueChart"
                :key="item.month"
                class="bar-col"
              >
                <div class="bar-track">
                  <div
                    class="bar-fill"
                    :style="{ height: barHeightPercent(item.value) + '%' }"
                  ></div>
                </div>
                <span class="bar-month">{{ item.month }}</span>
              </div>
            </div>
          </div>
        </v-card>
      </v-col>
    </v-row>

    <!-- Row 5: expiring contracts + top debtors -->
    <v-row class="stat-row" no-gutters>
      <v-col cols="12" md="7" class="stat-col">
        <p class="section-heading">ສັນຍາໃກ້ໝົດອາຍຸ (ພາຍໃນ 30 ວັນ)</p>
        <v-card class="table-card" outlined>
          <table class="dash-table">
            <thead>
              <tr>
                <th>ຫ້ອງ</th>
                <th>ວັນທີ່ສິ້ນສຸດ</th>
                <th>ເຫຼືອ</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(row, idx) in expiringContracts" :key="idx">
                <td>{{ row.room }}</td>
                <td>{{ row.endDate }}</td>
                <td>
                  <span class="days-badge">{{ row.daysLeft }} ວັນ</span>
                </td>
              </tr>
              <tr v-if="!expiringContracts.length">
                <td colspan="3" class="empty-row">ບໍ່ມີສັນຍາໃກ້ໝົດອາຍຸ</td>
              </tr>
            </tbody>
          </table>
        </v-card>
      </v-col>

      <v-col cols="12" md="5" class="stat-col">
        <p class="section-heading">5 ອັນດັບຫ້ອງຄ້າງຊຳລະດົນສຸດ</p>
        <v-card class="table-card" outlined>
          <table class="dash-table">
            <thead>
              <tr>
                <th>ຫ້ອງ</th>
                <th class="align-right">ຄ້າງມາແລ້ວ</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(row, idx) in topOverdue" :key="idx">
                <td>{{ row.room }}</td>
                <td class="align-right">{{ row.overdueMonths }} ເດືອນ</td>
              </tr>
              <tr v-if="!topOverdue.length">
                <td colspan="2" class="empty-row">ບໍ່ມີຂໍ້ມູນ</td>
              </tr>
            </tbody>
          </table>
        </v-card>
      </v-col>
    </v-row>
    </template>
  </div>
</template>

<script>
export default {
  name: 'DashboardPage',
  data() {
    return {
      loading: true,
      error: null,

      // Populated from GET /api/admin/dashboard — see fetchDashboard()
      topStats: [],
      financeStats: [],
      occupancy: {
        occupiedPct: 0,
        vacantPct: 0,
      },
      roomTypes: [],
      revenueChart: [],
      expiringContracts: [],
      topOverdue: [],
    }
  },
  created() {
    this.fetchDashboard()
  },
  computed: {
    donutCircumference() {
      const r = 55
      return 2 * Math.PI * r
    },
    donutOccupiedDash() {
      const total = this.donutCircumference
      const occupiedLength = (this.occupancy.occupiedPct / 100) * total
      return `${occupiedLength} ${total}`
    },
    donutVacantDash() {
      const total = this.donutCircumference
      const vacantLength = (this.occupancy.vacantPct / 100) * total
      return `${vacantLength} ${total}`
    },
    donutOccupiedLength() {
      return (this.occupancy.occupiedPct / 100) * this.donutCircumference
    },
    chartMaxValue() {
      if (!this.revenueChart.length) return 1
      return Math.max(...this.revenueChart.map((i) => i.value), 1)
    },
    chartTicks() {
      // 5 evenly spaced ticks from 0 to a "nice" rounded max
      const niceMax = Math.ceil(this.chartMaxValue / 100000000) * 100000000
      const steps = 4
      const ticks = []
      for (let i = steps; i >= 0; i--) {
        ticks.push((niceMax / steps) * i)
      }
      return ticks
    },
  },
  methods: {
    async fetchDashboard() {
      this.loading = true
      this.error = null

      try {
        // Express route: GET /api/admin/dashboard -> { success, data: {...} }
        const response = await this.$axios.get('/admin/dashboard')
        const data = response.data.data

        this.topStats = [
          { label: 'ຈຳນວນຫ້ອງທັງໝົດ', value: String(data.totalApartments ?? 0) },
          {
            label: 'ຈຳນວນຫ້ອງທີ່ຍັງວ່າງ',
            value: String(data.availableApartments ?? 0),
          },
          {
            label: 'ຈຳນວນຫ້ອງທີ່ຍັງບໍ່ທັນຈ່າຍເງິນ',
            value: String(data.notPaidApartments ?? 0),
          },
          {
            label: 'ຈຳນວນຫ້ອງທີ່ຈ່າຍແລ້ວ',
            value: String(data.paidApartments ?? 0),
          },
        ]

        this.financeStats = [
          {
            label: 'ລາຍໄດ້ເດືອນນີ້',
            value: this.formatCurrency(data.incomeThisMonth),
          },
          {
            label: 'ຍອດຄ້າງຊຳລະທັງໝົດ',
            value: this.formatCurrency(data.outstanding),
          },
          { label: 'ຈຳນວນຜູ້ເຊົ່າທັງໝົດ', value: String(data.totalTenants ?? 0) },
        ]

        // occupancyRate is a single % from the API; derive the vacant half.
        const occupiedPct = data.occupancyRate ?? 0
        this.occupancy = {
          occupiedPct,
          vacantPct: Math.round((100 - occupiedPct) * 10) / 10,
        }

        // roomTypeBreakdown only gives raw counts per type (no per-type
        // occupied/total split), so this panel shows a simple count
        // comparison rather than the "current/total" fraction from the mockup.
        const breakdown = data.roomTypeBreakdown || {}
        this.roomTypes = [
          { label: 'ຫ້ອງນ້ອຍ', count: breakdown.type0 ?? 0 },
          { label: 'ຫ້ອງໃຫຍ່', count: breakdown.type1 ?? 0 },
        ]

        // incomeHistory: [{ month: 'YYYY-MM', income }]
        this.revenueChart = (data.incomeHistory || []).map((row) => ({
          month: this.formatMonthLabel(row.month),
          value: row.income || 0,
        }))

        // expiringLeases: [{ roomId, name, startDate, endDate, daysLeft }]
        this.expiringContracts = (data.expiringLeases || []).map((row) => ({
          room: row.name,
          endDate: row.endDate,
          daysLeft: row.daysLeft,
        }))

        // topOverdue: [{ roomId, name, overdue }] — overdue is in MONTHS,
        // not a LAK amount (the backend doesn't join a bill total here).
        this.topOverdue = (data.topOverdue || []).map((row) => ({
          room: row.name,
          overdueMonths: row.overdue,
        }))
      } catch (err) {
        this.error = err?.message || 'Unknown error'
      } finally {
        this.loading = false
      }
    },

    formatMonthLabel(yyyyMm) {
      if (!yyyyMm) return ''
      const date = new Date(`${yyyyMm}-01`)
      return date.toLocaleString('en', { month: 'short' })
    },

    roomTypePercent(type) {
      const maxCount = Math.max(...this.roomTypes.map((t) => t.count), 1)
      return Math.min(100, (type.count / maxCount) * 100)
    },
    barHeightPercent(value) {
      const niceMax = this.chartTicks[0] || this.chartMaxValue
      if (!niceMax) return 0
      return Math.min(100, (value / niceMax) * 100)
    },
    gridLinePosition(value) {
      const niceMax = this.chartTicks[0] || this.chartMaxValue
      if (!niceMax) return 100
      return 100 - Math.min(100, (value / niceMax) * 100)
    },
    formatAxisTick(value) {
      return `${Number(value).toLocaleString()} LAK`
    },
    formatCurrency(value) {
      return `${Number(value || 0).toLocaleString()} LAK`
    },
  },
}
</script>

<style scoped>
.dashboard-page {
  padding: 24px;
}

.page-title {
  font-size: 20px;
  font-weight: 700;
  color: #111827;
  margin: 0 0 20px;
}

.state-wrap {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  padding: 60px 0;
  color: #6b7280;
}

.stat-row {
  margin-bottom: 16px;
  gap: 0 0 18;
}

.stat-col {
  padding: 0 8px !important;
}

.stat-card {
  padding: 18px 20px;
  border-radius: 10px;
  height: 100%;
}

.stat-label {
  font-size: 13px;
  color: #6b7280;
  margin: 0 0 8px;
}

.stat-value {
  font-size: 24px;
  font-weight: 700;
  color: #111827;
  margin: 0;
}

.panel-card {
  padding: 20px;
  border-radius: 10px;
  height: 100%;
}

.panel-title {
  font-size: 14px;
  font-weight: 700;
  color: #111827;
  margin: 0 0 16px;
}

/* Donut chart */
.donut-wrap {
  display: flex;
  align-items: center;
  gap: 24px;
  flex-wrap: wrap;
}

.donut-svg {
  width: 140px;
  height: 140px;
  flex-shrink: 0;
}

.donut-legend {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.legend-line {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 13px;
  color: #374151;
}

.legend-pct {
  font-weight: 700;
  color: #111827;
  margin-left: 4px;
}

.dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  display: inline-block;
}

.dot-occupied {
  background: #1e3a5f;
}

.dot-vacant {
  background: #8fd19e;
}

/* Room type progress bars */
.room-type-row {
  margin-bottom: 18px;
}

.room-type-row:last-child {
  margin-bottom: 0;
}

.room-type-header {
  display: flex;
  justify-content: space-between;
  font-size: 13px;
  color: #374151;
  margin-bottom: 6px;
}

.progress-track {
  background: #e5e7eb;
  border-radius: 6px;
  height: 10px;
  overflow: hidden;
}

.progress-fill {
  background: #1e3a5f;
  height: 100%;
  border-radius: 6px;
}

/* Bar chart */
.chart-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 16px;
}

.range-badge {
  background: #1e3a5f;
  color: #fff;
  font-size: 12px;
  padding: 4px 12px;
  border-radius: 14px;
}

.bar-chart {
  display: flex;
  gap: 12px;
}

.bar-chart-axis {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  font-size: 11px;
  color: #6b7280;
  height: 220px;
  text-align: right;
  min-width: 90px;
}

.bar-chart-bars {
  flex: 1;
  position: relative;
  display: flex;
  align-items: flex-end;
  justify-content: space-around;
  height: 220px;
  border-left: 1px solid #e5e7eb;
  border-bottom: 1px solid #e5e7eb;
  padding: 0 8px;
}

.chart-grid {
  position: absolute;
  inset: 0 0 0 0;
  pointer-events: none;
}

.chart-grid-line {
  position: absolute;
  left: 0;
  right: 0;
  border-top: 1px dashed #d9dee7;
}

.bar-col {
  position: relative;
  z-index: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-end;
  height: 100%;
  width: 40px;
}

.bar-track {
  width: 28px;
  height: 100%;
  display: flex;
  align-items: flex-end;
}

.bar-fill {
  width: 100%;
  background: #1e3a5f;
  border-radius: 3px 3px 0 0;
}

.bar-month {
  margin-top: 8px;
  font-size: 12px;
  color: #6b7280;
}

/* Tables */
.section-heading {
  font-size: 14px;
  font-weight: 700;
  color: #111827;
  margin: 0 0 10px;
}

.table-card {
  border-radius: 10px;
  overflow: hidden;
}

.dash-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 13px;
}

.dash-table thead th {
  background: #1e3a5f;
  color: #fff;
  text-align: left;
  padding: 10px 16px;
  font-weight: 600;
}

.dash-table tbody td {
  padding: 12px 16px;
  border-bottom: 1px solid #f1f1f1;
  color: #374151;
}

.align-right {
  text-align: right;
}

.days-badge {
  background: #dc2626;
  color: #fff;
  font-size: 12px;
  padding: 3px 10px;
  border-radius: 12px;
}

.empty-row {
  text-align: center;
  padding: 20px;
  color: #9ca3af;
}
</style>