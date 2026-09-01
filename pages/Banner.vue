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
          <BannerCreate @created="onBannerCreated" @error="onFormError" />
        </v-col>
      </v-row>

      <v-alert v-if="errorMessage" type="error" dense text class="mb-2">
        {{ errorMessage }}
      </v-alert>

      <BannerView
        :banners="banners"
        :search="search"
        :loading="loading"
        @toggle-status="toggleStatus"
        @edit-item="openEdit"
        @delete-item="openDelete"
        @reset="initialize"
      />
    </v-card>

    <BannerUpdate
      v-model="dialog"
      :item="editedItem"
      @updated="onBannerUpdated"
      @error="onFormError"
    />

    <BannerDelete
      v-model="dialogDelete"
      :item="editedItem"
      @deleted="removeBanner"
    />
  </div>
</template>

<script>
import BannerCreate from '@/components/Banner/Create.vue'
import BannerUpdate from '@/components/Banner/Update.vue'
import BannerView from '@/components/Banner/View.vue'
import BannerDelete from '@/components/Banner/Delete.vue'

export default {
  name: 'BannerPage',
  components: {
    BannerCreate,
    BannerUpdate,
    BannerView,
    BannerDelete,
  },
  data: () => ({
    search: '',
    dialog: false,
    dialogDelete: false,
    editedIndex: -1,
    editedItem: {},
    defaultItem: {},
    banners: [],
    loading: false,
    errorMessage: '',
  }),

  created() {
    this.initialize()
  },

  methods: {
    authHeader() {
      const token = localStorage.getItem('token')
      if (!token) throw new Error('Authentication token is missing')
      return { Authorization: `Bearer ${token}` }
    },

    // backend returns image as a relative path like "/uploads/xxx.png" —
    // resolve it against the API host so <v-img> can load it.
    // adjust this if your deployment serves /uploads from a different host.
    resolveImage(path) {
      if (!path) return ''
      if (/^https?:\/\//.test(path)) return path
      const base = this.$axios.defaults.baseURL || ''
      try {
        return new URL(base, window.location.origin).origin + path
      } catch (e) {
        return path
      }
    },

    async initialize() {
      this.loading = true
      this.errorMessage = ''

      try {
        const { data } = await this.$axios.get('/admin/banners', {
          headers: this.authHeader(),
        })

        const list = data.data || data || []
        this.banners = list.map((b) => ({ ...b, image: this.resolveImage(b.image) }))
      } catch (error) {
        this.errorMessage = 'ບໍ່ສາມາດໂຫລດຂໍ້ມູນ Banner ໄດ້'
        console.error('GET /admin/banners error:', error)
        this.banners = []
      } finally {
        this.loading = false
      }
    },

    buildFormData(banner) {
      const formData = new FormData()
      formData.append('type', Number(banner.type ?? 0))
      formData.append('topic', banner.topic || '')
      formData.append('link_url', banner.link_url || '')
      formData.append('display_order', Number(banner.display_order ?? 1))
      formData.append('status', Number(banner.status ?? 1))
      if (banner.imageFile) formData.append('image', banner.imageFile)
      return formData
    },

    async onBannerCreated(banner) {
      try {
        await this.$axios.post('/admin/banners', this.buildFormData(banner), {
          headers: this.authHeader(), // let axios set the multipart boundary itself
        })
        // POST only returns { id }, so refresh from the server to get the real image path
        await this.initialize()
      } catch (error) {
        this.errorMessage = 'ບໍ່ສາມາດສ້າງ Banner ໄດ້'
        console.error('POST /admin/banners error:', error)
      }
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

    async onBannerUpdated(banner) {
      try {
        await this.$axios.put(`/admin/banners/${banner.id}`, this.buildFormData(banner), {
          headers: this.authHeader(),
        })
        // PUT only returns { success, message }, so refresh from the server
        await this.initialize()
      } catch (error) {
        this.errorMessage = 'ບໍ່ສາມາດອັບເດດ Banner ໄດ້'
        console.error('PUT /admin/banners error:', error)
      } finally {
        this.close()
      }
    },

    removeBanner(item) {
      this.banners = this.banners.filter((banner) => banner.id !== item.id)
      this.closeDelete()
    },

    close() {
      this.dialog = false
      this.editedItem = {}
      this.editedIndex = -1
    },

    closeDelete() {
      this.dialogDelete = false
      this.editedItem = {}
      this.editedIndex = -1
    },

    onFormError(message) {
      this.errorMessage = message
    },

    async toggleStatus(item) {
      const newStatus = item.status === 1 ? 0 : 1
      const formData = new FormData()
      formData.append('status', newStatus)

      try {
        await this.$axios.put(`/admin/banners/${item.id}`, formData, {
          headers: this.authHeader(),
        })
        item.status = newStatus
      } catch (error) {
        this.errorMessage = 'ບໍ່ສາມາດປ່ຽນສະຖານະ Banner ໄດ້'
        console.error('toggleStatus error:', error)
      }
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