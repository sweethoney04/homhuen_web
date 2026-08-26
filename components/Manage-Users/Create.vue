<template>
  <v-dialog v-model="show" max-width="500px">
    <template v-slot:activator="{ on, attrs }">
      <v-btn color="#064D8D" dark v-bind="attrs" v-on="on">
        ເພີ່ມ ພະນັກງານ
      </v-btn>
    </template>

    <v-card class="rounded-lg overflow-hidden pb-4">
      <!-- Header Bar -->
      <v-card-title
        class="white--text d-flex justify-space-between align-center px-6 py-3"
        style="background-color: #064d8d"
      >
        <span class="text-subtitle-1 font-weight-medium">ລາຍລະອຽດພະນັກງານ</span>
        <v-btn icon dark small @click="close">
          <v-icon size="18">mdi-close</v-icon>
        </v-btn>
      </v-card-title>

      <!-- Form Content -->
      <v-card-text class="pt-4 px-6">
        <div class="text-subtitle-2 font-weight-bold mb-2 primary-text">ຂໍ້ມູນພະນັກງານ</div>

        <v-container class="pa-0">
          <v-row dense>
            <!-- 1. Full Name (Full Width) -->
            <v-col cols="12">
              <div class="field-label">ຊື່ ແລະ ນາມສະກຸນ</div>
              <v-text-field
                v-model="form.EmployeeName"
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
              <div class="field-label">ເພດ</div>
              <v-text-field
                v-model="form.gender"
                dense
                outlined
                hide-details
                type="gender"
                placeholder="ເພດ"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <v-col cols="12" sm="6" class="mt-2 pl-sm-2">
              <div class="field-label">ວັນເດືອນປີເກີດ</div>
              <v-text-field
                v-model="form.dateOfBirth"
                dense
                outlined
                hide-details
                type="date"
                placeholder="DD/MM/YYYY"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <!-- 3. Interested Room (Full Width) -->
            <v-col cols="12" class="mt-2">
              <div class="field-label">ເບີໂທລະສັບ</div>
              <v-text-field
                v-model="form.phoneNumber"
                dense
                outlined
                hide-details
                type="tel"
                placeholder="ເບີໂທລະສັບ"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <v-col cols="12" class="mt-2">
              <div class="field-label">ເລກປະຈຳຕົວ / Passport</div>
              <v-text-field
                v-model="form.idNumber"
                dense
                outlined
                hide-details
                type="text"
                placeholder="ເລກປະຈຳຕົວ / Passport"
                class="custom-input"
              ></v-text-field>
            </v-col>
             <v-col cols="12" class="mt-2">
              <div class="field-label">Email</div>
              <v-text-field
                v-model="form.email"
                dense
                outlined
                hide-details
                type="email"
                placeholder="Email"
                class="custom-input"
              ></v-text-field>
            </v-col>
            <v-col cols="12" class="mt-2">
              <div class="field-label">ຕຳແໜ່ງ</div>
              <v-text-field
                v-model="form.position"
                dense
                outlined
                hide-details
                type="text"
                placeholder="ຕຳແໜ່ງ"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <v-col cols="12" class="mt-2">
              <v-switch
                v-model="form.status"
                inset
                color="success"
                hide-details
                :label="form.status ? 'Active' : 'Inactive'"
              ></v-switch>
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
  name: 'ManageUsersCreate',
  data() {
    return {
      show: false,
      form: this.emptyForm(),
      channelOptions: ['Website', 'Facebook', 'WhatsApp', 'Call', 'Walk-in'],
      newContactNote: '',
    }
  },
  methods: {
    emptyForm() {
      return {
        EmployeeName: '',
        gender: '',
        dateOfBirth: '',
        phoneNumber: '',
        idNumber: '',
        email: '',
        position: '',
        status: true,
      }
    },
    addContactNote() {
      if (this.newContactNote.trim()) {
        this.form.contactLogs.push({
          date: new Date().toLocaleDateString('lo-LA'),
          note: this.newContactNote.trim(),
        })
        this.newContactNote = ''
      }
    },
    close() {
      this.show = false
      this.$nextTick(() => {
        this.form = this.emptyForm()
      })
    },
    save() {
      if (!this.form.EmployeeName.trim()) {
        alert('ກະລຸນາປ້ອນຊື່-ນາມສະກຸນ')
        return
      }

      this.$emit('created', { ...this.form })
      this.close()
    },
  },
}
</script>