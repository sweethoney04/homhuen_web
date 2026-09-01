<template>
  <div>
    <h2 class="page-title mb-4">ຈັດການພະນັກງານ</h2>

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
          />
        </v-col>

        <v-spacer />

        <v-col cols="auto">
          <ManageUsersCreate @created="addUser" />
        </v-col>
      </v-row>

      <v-data-table
        :headers="headers"
        :items="users"
        :search="search"
        :loading="loading"
        class="user-table"
      >
        <template v-slot:item.no="{ item }">
          {{ users.indexOf(item) + 1 }}
        </template>

        <template v-slot:item.status="{ item }">
          <v-chip
            :color="item.status ? 'success' : 'grey lighten-1'"
            :text-color="item.status ? 'white' : 'black'"
            small
            label
          >
            {{ item.status ? 'Active' : 'Inactive' }}
          </v-chip>
        </template>

        <template v-slot:item.edit="{ item }">
          <v-btn icon small color="primary" @click="openEdit(item)">
            <v-icon small>mdi-pencil</v-icon>
          </v-btn>
        </template>

        <template v-slot:item.delete="{ item }">
          <v-btn icon small color="error" @click="openDelete(item)">
            <v-icon small>mdi-delete</v-icon>
          </v-btn>
        </template>

        <template v-slot:no-data>
          <v-alert type="info" text>
            ບໍ່ມີຂໍ້ມູນພະນັກງານ
          </v-alert>
        </template>
      </v-data-table>

      <v-alert v-if="errorMessage" type="error" dense text class="mt-3">
        {{ errorMessage }}
      </v-alert>
    </v-card>

    <ManageUsersUpdate
      v-model="dialog"
      :item="editedItem"
      @updated="updateUser"
    />

    <ManageUsersDelete
      v-model="dialogDelete"
      :item="editedItem"
      @deleted="removeUser"
    />
  </div>
</template>

<script>
export default {
  data: () => ({
    search: '',
    dialog: false,
    dialogDelete: false,
    editedIndex: -1,
    loading: false,
    errorMessage: '',
    editedItem: {
      username: '',
      phone: '',
      password: '',
      status: true,
    },
    defaultItem: {
      username: '',
      phone: '',
      password: '',
      status: true,
    },
    headers: [
      { text: 'ລຳດັບ', value: 'no', sortable: false, width: '70' },
      { text: 'ເບີໂທ', value: 'phone', sortable: false },
      { text: 'ສະຖານະ', value: 'status', sortable: false, align: 'center' },
      { text: 'ແກ້ໄຂ', value: 'edit', sortable: false, align: 'center' },
      { text: 'ລົບ', value: 'delete', sortable: false, align: 'center' },
    ],
    users: [],
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'ເພີ່ມພະນັກງານ' : 'ແກ້ໄຂພະນັກງານ'
    },
  },

  created() {
    this.initialize()
  },

  methods: {
    async initialize() {
      this.loading = true
      this.errorMessage = ''

      try {
        const { data } = await this.$axios.get('/admin/users')
        const users = Array.isArray(data) ? data : data.data || data.users || []
        this.users = users.map((user) => this.normalizeUser(user))
      } catch (error) {
        this.errorMessage = 'ບໍ່ສາມາດໂຫລດຂໍ້ມູນພະນັກງານໄດ້'
        console.error('GET /admin/users error:', error)
      } finally {
        this.loading = false
      }
    },

    openCreate() {
      this.editedIndex = -1
      this.editedItem = { ...this.defaultItem }
      this.dialog = true
    },

    async addUser(user) {
      try {
        const { data } = await this.$axios.post(
          '/admin/users',
          this.userPayload(user)
        )
        const createdUser = data.data || data.user || data
        this.users.push(this.normalizeUser(createdUser, user))
      } catch (error) {
        this.errorMessage = 'ບໍ່ສາມາດເພີ່ມພະນັກງານໄດ້'
        console.error('POST /admin/users error:', error)
      }
    },

    openEdit(item) {
      this.editedIndex = this.users.indexOf(item)
      this.editedItem = { ...item }
      this.dialog = true
    },

    openDelete(item) {
      this.editedIndex = this.users.indexOf(item)
      this.editedItem = { ...item }
      this.dialogDelete = true
    },

    async removeUser(user) {
      const index = this.users.indexOf(user)
      if (index === -1 || !user.id) return

      try {
        await this.$axios.delete(`/admin/users/${user.id}`)
        this.users.splice(index, 1)
      } catch (error) {
        this.errorMessage = 'ບໍ່ສາມາດລົບພະນັກງານໄດ້'
        console.error('DELETE /admin/users/:id error:', error)
      }
      this.closeDelete()
    },

    async updateUser(user) {
      const index = this.editedIndex
      if (index === -1 || !user.id) return

      try {
        const { data } = await this.$axios.put(
          `/admin/users/${user.id}`,
          this.userPayload(user)
        )
        const updatedUser = data.data || data.user || data
        this.users.splice(index, 1, this.normalizeUser(updatedUser, user))
      } catch (error) {
        this.errorMessage = 'ບໍ່ສາມາດແກ້ໄຂພະນັກງານໄດ້'
        console.error('PUT /admin/users/:id error:', error)
      }
      this.close()
    },

    close() {
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = { ...this.defaultItem }
        this.editedIndex = -1
      })
    },

    closeDelete() {
      this.dialogDelete = false
      this.$nextTick(() => {
        this.editedItem = { ...this.defaultItem }
        this.editedIndex = -1
      })
    },

    normalizeUser(user, fallback = {}) {
      return {
        ...fallback,
        ...user,
        username: user.username || user.userName || user.name || fallback.username || '',
        phone: user.phone || user.phoneNumber || fallback.phone || '',
        password: user.password || fallback.password || '',
        status: user.status !== undefined ? user.status : fallback.status !== false,
      }
    },

    userPayload(user) {
      return {
        ...user,
        username: user.username,
        phone: user.phone,
        password: user.password,
      }
    },
  },
}
</script>

<style scoped>
.page-title {
  font-weight: 600;
}

.user-table >>> thead tr th {
  background-color: #064d8d !important;
  color: #ffffff !important;
}
</style>