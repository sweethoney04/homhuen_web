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

      <ManageRoomView
        :rooms="rooms"
        :search="search"
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
  }),

  created() {
    this.initialize()
  },

  methods: {
    initialize() {
      // Replace with real API later
      // const { data } = await this.$axios.get('/rooms')
      // this.rooms = data

      this.rooms = [
        {
          id: 1,
          roomName: 'Room A101',
          type: 'Deluxe',
          price: 250000,
          capacity: 2,
          active: true,
          image: '',
        },
        {
          id: 2,
          roomName: 'Room A102',
          type: 'Standard',
          price: 180000,
          capacity: 2,
          active: true,
          image: '',
        },
        {
          id: 3,
          roomName: 'Room B205',
          type: 'Family',
          price: 350000,
          capacity: 4,
          active: false,
          image: '',
        },
      ]
    },

    addRoom(item) {
      this.rooms.push({
        id: Date.now(),
        ...item,
      })
    },

    openEdit(item) {
      this.editedItem = { ...item }
      this.dialogUpdate = true
    },

    updateRoom(item) {
      const index = this.rooms.findIndex((room) => room.id === item.id)
      if (index > -1) {
        this.rooms.splice(index, 1, item)
      }
    },

    openDelete(item) {
      this.editedItem = { ...item }
      this.dialogDelete = true
    },

    removeRoom(item) {
      const index = this.rooms.findIndex((room) => room.id === item.id)
      if (index > -1) {
        this.rooms.splice(index, 1)
      }
    },

    toggleStatus(item) {
      item.active = !item.active
      // TODO: update API later
      // await this.$axios.patch(`/rooms/${item.id}`, { active: item.active })
    },
  },
}
</script>

<style scoped>
.page-title {
  font-weight: 600;
}
</style>