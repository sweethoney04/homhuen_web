<template>
  <div>
    <h2 class="page-title mb-4">ຈັດການໜ້າ About Us</h2>

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
          <v-btn color="primary" dark @click="openCreate">
            ເພີ່ມ
          </v-btn>
        </v-col>
      </v-row>

      <v-data-table
        :headers="headers"
        :items="aboutItems"
        :search="search"
        class="about-table"
      >
        <template v-slot:item.no="{ item }">
          {{ aboutItems.indexOf(item) + 1 }}
        </template>

        <template v-slot:item.mapLink="{ item }">
          <a :href="item.mapLink" target="_blank" class="link-text">{{ item.mapLink }}</a>
        </template>

        <template v-slot:item.edit="{ item }">
          <v-btn icon small color="primary" @click="editItem(item)">
            <v-icon small>mdi-pencil</v-icon>
          </v-btn>
        </template>

        <template v-slot:item.delete="{ item }">
          <v-btn icon small color="error" @click="deleteItem(item)">
            <v-icon small>mdi-delete</v-icon>
          </v-btn>
        </template>

        <template v-slot:no-data>
          <v-alert type="info" text>
            ບໍ່ມີຂໍ້ມູນ About Us
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
                  v-model="editedItem.phoneNumber"
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
                <v-text-field
                  v-model="editedItem.address"
                  label="Address"
                />
              </v-col>

              <v-col cols="12">
                <v-text-field
                  v-model="editedItem.mapLink"
                  label="Map Link"
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
    headers: [
      { text: 'ລຳດັບ', value: 'no', sortable: false, width: '70' },
      { text: 'ເບີໂທ', value: 'phoneNumber', sortable: false },
      { text: 'Email', value: 'email', sortable: false },
      { text: 'Address', value: 'address', sortable: false },
      { text: 'Map Link', value: 'mapLink', sortable: false },
      { text: 'ແກ້ໄຂ', value: 'edit', sortable: false, align: 'center' },
      { text: 'ລົບ', value: 'delete', sortable: false, align: 'center' },
    ],
    aboutItems: [],
    editedIndex: -1,
    editedItem: {
      phoneNumber: '',
      email: '',
      address: '',
      mapLink: '',
    },
    defaultItem: {
      phoneNumber: '',
      email: '',
      address: '',
      mapLink: '',
    },
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'ເພີ່ມ About Us' : 'ແກ້ໄຂ About Us'
    },
  },

  created() {
    this.initialize()
  },

  methods: {
    initialize() {
      this.aboutItems = [
        {
          phoneNumber: '020 123 4567',
          email: 'info@homhuen.com',
          address: 'ບ້ານໂພນສີນວນ, ເມືອງສີສັດຕະນາກ, ນະຄອນຫຼວງວຽງຈັນ',
          mapLink: 'https://maps.google.com/?q=Vientiane',
        },
        {
          phoneNumber: '020 987 6543',
          email: 'support@homhuen.com',
          address: 'ບ້ານຫັດສະດີ, ເມືອງຈັນທະບູລິ, ນະຄອນຫຼວງວຽງຈັນ',
          mapLink: 'https://maps.google.com/?q=Vientiane',
        },
      ]
    },

    openCreate() {
      this.editedIndex = -1
      this.editedItem = { ...this.defaultItem }
      this.dialog = true
    },

    editItem(item) {
      this.editedIndex = this.aboutItems.indexOf(item)
      this.editedItem = { ...item }
      this.dialog = true
    },

    deleteItem(item) {
      this.editedIndex = this.aboutItems.indexOf(item)
      this.editedItem = { ...item }
      this.dialogDelete = true
    },

    deleteItemConfirm() {
      this.aboutItems.splice(this.editedIndex, 1)
      this.closeDelete()
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

    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.aboutItems[this.editedIndex], this.editedItem)
      } else {
        this.aboutItems.push({ ...this.editedItem })
      }
      this.close()
    },
  },
}
</script>

<style scoped>
.page-title {
  font-weight: 600;
}

.link-text {
  color: #064d8d;
  text-decoration: none;
}

.link-text:hover {
  text-decoration: underline;
}

.about-table >>> thead tr th {
  background-color: #064d8d !important;
  color: #ffffff !important;
}
</style>