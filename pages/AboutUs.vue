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
          <v-btn color="#064D8D" dark class="white--text" @click="openCreate">
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
      <v-card class="rounded-lg overflow-hidden pb-4">
      <!-- Header Bar -->
      <v-card-title
        class="white--text d-flex justify-space-between align-center px-6 py-3"
        style="background-color: #064d8d"
      >
        <span class="text-subtitle-1 font-weight-medium">ເພີ່ມ / ແກ້ໄຂ About Us</span>
        <v-btn icon dark small @click="close">
          <v-icon size="18">mdi-close</v-icon>
        </v-btn>
      </v-card-title>

      <!-- Form Content -->
      <v-card-text class="pt-6 px-6">
        <v-container class="pa-0">
          <!-- Image Upload Area -->

          <!-- Form Inputs -->
          <v-row class="mt-4" dense>
            <!-- 1. Banner Title & Link -->
            <v-col cols="12" sm="6" class="pr-sm-2">
              <div class="field-label">ເບີໂທ</div>
              <v-text-field
                v-model="form.phoneNumber"
                dense
                outlined
                hide-details
                placeholder="ເບີໂທ"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <v-col cols="12" sm="6" class="pl-sm-2">
              <div class="field-label">Email</div>
              <v-text-field
                v-model="form.email"
                dense
                outlined
                hide-details
                type="email"
                placeholder="Email"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <!-- 2. Display Order & Status -->
            <v-col cols="12" sm="6" class="mt-3 pr-sm-2">
              <div class="field-label">Map Link</div>
              <v-text-field
                v-model="form.mapLink"
                dense
                outlined
                hide-details
                type="url"
                placeholder="Map Link"
                class="custom-input"
              ></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" class="mt-3 pl-sm-2">
              <div class="field-label">ທີ່ຢູ່</div>
              <v-text-field
                v-model="form.address"
                dense
                outlined
                hide-details
                type="text"
                placeholder="ທີ່ຢູ່"
                class="custom-input"
              ></v-text-field>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>

      <!-- Action Footer -->
      <v-card-actions class="px-6 pt-2 pb-2">
        <v-spacer></v-spacer>
        <v-btn
          color="#064D8D"
          dark
          depressed
          class="px-8 rounded-lg text-none font-weight-regular"
          @click="save"
        >
          ບັນທຶກ
        </v-btn>
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
  data() {
    return {
      search: '',
      dialog: false,
      dialogDelete: false,
      form: this.emptyForm(),
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
    };
  },

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'ເພີ່ມ About Us' : 'ແກ້ໄຂ About Us'
    },
  },

  created() {
    this.initialize()
  },

  methods: {
    emptyForm() {
      return {
        phoneNumber: '',
        email: '',
        address: '',
        mapLink: '',
      };
    },

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