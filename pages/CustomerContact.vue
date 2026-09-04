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
        :loading="loading"
        class="contact-table"
      >
        <template v-slot:item.no="{ item }">
          {{ contacts.indexOf(item) + 1 }}
        </template>

        <template v-slot:item.create_date="{ item }">
          {{ formatDate(item.create_date) }}
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
          <v-btn color="error" text :loading="deleting" @click="deleteItemConfirm">OK</v-btn>
          <v-spacer></v-spacer>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-snackbar v-model="snackbar.show" :color="snackbar.color" timeout="3000">
      {{ snackbar.text }}
    </v-snackbar>
  </div>
</template>

<script>
const API_PATH = "/admin/contact-messages";

export default {
  data: () => ({
    search: "",
    dialogDelete: false,
    loading: false,
    deleting: false,
    headers: [
      { text: "ລຳດັບ", value: "no", sortable: false, width: "70" },
      { text: "ຊື່ລູກຄ້າ", value: "name", sortable: false },
      { text: "ເບີໂທ", value: "phone", sortable: false },
      { text: "ຫົວຂໍ້", value: "topic", sortable: false },
      { text: "ລາຍລະອຽດ", value: "detail", sortable: false },
      { text: "ວັນທີ", value: "create_date", sortable: true },
      { text: "ລົບ", value: "delete", sortable: false, align: "center" },
    ],
    contacts: [],
    editedIndex: -1,
    editedItem: null,
    snackbar: { show: false, text: "", color: "success" },
  }),

  created() {
    this.initialize();
  },

  methods: {
    async initialize() {
      this.loading = true;
      try {
        const response = await this.$axios.$get(API_PATH);
        this.contacts = response.data || [];
      } catch (err) {
        this.notify("ໂຫລດຂໍ້ມູນບໍ່ສຳເລັດ", "error");
        console.error(err);
      } finally {
        this.loading = false;
      }
    },

    formatDate(value) {
      if (!value) return "";
      const d = new Date(value);
      return isNaN(d) ? value : d.toLocaleString("lo-LA");
    },

    deleteItem(item) {
      this.editedIndex = this.contacts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },

    async deleteItemConfirm() {
      this.deleting = true;
      try {
        await this.$axios.$delete(`${API_PATH}/${this.editedItem.id}`);
        this.contacts.splice(this.editedIndex, 1);
        this.notify("ລົບຂໍ້ມູນສຳເລັດ", "success");
      } catch (err) {
        this.notify("ລົບຂໍ້ມູນບໍ່ສຳເລັດ", "error");
        console.error(err);
      } finally {
        this.deleting = false;
        this.closeDelete();
      }
    },

    closeDelete() {
      this.dialogDelete = false;
      this.editedItem = null;
      this.editedIndex = -1;
    },

    notify(text, color) {
      this.snackbar = { show: true, text, color };
    },
  },
};
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