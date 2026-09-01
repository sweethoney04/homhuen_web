<template>
  <v-dialog v-model="show" max-width="500px">
    <template v-slot:activator="{ on, attrs }">
      <v-btn color="#064D8D" dark v-bind="attrs" v-on="on">
        ເພີ່ມ User ໃໝ່
      </v-btn>
    </template>

    <v-card class="rounded-lg overflow-hidden pb-4">
      <!-- Header Bar -->
      <v-card-title
        class="white--text d-flex justify-space-between align-center px-6 py-3"
        style="background-color: #064d8d"
      >
        <span class="text-subtitle-1 font-weight-medium">
          <div>ລາຍລະອຽດພະນັກງານ</div></span>
        <v-btn icon dark small @click="close">
          <v-icon size="18">mdi-close</v-icon>
        </v-btn>
      </v-card-title>

      <!-- Form Content -->
      <v-card-text class="pt-4 px-6">
        <div class="text-subtitle-2 font-weight-bold mb-2 primary-text">
          <div>ຂໍ້ມູນ User</div></div>

        <v-container class="pa-0">
          <v-row dense>

            <v-col cols="12" class="mt-2">
              <div class="field-label">Phone</div>
              <v-text-field
                v-model="form.phone"
                dense
                outlined
                hide-details
                type="tel"
                placeholder="ເບີໂທລະສັບ"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <v-col cols="12" class="mt-2">
              <div class="field-label">Password</div>
              <v-text-field
                v-model="form.password"
                dense
                outlined
                hide-details
                type="password"
                placeholder="Password"
                class="custom-input"
              ></v-text-field>
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
          Submit
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
        username: '',
        phone: '',
        password: '',
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
      if (!this.form.username.trim() || !this.form.password.trim()) {
        alert('ກະລຸນາປ້ອນ Username ແລະ Password')
        return
      }

      this.$emit('created', { ...this.form })
      this.close()
    },
  },
}
</script>