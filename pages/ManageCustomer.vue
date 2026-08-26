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
        :loading="loading"
        @edit-item="openEdit"
        @delete-item="openDelete"
        @reset="initialize"
      />

      <v-alert v-if="errorMessage" type="error" dense text class="mt-3">
        {{ errorMessage }}
      </v-alert>
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
    loading: false,
    errorMessage: '',
    dialogUpdate: false,
    dialogDelete: false,
    editedItem: {},
  }),
  created() {
    this.initialize()
  },
  methods: {
    async initialize() {
      this.loading = true
      this.errorMessage = ''

      try {
        const { data } = await this.$axios.get('/customers')
        this.customers = Array.isArray(data)
          ? data
          : data.data || data.customers || []
      } catch (error) {
        this.errorMessage = 'ບໍ່ສາມາດໂຫລດຂໍ້ມູນລູກຄ້າໄດ້'
        console.error('GET /customers error:', error)
      } finally {
        this.loading = false
      }
    },

    addCustomer(item) {
      this.customers.push({ id: Date.now(), ...item })
    },

    openEdit(item) {
      this.editedItem = { ...item }
      this.dialogUpdate = true
    },

    async updateCustomer(item) {
      const index = this.customers.findIndex((c) => c.id === item.id)
      if (index === -1 || !item.id) return

      try {
        const { data } = await this.$axios.put(`/customers/${item.id}`, item)
        const updatedCustomer = data.data || data.customer || data
        this.customers.splice(index, 1, updatedCustomer)
      } catch (error) {
        this.errorMessage = 'ບໍ່ສາມາດແກ້ໄຂຂໍ້ມູນລູກຄ້າໄດ້'
        console.error('PUT /customers/:id error:', error)
      }
    },

    openDelete(item) {
      this.editedItem = { ...item }
      this.dialogDelete = true
    },

    async removeCustomer(item) {
      const index = this.customers.findIndex((c) => c.id === item.id)
      if (index === -1 || !item.id) return

      try {
        await this.$axios.delete(`/customers/${item.id}`)
        this.customers.splice(index, 1)
      } catch (error) {
        this.errorMessage = 'ບໍ່ສາມາດລົບຂໍ້ມູນລູກຄ້າໄດ້'
        console.error('DELETE /customers/:id error:', error)
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