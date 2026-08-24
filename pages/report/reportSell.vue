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
          <v-btn color="primary" @click="newCustomer">
            <v-icon left small>mdi-account-plus</v-icon>
            ເພີ່ມລູກຄ້າ
          </v-btn>
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
          {{ formatCurrency(item.payment) }}
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
  </div>
</template>

<script>
export default {
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
          payment: 2500000,
          details: 'ເຊົ່າ 1 ອາທິດ',
        },
        {
          room: 'A102',
          lessee: 'ທ່ານ ສົມສະຫຼີ',
          leaseContract: 'LC-002',
          payment: 3200000,
          details: 'ເຊົ່າ 2 ອາທິດ',
        },
      ],
    }
  },
  methods: {
    formatCurrency(value) {
      return `${Number(value).toLocaleString()} ₭`
    },

    newCustomer() {
      alert('ເພີ່ມລູກຄ້າ')
    },

    viewDetail(item) {
      alert(`ລາຍລະອຽດ: ${item.room} - ${item.details}`)
    },

    openManage(item) {
      alert(`ຈັດການ: ${item.room}`)
    },

    calculate(item) {
      alert(`ຄິດໄລ່: ${item.room} - ${this.formatCurrency(item.payment)}`)
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
</style>