<template>
  <div>
    <h2 class="page-title mb-4">ຈັດການໜ້າ Banner</h2>

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
        :items="banners"
        :search="search"
        class="banner-table"
      >
        <template v-slot:item.no="{ item }">
          {{ banners.indexOf(item) + 1 }}
        </template>

        <template v-slot:item.image="{ item }">
          <v-img
            v-if="item.image"
            :src="item.image"
            max-width="80"
            max-height="50"
            contain
            class="my-1"
          />
          <span v-else class="text-caption grey--text">No image</span>
        </template>

        <template v-slot:item.active="{ item }">
          <v-switch
            v-model="item.active"
            inset
            color="success"
            dense
            hide-details
            @change="toggleStatus(item)"
          />
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
            ບໍ່ມີຂໍ້ມູນ Banner
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
                  v-model="editedItem.topic"
                  label="ຫົວຂໍ້"
                />
              </v-col>

              <v-col cols="12">
                <v-text-field
                  v-model="editedItem.link"
                  label="ລິ້ງ"
                />
              </v-col>

              <v-col cols="12">
                <v-text-field
                  v-model.number="editedItem.order"
                  label="ລຳດັບ"
                  type="number"
                />
              </v-col>

              <v-col cols="12">
                <v-text-field
                  v-model="editedItem.image"
                  label="URL ຮູບພາບ"
                />
              </v-col>

              <v-col cols="12" class="d-flex align-center">
                <span class="mr-3">ສະຖານະ</span>
                <v-switch
                  v-model="editedItem.active"
                  inset
                  color="success"
                  hide-details
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
  name: 'BannerPage',
  data: () => ({
    search: '',
    dialog: false,
    dialogDelete: false,
    editedIndex: -1,
    editedItem: {
      id: null,
      topic: '',
      link: '',
      order: 1,
      active: true,
      image: '',
    },
    defaultItem: {
      id: null,
      topic: '',
      link: '',
      order: 1,
      active: true,
      image: '',
    },
    headers: [
      { text: 'ລຳດັບ', value: 'no', sortable: false, width: '70' },
      { text: 'ຮູບ', value: 'image', sortable: false, width: '120' },
      { text: 'ຫົວຂໍ້', value: 'topic', sortable: false },
      { text: 'ລິ້ງ', value: 'link', sortable: false },
      { text: 'ລຳດັບ', value: 'order', sortable: false, width: '90' },
      { text: 'ສະຖານະ', value: 'active', sortable: false, align: 'center' },
      { text: 'ແກ້ໄຂ', value: 'edit', sortable: false, align: 'center' },
      { text: 'ລົບ', value: 'delete', sortable: false, align: 'center' },
    ],
    banners: [],
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'ເພີ່ມ Banner' : 'ແກ້ໄຂ Banner'
    },
  },

  created() {
    this.initialize()
  },

  methods: {
    initialize() {
      this.banners = [
        {
          id: 1,
          topic: 'Promotion 20%',
          link: 'https://www.google.com/',
          order: 1,
          active: true,
          image: '',
        },
        {
          id: 2,
          topic: 'Promotion 30%',
          link: 'https://www.facebook.com/',
          order: 2,
          active: true,
          image: '',
        },
        {
          id: 3,
          topic: 'New Offer',
          link: 'https://www.youtube.com/',
          order: 3,
          active: false,
          image: '',
        },
      ]
    },

    openCreate() {
      this.editedIndex = -1
      this.editedItem = { ...this.defaultItem, id: Date.now() }
      this.dialog = true
    },

    openEdit(item) {
      this.editedIndex = this.banners.indexOf(item)
      this.editedItem = { ...item }
      this.dialog = true
    },

    openDelete(item) {
      this.editedIndex = this.banners.indexOf(item)
      this.editedItem = { ...item }
      this.dialogDelete = true
    },

    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.banners[this.editedIndex], this.editedItem)
      } else {
        this.banners.push({ ...this.editedItem })
      }

      this.close()
    },

    deleteItemConfirm() {
      this.banners.splice(this.editedIndex, 1)
      this.closeDelete()
    },

    close() {
      this.dialog = false
      this.editedItem = { ...this.defaultItem }
      this.editedIndex = -1
    },

    closeDelete() {
      this.dialogDelete = false
      this.editedItem = { ...this.defaultItem }
      this.editedIndex = -1
    },

    toggleStatus(item) {
      item.active = !item.active
      // TODO: replace with API update:
      // await this.$axios.patch(`/banners/${item.id}`, { active: item.active })
    },
  },
}
</script>

<style scoped>
.page-title {
  font-weight: 600;
}

.banner-table >>> thead tr th {
  background-color: #064d8d !important;
  color: #ffffff !important;
}
</style>