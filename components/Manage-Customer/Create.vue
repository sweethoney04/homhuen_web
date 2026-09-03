<template>
  <v-dialog v-model="show" max-width="520px" persistent>
    <template v-slot:activator="{ on, attrs }">
      <v-btn color="#064D8D" dark v-bind="attrs" v-on="on">
        ເພີ່ມລູກຄ້າ
      </v-btn>
    </template>

    <v-card class="rounded-lg overflow-hidden pb-4">
      <!-- Header Bar -->
      <v-card-title
        class="white--text d-flex justify-space-between align-center px-6 py-3"
        style="background-color: #064d8d"
      >
        <span class="text-subtitle-1 font-weight-medium"><div>ລາຍລະອຽດຂໍ້ມູນລູກຄ້າ</div></span>
        <v-btn icon dark small @click="close">
          <v-icon size="18">mdi-close</v-icon>
        </v-btn>
      </v-card-title>

      <!-- Form Content -->
      <v-card-text class="pt-4 px-6">
        <div class="text-subtitle-2 font-weight-bold mb-2 primary-text"><div>ຂໍ້ມູນລູກຄ້າ</div></div>

        <v-container class="pa-0">
          <v-row dense>
            <!-- 1. Name -->
            <v-col cols="12">
              <div class="field-label">ຊື່ ແລະ ນາມສະກຸນ</div>
              <v-text-field
                v-model="form.name"
                dense
                outlined
                hide-details
                placeholder="ຊື່ ແລະ ນາມສະກຸນ"
                prepend-inner-icon="mdi-account-outline"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <!-- 2. Phone & Contact Channel -->
            <v-col cols="12" sm="6" class="mt-2 pr-sm-2">
              <div class="field-label">ເບີໂທລະສັບ</div>
              <v-text-field
                v-model="form.phone"
                dense
                outlined
                hide-details
                type="tel"
                placeholder="ເບີໂທລະສັບ"
                prepend-inner-icon="mdi-phone-outline"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <v-col cols="12" sm="6" class="mt-2 pl-sm-2">
              <div class="field-label">ຊ່ອງທາງທີ່ຕິດຕໍ່ເຂົ້າມາ</div>
              <v-select
                v-model="form.channel"
                :items="channelOptions"
                dense
                outlined
                hide-details
                placeholder="Website"
                class="custom-input"
              ></v-select>
            </v-col>

            <!-- 3. Room Interest -->
            <v-col cols="12" class="mt-2">
              <div class="field-label">ຫ້ອງທີ່ສົນໃຈ</div>
              <v-text-field
                v-model="form.room_interest"
                dense
                outlined
                hide-details
                placeholder="ຫ້ອງ A01"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <!-- 4. First Contact & Next Appointment Dates -->
            <v-col cols="12" sm="6" class="mt-2 pr-sm-2">
              <div class="field-label">ຕິດຕໍ່ຄັ້ງທຳອິດ</div>
              <v-text-field
                v-model="form.first_contact"
                dense
                outlined
                hide-details
                type="date"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <v-col cols="12" sm="6" class="mt-2 pl-sm-2">
              <div class="field-label">ນັດໝາຍຄັ້ງຕໍ່ໄປ</div>
              <v-text-field
                v-model="form.next_appointment"
                dense
                outlined
                hide-details
                type="date"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <!-- 5. Assigned To -->
            <v-col cols="12" class="mt-2">
              <div class="field-label">ຜູ້ຮັບຜິດຊອບ</div>
              <v-text-field
                v-model="form.assigned_to"
                dense
                outlined
                hide-details
                placeholder="ຜູ້ຮັບຜິດຊອບ"
                prepend-inner-icon="mdi-account-outline"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <!-- 6. Status -->
            <v-col cols="12" class="mt-2">
              <div class="field-label">ສະຖານະປັດຈຸບັນ</div>
              <v-select
                v-model="form.status"
                :items="statusOptions"
                item-text="text"
                item-value="value"
                dense
                outlined
                hide-details
                class="custom-input status-select"
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
  name: 'CustomerCreate',
  data() {
    return {
      show: false,
      channelOptions: ['Website', 'Facebook', 'WhatsApp', 'Call', 'Walk-in'],
      statusOptions: [
        { text: 'ກຳລັງຕິດຕໍ່', value: 0 },
        { text: 'ໃໝ່', value: 1 },
        { text: 'ປິດການຂາຍແລ້ວ', value: 2 },
      ],
      form: this.emptyForm(),
    }
  },
  methods: {
    emptyForm() {
      return {
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
      this.$nextTick(() => {
        this.form = this.emptyForm()
      })
    },
    save() {
      if (!this.form.name || !this.form.name.trim()) {
        alert('ກະລຸນາປ້ອນຊື່ລູກຄ້າ')
        return
      }

      this.$emit('created', { ...this.form })
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

.primary-text {
  color: #064d8d;
}
</style>