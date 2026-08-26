<template>
  <v-dialog v-model="show" max-width="420px">
    <v-card>
      <v-card-title class="text-h6 d-flex flex-column align-center text-center pt-6">
        <v-icon color="error" size="48" class="mb-2">mdi-alert-circle-outline</v-icon>
        ທ່ານແນ່ໃຈບໍ່ວ່າຈະລົບຫ້ອງແຖວນີ້?
      </v-card-title>

      <v-card-subtitle v-if="item.roomName" class="text-center pb-0">
        "{{ item.roomName }}"
      </v-card-subtitle>

      <v-card-actions class="pb-5 pt-4 px-6">
        <v-btn
          outlined
          color="#064D8D"
          block
          class="mr-2"
          @click="close"
        >
          Cancel
        </v-btn>
        <v-btn
          color="error"
          dark
          block
          class="ml-2"
          @click="confirm"
        >
          ລົບ
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
      this.show = false
    },
    async confirm() {
      // TODO: ຮ້ອງ API ລົບຂໍ້ມູນຫ້ອງແຖວ ຕົວຢ່າງ:
      // await this.$axios.delete(`/rooms/${this.item.id}`)
      this.$emit('deleted', this.item)
      this.close()
    },
  },
}
</script>