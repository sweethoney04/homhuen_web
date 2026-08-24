<template>
  <div>
    <h2 class="page-title mb-4">ຈັດການລູກຄ້າ</h2>

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
          <CustomerCreate @created="addCustomer" />
        </v-col>
      </v-row>

      <CustomerView
        :customers="customers"
        :search="search"
        @edit-item="openEdit"
        @delete-item="openDelete"
        @reset="initialize"
      />
    </v-card>

    <CustomerUpdate
      v-model="dialogUpdate"
      :item="editedItem"
      @updated="updateCustomer"
    />

    <CustomerDelete
      v-model="dialogDelete"
      :item="editedItem"
      @deleted="removeCustomer"
    />
  </div>
</template>

<script>
import CustomerView from '~/components/Manage-Customer/View.vue'
import CustomerCreate from '~/components/Manage-Customer/Create.vue'
import CustomerUpdate from '~/components/Manage-Customer/Update.vue'
import CustomerDelete from '~/components/Manage-Customer/Delete.vue'

export default {
  name: 'CustomerManagementPage',
  components: {
    CustomerCreate,
    CustomerView,
    CustomerUpdate,
    CustomerDelete,
  },
  data: () => ({
    search: '',
    customers: [],
    dialogUpdate: false,
    dialogDelete: false,
    editedItem: {},
  }),
  created() {
    this.initialize()
  },
  methods: {
    initialize() {
      // TODO: ປ່ຽນເປັນການດຶງຂໍ້ມູນຈິງຈາກ API, ຕົວຢ່າງ:
      // const { data } = await this.$axios.get('/customers')
      // this.customers = data
      this.customers = [
        { id: 1, customerName: 'ນາງ ລັດດາວັນ', interestedRoom: 'ຫ້ອງ A01', phoneNumber: '020xxxxxxx', detail: 'ຖາມລາຄາ', contactDate: '12/12/2026', responsible: 'ພອນຄຳ', status: 'ກຳລັງຕິດຕໍ່' },
        { id: 2, customerName: 'ນາງ ເອມີລີ່', interestedRoom: 'ຫ້ອງ A02', phoneNumber: '020xxxxxxx', detail: 'ຢາກຮູ້ລາຄາຜ່ອນ', contactDate: '12/12/2026', responsible: 'ແກ້ວ', status: 'ໃໝ່' },
        { id: 3, customerName: 'ນາງ ເຈມນີ່', interestedRoom: 'ຫ້ອງ A03', phoneNumber: '020xxxxxxx', detail: 'ຜ່ອນຈົນຈົບ', contactDate: '12/12/2026', responsible: 'ຕົ້ນທອງ', status: 'ປິດການຂາຍແລ້ວ' },
        { id: 4, customerName: 'ນາງ ສອນມະນີ', interestedRoom: 'ຫ້ອງ A04', phoneNumber: '020xxxxxxx', detail: 'ຈ່າຍຄົບ', contactDate: '12/12/2026', responsible: 'ແກ້ວ', status: 'ກຳລັງຕິດຕໍ່' },
        { id: 5, customerName: 'ນາງ ເພັດ', interestedRoom: 'ຫ້ອງ A05', phoneNumber: '020xxxxxxx', detail: 'ຈ່າຍງວດໄດ້', contactDate: '12/12/2026', responsible: 'ພອນຄຳ', status: 'ໃໝ່' },
      ]
    },

    addCustomer(item) {
      this.customers.push({ id: Date.now(), ...item })
    },

    openEdit(item) {
      this.editedItem = { ...item }
      this.dialogUpdate = true
    },

    updateCustomer(item) {
      const index = this.customers.findIndex((c) => c.id === item.id)
      if (index > -1) {
        this.customers.splice(index, 1, item)
      }
    },

    openDelete(item) {
      this.editedItem = { ...item }
      this.dialogDelete = true
    },

    removeCustomer(item) {
      const index = this.customers.findIndex((c) => c.id === item.id)
      if (index > -1) {
        this.customers.splice(index, 1)
      }
    },
  },
}
</script>

<style scoped>
.page-title {
  font-weight: 600;
}
</style>