<template>
  <v-dialog v-model="show" max-width="500px">
    <v-card>
      <v-card-title>
        <span class="text-h6">ແກ້ໄຂຂໍ້ມູນລູກຄ້າ</span>
      </v-card-title>

      <v-card-text>
        <v-container>
          <v-row>
            <v-col cols="12" sm="6">
              <v-text-field v-model="form.customerName" label="ຊື່ລູກຄ້າ"></v-text-field>
            </v-col>

            <v-col cols="12" sm="6">
              <v-text-field v-model="form.interestedRoom" label="ຫ້ອງທີ່ສົນໃຈ"></v-text-field>
            </v-col>

            <v-col cols="12" sm="6">
              <v-text-field v-model="form.phoneNumber" label="ເບີໂທ"></v-text-field>
            </v-col>

            <v-col cols="12" sm="6">
              <v-menu
                v-model="dateMenu"
                :close-on-content-click="false"
                transition="scale-transition"
                offset-y
                min-width="auto"
              >
                <template v-slot:activator="{ on: onDate, attrs: attrsDate }">
                  <v-text-field
                    v-model="form.contactDate"
                    label="ວັນທີ່ຕິດຕໍ່"
                    readonly
                    v-bind="attrsDate"
                    v-on="onDate"
                  ></v-text-field>
                </template>
                <v-date-picker
                  v-model="form.contactDate"
                  @input="dateMenu = false"
                ></v-date-picker>
              </v-menu>
            </v-col>

            <v-col cols="12">
              <v-text-field v-model="form.detail" label="ລາຍລະອຽດ"></v-text-field>
            </v-col>

            <v-col cols="12" sm="6">
              <v-text-field v-model="form.responsible" label="ຜູ້ຮັບຜິດຊອບ"></v-text-field>
            </v-col>

            <v-col cols="12" sm="6">
              <v-select
                v-model="form.status"
                :items="statusOptions"
                label="ສະຖານະ"
              ></v-select>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>

      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn text @click="close">Cancel</v-btn>
        <v-btn color="primary" text @click="save">Save</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  name: 'CustomerUpdate',
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
      dateMenu: false,
      statusOptions: ['ໃໝ່', 'ກຳລັງຕິດຕໍ່', 'ປິດການຂາຍແລ້ວ'],
      form: { ...this.item },
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
    item(val) {
      this.form = { ...val }
    },
  },
  methods: {
    close() {
      this.show = false
    },
    save() {
      // TODO: ຮ້ອງ API ອັບເດດຂໍ້ມູນລູກຄ້າ ຕົວຢ່າງ:
      // await this.$axios.put(`/customers/${this.form.id}`, this.form)
      this.$emit('updated', { ...this.form })
      this.close()
    },
  },
}
</script>