<template>
  <v-dialog
    v-model="show"
    width="360"
    persistent
    content-class="delete-dialog"
  >
    <v-card class="pa-4 rounded-lg" style="width: 360px; max-width: 360px;">
      <v-card-title class="text-subtitle-1 font-weight-medium justify-center text-center pt-2 pb-4">
        <div>ທ່ານຕ້ອງການລຶບຂໍ້ມູນນີ້?</div>
      </v-card-title>

      <v-card-actions class="pb-2 px-2">
        <v-btn
          outlined
          color="#064D8D"
          class="mr-2 text-none flex-grow-1"
          @click="close"
        >
          ຍົກເລີກ
        </v-btn>
        <v-btn
          color="#E53935"
          dark
          class="ml-2 text-none flex-grow-1"
          :loading="loading"
          :disabled="loading"
          @click="confirm"
        >
          ລຶບ
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  name: 'ManageRoomDelete',
  props: {
    value: {
      type: Boolean,
      default: false,
    },
    item: {
      type: Object,
      default: () => ({}),
    },
  },
  data() {
    return {
      loading: false,
    }
  },
  computed: {
    show: {
      get() {
        return this.value
      },
      set(val) {
        this.$emit('input', val)
      },
    },
  },
  methods: {
    close() {
      if (!this.loading) {
        this.show = false
      }
    },
    async confirm() {
      if (!this.item.id) {
        alert('Room ID is missing')
        return
      }

      this.loading = true
      try {
        const token = localStorage.getItem('token')
        if (!token) {
          throw new Error('Authentication token is missing')
        }

        await this.$axios.delete(`/admin/rooms/${this.item.id}`, {
          headers: {
            Authorization: `Bearer ${token}`,
          },
        })

        this.$emit('deleted', this.item)
        this.show = false
      } catch (error) {
        console.error('Delete room error:', error)
        alert(
          error.response?.status === 401
            ? 'Session expired. Please log in again.'
            : error.response?.data?.message || 'Could not delete room'
        )
      } finally {
        this.loading = false
      }
    },
  },
}
</script>

<style scoped>
.delete-dialog {
  max-width: 360px !important;
}
</style>