<template>
  <v-dialog v-model="show" max-width="420px">
    <v-card>
      <v-card-title class="text-h6">
        ທ່ານແນ່ໃຈບໍ່ວ່າຈະລົບຂໍ້ມູນລູກຄ້ານີ້?
      </v-card-title>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn text @click="close">Cancel</v-btn>
        <v-btn color="error" text @click="confirm">OK</v-btn>
        <v-spacer></v-spacer>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  name: 'CustomerDelete',
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
    confirm() {
      // TODO: ຮ້ອງ API ລົບຂໍ້ມູນລູກຄ້າ ຕົວຢ່າງ:
      // await this.$axios.delete(`/customers/${this.item.id}`)
      this.$emit('deleted', this.item)
      this.close()
    },
  },
}
</script>