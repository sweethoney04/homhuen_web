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
            <!-- 1. Full Name (Full Width) -->
            <v-col cols="12">
              <div class="field-label">ຊື່ ແລະ ນາມສະກຸນ</div>
              <v-text-field
                v-model="form.customerName"
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
                v-model="form.phoneNumber"
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

            <!-- 3. Interested Room (Full Width) -->
            <v-col cols="12" class="mt-2">
              <div class="field-label">ຫ້ອງທີ່ສົນໃຈ</div>
              <v-text-field
                v-model="form.roomInterested"
                dense
                outlined
                hide-details
                placeholder="ຫ້ອງ A01"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <!-- 4. Contact Record Section -->
            <v-col cols="12" class="mt-2">
              <div class="field-label">ບັນທຶກການຕິດຕໍ່</div>
              
              <!-- History List Tag Display -->
              <div v-if="form.contactLogs.length" class="contact-logs-container mb-2 pa-2 rounded grey lighten-4">
                <div v-for="(log, index) in form.contactLogs" :key="index" class="text-caption mb-1">
                  <span class="text-caption grey--text text--darken-1">{{ log.date }}</span>
                  <div class="black--text">{{ log.note }}</div>
                </div>
              </div>

              <!-- Input + Add Button Group -->
              <v-row dense class="align-center">
                <v-col cols="8" sm="9">
                  <v-text-field
                    v-model="newContactNote"
                    dense
                    outlined
                    hide-details
                    placeholder="ພິມບັນທຶກການຕິດຕໍ່..."
                    class="custom-input"
                    @keyup.enter="addContactNote"
                  ></v-text-field>
                </v-col>
                <v-col cols="4" sm="3" class="pl-2">
                  <v-btn
                    block
                    color="#064D8D"
                    dark
                    depressed
                    class="rounded-md text-none"
                    @click="addContactNote"
                  >
                    ບັນທຶກ
                  </v-btn>
                </v-col>
              </v-row>
            </v-col>

            <!-- 5. First Contact & Next Appointment Dates -->
            <v-col cols="12" sm="6" class="mt-2 pr-sm-2">
              <div class="field-label">ຕິດຕໍ່ຄັ້ງທຳອິດ</div>
              <v-text-field
                v-model="form.firstContactDate"
                dense
                outlined
                hide-details
                type="date"
                placeholder="DD/MM/YYYY"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <v-col cols="12" sm="6" class="mt-2 pl-sm-2">
              <div class="field-label">ນັດໝາຍຄັ້ງຕໍ່ໄປ</div>
              <v-text-field
                v-model="form.nextAppointmentDate"
                dense
                outlined
                hide-details
                type="date"
                placeholder="DD/MM/YYYY"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <!-- 6. Responsible Person (Full Width) -->
            <v-col cols="12" class="mt-2">
              <div class="field-label">ຜູ້ຮັບຜິດຊອບ</div>
              <v-text-field
                v-model="form.responsible"
                dense
                outlined
                hide-details
                placeholder="ຜູ້ຮັບຜິດຊອບ"
                prepend-inner-icon="mdi-account-outline"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <!-- 7. Current Status Select (Full Width) -->
            <v-col cols="12" class="mt-2">
              <div class="field-label">ສະຖານະປັດຈຸບັນ</div>
              <v-select
                v-model="form.status"
                :items="statusOptions"
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
      newContactNote: '',
      channelOptions: ['Website', 'Facebook', 'WhatsApp', 'Call', 'Walk-in'],
      statusOptions: ['ກຳລັງຕິດຕໍ່', 'ໃໝ່', 'ປິດການຂາຍແລ້ວ'],
      form: this.emptyForm(),
    }
  },
  methods: {
    emptyForm() {
      return {
        customerName: '',
        phoneNumber: '',
        channel: 'Website',
        roomInterested: '',
        contactLogs: [],
        firstContactDate: '',
        nextAppointmentDate: '',
        responsible: '',
        status: 'ກຳລັງຕິດຕໍ່',
      }
    },
    addContactNote() {
      if (!this.newContactNote.trim()) return
      const now = new Date()
      const formattedDate = `${now.getDate()}/${now.getMonth() + 1}/${now.getFullYear()} ເວລາ ${now.getHours()}:${String(now.getMinutes()).padStart(2, '0')} ${now.getHours() >= 12 ? 'PM' : 'AM'}`
      
      this.form.contactLogs.push({
        date: formattedDate,
        note: this.newContactNote,
      })
      this.newContactNote = ''
    },
    close() {
      this.show = false
      this.$nextTick(() => {
        this.form = this.emptyForm()
        this.newContactNote = ''
      })
    },
    save() {
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

.contact-logs-container {
  max-height: 100px;
  overflow-y: auto;
  border: 1px solid #e0e0e0;
}
</style>