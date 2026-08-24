<template>
  <div>
    <h2 class="page-title mb-4">ຈັດການຂໍ້ມູນຕິດຕໍ່</h2>

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
      </v-row>

      <v-data-table
        :headers="headers"
        :items="contacts"
        :search="search"
        class="contact-table"
      >
        <template v-slot:item.no="{ item }">
          {{ contacts.indexOf(item) + 1 }}
        </template>

        <template v-slot:item.delete="{ item }">
          <v-btn
            icon
            small
            color="error"
            @click="deleteItem(item)"
          >
            <v-icon small>mdi-delete</v-icon>
          </v-btn>
        </template>

        <template v-slot:no-data>
          <v-alert type="info" text>
            ບໍ່ມີຂໍ້ມູນຕິດຕໍ່
          </v-alert>
        </template>
      </v-data-table>
    </v-card>

    <v-dialog v-model="dialogDelete" max-width="420px">
      <v-card>
        <v-card-title class="text-h6">
          ທ່ານແນ່ໃຈບໍ່ວ່າຈະລົບຂໍ້ມູນນີ້?
        </v-card-title>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn text @click="closeDelete">Cancel</v-btn>
          <v-btn color="error" text @click="deleteItemConfirm">OK</v-btn>
          <v-spacer></v-spacer>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
  data: () => ({
    search: '',
    dialogDelete: false,
    headers: [
      { text: 'ລຳດັບ', value: 'no', sortable: false, width: '70' },
      { text: 'ຊື່ລູກຄ້າ', value: 'customerName', sortable: false },
      { text: 'ເບີໂທ', value: 'phoneNumber', sortable: false },
      { text: 'ຫົວຂໍ້', value: 'topic', sortable: false },
      { text: 'ລາຍລະອຽດ', value: 'details', sortable: false },
      { text: 'ລົບ', value: 'delete', sortable: false, align: 'center' },
    ],
    contacts: [],
    editedIndex: -1,
    editedItem: null,
  }),

  created() {
    this.initialize()
  },

  methods: {
    initialize() {
      // Replace this with API call later:
      // const { data } = await this.$axios.get('/customer-contacts')
      // this.contacts = data

      this.contacts = [
        {
          customerName: 'ນາງ ສຸລິຍະ',
          phoneNumber: '020 123 4567',
          topic: 'ສອບຖາມ',
          details: 'ລາຄາເທົ່າໃດ',
        },
        {
          customerName: 'ທ່ານ ສົມສະຫຼີ',
          phoneNumber: '020 987 6543',
          topic: 'ສອບຖາມ',
          details: 'ຜ່ອນເລີ່ມຕົ້ນເທົ່າໃດ?',
        },
      ]
    },

    deleteItem(item) {
      this.editedIndex = this.contacts.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialogDelete = true
    },

    deleteItemConfirm() {
      this.contacts.splice(this.editedIndex, 1)
      this.closeDelete()
    },

    closeDelete() {
      this.dialogDelete = false
      this.editedItem = null
      this.editedIndex = -1
    },
  },
}
</script>

<style scoped>
.page-title {
  font-weight: 600;
}

.contact-table >>> thead tr th {
  background-color: #064d8d !important;
  color: #ffffff !important;
}
</style>