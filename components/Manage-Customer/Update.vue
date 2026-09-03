<template>
  <v-dialog v-model="show" max-width="520px" persistent>
    <v-card class="rounded-lg overflow-hidden pb-4">
      <!-- Header Bar -->
      <v-card-title
        class="white--text d-flex justify-space-between align-center px-6 py-3"
        style="background-color: #064d8d"
      >
        <span class="text-subtitle-1 font-weight-medium">ແກ້ໄຂຂໍ້ມູນລູກຄ້າ</span>
        <v-btn icon dark small @click="close">
          <v-icon size="18">mdi-close</v-icon>
        </v-btn>
      </v-card-title>

      <!-- Form Content -->
      <v-card-text class="pt-4 px-6">
        <v-container class="pa-0">
          <v-row dense>
            <v-col cols="12">
              <div class="field-label">ຊື່ ແລະ ນາມສະກຸນ</div>
              <v-text-field v-model="form.name" placeholder="ຊື່ ແລະ ນາມສະກຸນ" outlined dense hide-details></v-text-field>
            </v-col>

            <v-col cols="12" sm="6" class="mt-2 pr-sm-2">
              <div class="field-label">ເບີໂທລະສັບ</div>
              <v-text-field v-model="form.phone" placeholder="ເບີໂທລະສັບ" outlined dense hide-details></v-text-field>
            </v-col>

            <v-col cols="12" sm="6" class="mt-2 pl-sm-2">
              <div class="field-label">ຊ່ອງທາງທີ່ຕິດຕໍ່ເຂົ້າມາ</div>
              <v-select
                v-model="form.channel"
                :items="channelOptions"
                outlined
                dense
                hide-details
              ></v-select>
            </v-col>

            <v-col cols="12" class="mt-2">
              <div class="field-label">ຫ້ອງທີ່ສົນໃຈ</div>
              <v-text-field v-model="form.room_interest" placeholder="ຫ້ອງ A01" outlined dense hide-details></v-text-field>
            </v-col>

            <v-col cols="12" sm="6" class="mt-2 pr-sm-2">
              <div class="field-label">ຕິດຕໍ່ຄັ້ງທຳອິດ</div>
              <v-text-field v-model="form.first_contact" type="date" outlined dense hide-details></v-text-field>
            </v-col>

            <v-col cols="12" sm="6" class="mt-2 pl-sm-2">
              <div class="field-label">ນັດໝາຍຄັ້ງຕໍ່ໄປ</div>
              <v-text-field v-model="form.next_appointment" type="date" outlined dense hide-details></v-text-field>
            </v-col>

            <v-col cols="12" sm="6" class="mt-2 pr-sm-2">
              <div class="field-label">ຜູ້ຮັບຜິດຊອບ</div>
              <v-text-field v-model="form.assigned_to" placeholder="ຜູ້ຮັບຜິດຊອບ" outlined dense hide-details></v-text-field>
            </v-col>

            <v-col cols="12" sm="6" class="mt-2 pl-sm-2">
              <div class="field-label">ສະຖານະປັດຈຸບັນ</div>
              <v-select
                v-model="form.status"
                :items="statusOptions"
                item-text="text"
                item-value="value"
                outlined
                dense
                hide-details
              ></v-select>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>

      <!-- Main Action Footer -->
      <v-card-actions class="px-6 pt-3 pb-2 justify-end">
        <v-btn
          color="#064D8D"
          dark
          depressed
          min-width="120"
          class="rounded-lg text-none font-weight-medium px-6"
          @click="save"
        >
          ບັນທຶກ
        </v-btn>
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
      channelOptions: ['Website', 'Facebook', 'WhatsApp', 'Call', 'Walk-in'],
      statusOptions: [
        { text: 'ກຳລັງຕິດຕໍ່', value: 0 },
        { text: 'ໃໝ່', value: 1 },
        { text: 'ປິດການຂາຍແລ້ວ', value: 2 },
      ],
      form: this.emptyForm(),
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
      if (val) {
        this.form = { ...this.emptyForm(), ...val }
      }
    },
  },
  methods: {
    emptyForm() {
      return {
        id: null,
        name: '',
        phone: '',
        channel: 'Website',
        room_interest: '',
        first_contact: null,
        next_appointment: null,
        assigned_to: '',
        status: 0,
      }
    },
    close() {
      this.show = false
    },
    save() {
      if (!this.form.name || !this.form.name.trim()) {
        alert('ກະລຸນາປ້ອນຊື່ລູກຄ້າ')
        return
      }

      this.$emit('updated', { ...this.form })
      this.close()
    },
  },
}
</script>

<style scoped>
.field-label {
  font-size: 12px;
  color: #555;
  margin-bottom: 4px;
}
</style>