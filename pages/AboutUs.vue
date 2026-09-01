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
          <!-- Backend only ever stores ONE contact record (singleton).
               Hide "ເພີ່ມ" once a record already exists, so users don't
               trigger a 409 from the backend. -->
          <v-btn
            v-if="aboutItems.length === 0"
            color="#064D8D"
            dark
            class="white--text"
            @click="openCreate"
          >
            ເພີ່ມ
          </v-btn>
        </v-col>
      </v-row>

      <v-data-table
        :headers="headers"
        :items="aboutItems"
        :search="search"
        :loading="loading"
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
        <span class="text-subtitle-1 font-weight-medium">{{ formTitle }}</span>
        <v-btn icon dark small @click="close">
          <v-icon size="18">mdi-close</v-icon>
        </v-btn>
      </v-card-title>

      <!-- Form Content -->
      <v-card-text class="pt-6 px-6">
        <v-alert v-if="formError" type="error" dense text class="mb-4">
          {{ formError }}
        </v-alert>

        <v-container class="pa-0">
          <v-row class="mt-4" dense>
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
          :loading="saving"
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
          <v-btn color="error" text :loading="deleting" @click="deleteItemConfirm">OK</v-btn>
          <v-spacer />
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import axios from "axios";

// Adjust to match your project's real API base path / axios instance.
const API_URL = "http://localhost:8000/api/admin/contact";

// NOTE: this assumes a bearer token stored in localStorage, which is a
// common pattern but may not match your project's actual auth setup.
// Swap this for however your app already attaches auth headers
// (e.g. a shared axios instance / interceptor) if one already exists.
function authHeaders() {
  const token = localStorage.getItem("token");
  return token ? { Authorization: `Bearer ${token}` } : {};
}

export default {
  data() {
    return {
      search: "",
      dialog: false,
      dialogDelete: false,
      loading: false,
      saving: false,
      deleting: false,
      formError: "",
      form: this.emptyForm(),
      headers: [
        { text: "ລຳດັບ", value: "no", sortable: false, width: "70" },
        { text: "ເບີໂທ", value: "phoneNumber", sortable: false },
        { text: "Email", value: "email", sortable: false },
        { text: "Address", value: "address", sortable: false },
        { text: "Map Link", value: "mapLink", sortable: false },
        { text: "ແກ້ໄຂ", value: "edit", sortable: false, align: "center" },
        { text: "ລົບ", value: "delete", sortable: false, align: "center" },
      ],
      // Backend is a singleton: this will hold at most one item.
      aboutItems: [],
      editedIndex: -1,
      editedItem: null,
    };
  },

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "ເພີ່ມ About Us" : "ແກ້ໄຂ About Us";
    },
  },

  created() {
    this.initialize();
  },

  methods: {
    emptyForm() {
      return {
        phoneNumber: "",
        email: "",
        address: "",
        mapLink: "",
      };
    },

    // --- Mapping helpers: backend uses tel/link, frontend uses phoneNumber/mapLink ---
    fromApi(record) {
      return {
        id: record.id,
        phoneNumber: record.tel || "",
        email: record.email || "",
        address: record.address || "",
        mapLink: record.link || "",
      };
    },

    toApiPayload(item) {
      return {
        tel: item.phoneNumber || null,
        email: item.email || null,
        address: item.address || null,
        link: item.mapLink || null,
      };
    },

    async initialize() {
      this.loading = true;
      try {
        const { data } = await axios.get(API_URL, { headers: authHeaders() });
        // Backend returns a single object (singleton contact), not a list.
        // Wrap it in an array so the existing table UI can still render it.
        this.aboutItems = data.data ? [this.fromApi(data.data)] : [];
      } catch (err) {
        if (err.response?.status === 404) {
          // No contact record created yet - that's a valid empty state.
          this.aboutItems = [];
        } else {
          console.error("Failed to load About Us contacts:", err);
        }
      } finally {
        this.loading = false;
      }
    },

    openCreate() {
      this.editedIndex = -1;
      this.editedItem = null;
      this.form = this.emptyForm();
      this.formError = "";
      this.dialog = true;
    },

    editItem(item) {
      this.editedIndex = this.aboutItems.indexOf(item);
      this.editedItem = item;
      this.form = { ...item };
      this.formError = "";
      this.dialog = true;
    },

    deleteItem(item) {
      this.editedIndex = this.aboutItems.indexOf(item);
      this.editedItem = item;
      this.dialogDelete = true;
    },

    async deleteItemConfirm() {
      if (!this.editedItem) return this.closeDelete();
      this.deleting = true;
      try {
        // Backend has no :id on DELETE - it always removes the single
        // existing record, so no id is appended to the URL here.
        await axios.delete(API_URL, { headers: authHeaders() });
        this.aboutItems.splice(this.editedIndex, 1);
        this.closeDelete();
      } catch (err) {
        console.error("Failed to delete contact:", err);
      } finally {
        this.deleting = false;
      }
    },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.form = this.emptyForm();
        this.editedItem = null;
        this.editedIndex = -1;
        this.formError = "";
      });
    },

    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = null;
        this.editedIndex = -1;
      });
    },

    async save() {
      this.formError = "";
      this.saving = true;
      try {
        const payload = this.toApiPayload(this.form);

        if (this.editedIndex > -1 && this.editedItem) {
          // Update existing - backend has no :id on PUT, it always
          // targets the single existing record.
          await axios.put(API_URL, payload, { headers: authHeaders() });
          const updated = { ...this.editedItem, ...this.form };
          Object.assign(this.aboutItems[this.editedIndex], updated);
        } else {
          // Create new - backend rejects this with 409 if a record
          // already exists, since only one is allowed.
          const { data } = await axios.post(API_URL, payload, { headers: authHeaders() });
          this.aboutItems.push(this.fromApi(data.data));
        }

        this.close();
      } catch (err) {
        console.error("Failed to save contact:", err);
        if (err.response?.status === 409) {
          this.formError = "ມີຂໍ້ມູນຢູ່ແລ້ວ, ກະລຸນາໃຊ້ການແກ້ໄຂແທນການເພີ່ມ";
        } else {
          this.formError =
            err.response?.data?.message || "ບໍ່ສາມາດບັນທຶກຂໍ້ມູນໄດ້, ກະລຸນາລອງໃໝ່";
        }
      } finally {
        this.saving = false;
      }
    },
  },
};
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