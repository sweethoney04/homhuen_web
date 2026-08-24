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
          <v-btn color="primary" @click="openCreate">+ ເພີ່ມ</v-btn>
        </v-col>
      </v-row>

      <v-data-table
        :headers="headers"
        :items="users"
        :search="search"
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
            {{ item.status ? 'ເປີດ' : 'ປິດ' }}
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
    </v-card>

    <v-dialog v-model="dialog" max-width="500px">
      <v-card>
        <v-card-title class="text-h6">
          {{ formTitle }}
        </v-card-title>

        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-text-field
                  v-model="editedItem.fullName"
                  label="ຊື່-ນາມສະກຸນ"
                />
              </v-col>

              <v-col cols="12">
                <v-text-field
                  v-model="editedItem.position"
                  label="ຕໍາແໜ່ງ"
                />
              </v-col>

              <v-col cols="12">
                <v-text-field
                  v-model="editedItem.phone"
                  label="ເບີໂທ"
                />
              </v-col>

              <v-col cols="12">
                <v-text-field
                  v-model="editedItem.email"
                  label="Email"
                />
              </v-col>

              <v-col cols="12">
                <v-switch
                  v-model="editedItem.status"
                  inset
                  color="success"
                  hide-details
                  :label="editedItem.status ? 'ເປີດ' : 'ປິດ'"
                />
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>

        <v-card-actions>
          <v-spacer />
          <v-btn text @click="close">Cancel</v-btn>
          <v-btn color="primary" text @click="save">Save</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="dialogDelete" max-width="420px">
      <v-card>
        <v-card-title class="text-h6">
          ທ່ານແນ່ໃຈບໍ່ວ່າຈະລົບຂໍ້ມູນນີ້?
        </v-card-title>

        <v-card-actions>
          <v-spacer />
          <v-btn text @click="closeDelete">Cancel</v-btn>
          <v-btn color="error" text @click="deleteItemConfirm">OK</v-btn>
          <v-spacer />
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
  data: () => ({
    search: '',
    dialog: false,
    dialogDelete: false,
    editedIndex: -1,
    editedItem: {
      fullName: '',
      position: '',
      phone: '',
      email: '',
      status: true,
    },
    defaultItem: {
      fullName: '',
      position: '',
      phone: '',
      email: '',
      status: true,
    },
    headers: [
      { text: 'ລຳດັບ', value: 'no', sortable: false, width: '70' },
      { text: 'ຊື່-ນາມສະກຸນ', value: 'fullName', sortable: false },
      { text: 'ຕໍາແໜ່ງ', value: 'position', sortable: false },
      { text: 'ເບີໂທ', value: 'phone', sortable: false },
      { text: 'Email', value: 'email', sortable: false },
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
    initialize() {
      this.users = [
        {
          fullName: 'ນາງ ວິນດາ',
          position: 'Admin',
          phone: '020 111 2222',
          email: 'admin@homhuen.com',
          status: true,
        },
        {
          fullName: 'ທ່ານ ຄຳສົມ',
          position: 'Manager',
          phone: '020 333 4444',
          email: 'manager@homhuen.com',
          status: true,
        },
        {
          fullName: 'ນາງ ໄຊຍາວ',
          position: 'Staff',
          phone: '020 555 6666',
          email: 'staff@homhuen.com',
          status: false,
        },
      ]
    },

    openCreate() {
      this.editedIndex = -1
      this.editedItem = { ...this.defaultItem }
      this.dialog = true
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

    deleteItemConfirm() {
      this.users.splice(this.editedIndex, 1)
      this.closeDelete()
    },

    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.users[this.editedIndex], this.editedItem)
      } else {
        this.users.push({ ...this.editedItem })
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