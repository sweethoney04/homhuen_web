<template>
  <v-dialog v-model="show" max-width="500px">
    <template v-slot:activator="{ on, attrs }">
      <v-btn color="#064D8D" dark v-bind="attrs" v-on="on">
        ເພີ່ມລູກຄ້າ
      </v-btn>
    </template>

    <v-card>
      <v-card-title>
        <span class="text-h6">ເພີ່ມລູກຄ້າ</span>
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
  name: 'CustomerCreate',
  data() {
    return {
      show: false,
      dateMenu: false,
      statusOptions: ['ໃໝ່', 'ກຳລັງຕິດຕໍ່', 'ປິດການຂາຍແລ້ວ'],
      form: this.emptyForm(),
    }
  },
  methods: {
    emptyForm() {
      return {
        customerName: '',
        interestedRoom: '',
        phoneNumber: '',
        contactDate: '',
        detail: '',
        responsible: '',
        status: 'ໃໝ່',
      }
    },
    close() {
      this.show = false
      this.$nextTick(() => {
        this.form = this.emptyForm()
      })
    },
    save() {
      // TODO: ຮ້ອງ API ສ້າງລູກຄ້າໃໝ່ ຕົວຢ່າງ:
      // const { data } = await this.$axios.post('/customers', this.form)
      this.$emit('created', { ...this.form })
      this.close()
    },
  },
}
</script>