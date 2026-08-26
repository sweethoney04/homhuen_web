<template>
  <div>
    <h2 class="page-title mb-4">ຈັດການຫ້ອງ</h2>

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
          <ManageRoomCreate @created="addRoom" />
        </v-col>
      </v-row>

      <v-alert v-if="errorMessage" type="error" dense text class="mb-2">
        {{ errorMessage }}
      </v-alert>

      <ManageRoomView
        :rooms="rooms"
        :search="search"
        :loading="loading"
        @edit-item="openEdit"
        @delete-item="openDelete"
        @toggle-status="toggleStatus"
        @reset="initialize"
      />
    </v-card>

    <ManageRoomUpdate
      v-model="dialogUpdate"
      :item="editedItem"
      @updated="updateRoom"
    />

    <ManageRoomDelete
      v-model="dialogDelete"
      :item="editedItem"
      @deleted="removeRoom"
    />
  </div>
</template>

<script>
import ManageRoomView from '~/components/Manage-Room/View.vue'
import ManageRoomCreate from '~/components/Manage-Room/Create.vue'
import ManageRoomUpdate from '~/components/Manage-Room/Update.vue'
import ManageRoomDelete from '~/components/Manage-Room/Delete.vue'

export default {
  name: 'ManageRoomPage',
  components: {
    ManageRoomView,
    ManageRoomCreate,
    ManageRoomUpdate,
    ManageRoomDelete,
  },

  data: () => ({
    search: '',
    rooms: [],
    dialogUpdate: false,
    dialogDelete: false,
    editedItem: {},
    loading: false,
    errorMessage: '',
  }),

  created() {
    this.initialize()
  },

  methods: {
    // GET All Rooms (Admin)
    async initialize() {
      this.loading = true
      this.errorMessage = ''
      try {
        const { data } = await this.$axios.get('/admin/rooms')
        // ປັບຕາມໂຄງສ້າງ response ຈິງ ເຊັ່ນ data.data ຫຼື data.rooms
        this.rooms = data.data || data.rooms || data
      } catch (err) {
        this.errorMessage = 'ບໍ່ສາມາດໂຫລດຂໍ້ມູນຫ້ອງໄດ້'
        console.error('GET /admin/rooms error:', err)
      } finally {
        this.loading = false
      }
    },

    // Refresh the list after the create dialog saves a room.
    async addRoom() {
      await this.initialize()
    },

    openEdit(item) {
      this.editedItem = { ...item }
      this.dialogUpdate = true
    },

    // PUT Edit Room
    async updateRoom(item) {
      try {
        const { data } = await this.$axios.put(`/admin/rooms/${item.id}`, item)
        const updated = data.data || data.room || data
        const index = this.rooms.findIndex((room) => room.id === item.id)
        if (index > -1) this.rooms.splice(index, 1, updated)
        else await this.initialize()
      } catch (err) {
        this.errorMessage = 'ບໍ່ສາມາດແກ້ໄຂຫ້ອງໄດ້'
        console.error('PUT /admin/rooms/:id error:', err)
      }
    },

    openDelete(item) {
      this.editedItem = { ...item }
      this.dialogDelete = true
    },

    // DELETE Room
    async removeRoom(item) {
      try {
        await this.$axios.delete(`/admin/rooms/${item.id}`)
        const index = this.rooms.findIndex((room) => room.id === item.id)
        if (index > -1) {
          this.rooms.splice(index, 1)
        }
      } catch (err) {
        this.errorMessage = 'ບໍ່ສາມາດລຶບຫ້ອງໄດ້'
        console.error('DELETE /admin/rooms/:id error:', err)
      }
    },

    // PATCH/PUT toggle active status
    async toggleStatus(item) {
      const newStatus = !item.active
      try {
        await this.$axios.put(`/admin/rooms/${item.id}`, {
          ...item,
          active: newStatus,
        })
        item.active = newStatus
      } catch (err) {
        this.errorMessage = 'ບໍ່ສາມາດປ່ຽນສະຖານະໄດ້'
        console.error('toggleStatus error:', err)
      }
    },
  },
}
</script>

<style scoped>
.page-title {
  font-weight: 600;
}
</style>