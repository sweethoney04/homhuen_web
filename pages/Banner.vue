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
          <BannerCreate @created="onBannerCreated" />
        </v-col>
      </v-row>

      <BannerView
        :banners="banners"
        :search="search"
        @toggle-status="toggleStatus"
        @edit-item="openEdit"
        @delete-item="openDelete"
      />
    </v-card>

    <!-- Edit Dialog -->
    <v-dialog v-model="dialog" max-width="500px">
      <v-card>
        <v-card-title class="text-h6">
          ແກ້ໄຂ Banner
        </v-card-title>

        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <div class="label-text">ຫົວຂໍ້</div>
                <v-text-field
                  v-model="editedItem.topic"
                  dense
                  outlined
                  hide-details
                />
              </v-col>

              <v-col cols="12">
                <div class="label-text">Link</div>
                <v-text-field
                  v-model="editedItem.link"
                  dense
                  outlined
                  hide-details
                />
              </v-col>

              <v-col cols="12" sm="6">
                <div class="label-text">ລຳດັບ</div>
                <v-text-field
                  v-model.number="editedItem.order"
                  dense
                  outlined
                  hide-details
                  type="number"
                />
              </v-col>

              <v-col cols="12" sm="6">
                <div class="label-text">ປະເພດ Banner</div>
                <v-select
                  v-model="editedItem.type"
                  :items="[
                    { text: 'ຂະໜາດນ້ອຍ (Small)', value: 0 },
                    { text: 'ຂະໜາດໃຫຍ່ (Large)', value: 1 }
                  ]"
                  item-text="text"
                  item-value="value"
                  dense
                  outlined
                  hide-details
                />
              </v-col>

              <v-col cols="12" sm="6">
                <div class="label-text">ສະຖານະ</div>
                <v-select
                  v-model="editedItem.active"
                  :items="[
                    { text: 'ເປີດ', value: true },
                    { text: 'ປິດ', value: false }
                  ]"
                  item-text="text"
                  item-value="value"
                  dense
                  outlined
                  hide-details
                />
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>

        <v-card-actions>
          <v-spacer />
          <v-btn text @click="close">Cancel</v-btn>
          <v-btn color="primary" dark @click="save">Save</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Delete Dialog -->
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
import BannerCreate from '@/components/Banner/Create.vue'
import BannerView from '@/components/Banner/View.vue'

export default {
  name: 'BannerPage',
  components: {
    BannerCreate,
    BannerView,
  },
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
      type: 0,
      active: true,
      image: '',
    },
    defaultItem: {
      id: null,
      topic: '',
      link: '',
      order: 1,
      type: 0,
      active: true,
      image: '',
    },
    banners: [],
  }),

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
          type: 0,
          active: true,
          image: '',
        },
        {
          id: 2,
          topic: 'Promotion 30%',
          link: 'https://www.facebook.com/',
          order: 2,
          type: 1,
          active: true,
          image: '',
        },
        {
          id: 3,
          topic: 'New Offer',
          link: 'https://www.youtube.com/',
          order: 3,
          type: 0,
          active: false,
          image: '',
        },
      ]
    },

    onBannerCreated(banner) {
      this.banners.push({
        ...banner,
        id: banner.id || Date.now(),
      })
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

.label-text {
  font-size: 13px;
  color: #616161;
  margin-bottom: 4px;
}

.banner-table >>> thead tr th {
  background-color: #064d8d !important;
  color: #ffffff !important;
}
</style>