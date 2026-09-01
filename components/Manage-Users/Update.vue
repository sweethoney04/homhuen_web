<template>
  <v-dialog v-model="show" max-width="500px">
    <v-card>
      <v-card-title class="text-h6">ແກ້ໄຂພະນັກງານ</v-card-title>

      <v-card-text>
        <v-text-field v-model.trim="form.username" label="Username" />
        <v-text-field v-model.trim="form.phone" label="Phone" />
        <v-text-field v-model.trim="form.password" label="Password" type="password" />
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
        username: item.username || item.userName || item.name || '',
        phone: item.phone || '',
        password: '',
      }
    },
    close() {
      this.show = false
    },
    save() {
      if (!this.form.username.trim()) {
        alert('ກະລຸນາປ້ອນ Username')
        return
      }

      this.$emit('updated', { ...this.form })
      this.close()
    },
  },
}
</script>