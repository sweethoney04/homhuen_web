<template>
  <v-dialog v-model="show" max-width="500px">
    <v-card>
      <v-card-title class="text-h6">ແກ້ໄຂພະນັກງານ</v-card-title>

      <v-card-text>
        <v-text-field v-model.trim="form.fullName" label="ຊື່-ນາມສະກຸນ" />
        <v-text-field v-model.trim="form.position" label="ຕຳແໜ່ງ" />
        <v-text-field v-model.trim="form.phone" label="ເບີໂທ" />
        <v-text-field v-model.trim="form.email" label="Email" type="email" />
        <v-switch
          v-model="form.status"
          inset
          color="success"
          hide-details
          :label="form.status ? 'Active' : 'Inactive'"
        />
      </v-card-text>

      <v-card-actions>
        <v-spacer />
        <v-btn text @click="close">Cancel</v-btn>
        <v-btn color="primary" text @click="save">Save</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  name: 'ManageUsersUpdate',
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
      form: this.buildForm(this.item),
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
  watch: {
    item(value) {
      this.form = this.buildForm(value)
    },
  },
  methods: {
    buildForm(item) {
      return {
        id: item.id,
        fullName: item.fullName || '',
        position: item.position || '',
        phone: item.phone || '',
        email: item.email || '',
        status: item.status !== false,
      }
    },
    close() {
      this.show = false
    },
    save() {
      if (!this.form.fullName.trim()) {
        alert('ກະລຸນາປ້ອນຊື່-ນາມສະກຸນ')
        return
      }

      this.$emit('updated', { ...this.form })
      this.close()
    },
  },
}
</script>