<template>
  <v-dialog v-model="show" max-width="700px" persistent>
     <v-card class="rounded-lg overflow-hidden pb-4">
  <!-- Header Bar -->
  <v-card-title class="d-flex justify-space-between align-center px-6 pt-4 pb-2">
    <div>
      <div class="text-h6 font-weight-bold">
        <div>ເພີ່ມສັນຍາໃໝ່</div></div>
      <div class="text-caption grey--text"><div>ຕື່ມຂໍ້ມູນສັນຍາໃຫ້ຄົບຖ້ວນ</div></div>
    </div>
    <v-btn icon small @click="close">
      <v-icon size="18">mdi-close</v-icon>
    </v-btn>
  </v-card-title>

  <v-divider class="mx-6 mb-2"></v-divider>

  <!-- Form Content -->
  <v-card-text class="px-6 py-2">
    <v-container class="pa-0">
      <v-row dense>
        <!-- SECTION 1: ເລືອກຫ້ອງແຖວ -->
        <v-col cols="12">
          <div class="text-subtitle-2 font-weight-bold primary-text">
            <div>1. ເລືອກຫ້ອງແຖວ </div>
          </div>
        </v-col>

        <v-col cols="12" sm="6" class="pr-sm-2">
          <div class="field-label">ຊື່ຫ້ອງ</div>
          <v-text-field
            v-model="form.roomName"
            dense
            outlined
            hide-details
            placeholder="ຫ້ອງ A-301"
            class="custom-input"
          ></v-text-field>
        </v-col>

        <v-col cols="12" sm="6" class="pl-sm-2">
          <div class="field-label">ປະເພດຫ້ອງແຖວ</div>
          <v-select
            v-model="form.type"
            :items="typeOptions"
            dense
            outlined
            hide-details
            placeholder="ປະເພດຫ້ອງ"
            class="custom-input"
          ></v-select>
        </v-col>

        <v-col cols="12" sm="6" class="mt-2 pr-sm-2">
          <div class="field-label">ຄ່າເຊົ່າຕໍ່ເດືອນ</div>
          <v-text-field
            v-model="form.pricePerMonth"
            @input="formatPrice('pricePerMonth', $event)"
            dense
            outlined
            hide-details
            placeholder="1,500,000 LAK"
            class="custom-input"
          ></v-text-field>
        </v-col>

        <v-col cols="12" class="my-2">
          <v-divider></v-divider>
        </v-col>

        <!-- SECTION 2: ຂໍ້ມູນຜູ້ເຊົ່າ -->
        <v-col cols="12">
          <div class="text-subtitle-2 font-weight-bold primary-text">
            <div>2. ຂໍ້ມູນຜູ້ເຊົ່າ</div>
          </div>
        </v-col>

        <v-col cols="12" sm="6" class="pr-sm-2">
          <div class="field-label">ຊື່</div>
          <v-text-field
            v-model="form.firstName"
            dense
            outlined
            hide-details
            placeholder="Soupha"
            class="custom-input"
          ></v-text-field>
        </v-col>

        <v-col cols="12" sm="6" class="pl-sm-2">
          <div class="field-label">ນາມສະກຸນ</div>
          <v-text-field
            v-model="form.lastName"
            dense
            outlined
            hide-details
            placeholder="khamdee"
            class="custom-input"
          ></v-text-field>
        </v-col>

        <v-col cols="12" sm="6" class="mt-2 pr-sm-2">
          <div class="field-label">ເພດ</div>
          <v-select
            v-model="form.gender"
            :items="genderOptions"
            dense
            outlined
            hide-details
            placeholder="ເພດ"
            class="custom-input"
          ></v-select>
        </v-col>

        <v-col cols="12" sm="6" class="mt-2 pl-sm-2">
          <div class="field-label">ວັນເດືອນປີເກີດ</div>
          <v-text-field
            v-model="form.birthDate"
            dense
            outlined
            hide-details
            type="date"
            placeholder="11/04/2xxx"
            class="custom-input"
          ></v-text-field>
        </v-col>

        <v-col cols="12" sm="6" class="mt-2 pr-sm-2">
          <div class="field-label">ເບີໂທລະສັບ</div>
          <v-text-field
            v-model="form.phoneNumber"
            dense
            outlined
            hide-details
            type="tel"
            placeholder="020 xxxxxxxx"
            class="custom-input"
          ></v-text-field>
        </v-col>

        <v-col cols="12" sm="6" class="mt-2 pl-sm-2">
          <div class="field-label">ເລກບັດປະຈຳຕົວ / Passport</div>
          <v-text-field
            v-model="form.idNumber"
            dense
            outlined
            hide-details
            placeholder="1xxxxxxxxxxxx"
            class="custom-input"
          ></v-text-field>
        </v-col>

        <v-col cols="12" class="my-2">
          <v-divider></v-divider>
        </v-col>

        <!-- SECTION 3: ໄລຍະສັນຍາ ແລະ ຄ່າເຊົ່າ -->
        <v-col cols="12">
          <div class="text-subtitle-2 font-weight-bold primary-text">
            <div>3. ໄລຍະສັນຍາ ແລະ ຄ່າເຊົ່າ</div>
          </div>
        </v-col>

        <v-col cols="12" sm="6" class="pr-sm-2">
          <div class="field-label">ວັນທີເລີ່ມສັນຍາ</div>
          <v-text-field
            v-model="form.dateStart"
            dense
            outlined
            hide-details
            type="date"
            placeholder="DD/MM/YYYY"
            class="custom-input"
          ></v-text-field>
        </v-col>

        <v-col cols="12" sm="6" class="pl-sm-2">
          <div class="field-label">ວັນທີສິ້ນສຸດສັນຍາ</div>
          <v-text-field
            v-model="form.dateEnd"
            dense
            outlined
            hide-details
            type="date"
            placeholder="DD/MM/YYYY"
            class="custom-input"
          ></v-text-field>
        </v-col>

        <v-col cols="12" sm="6" class="mt-2 pr-sm-2">
          <div class="field-label">ເງິນມັດຈຳ</div>
          <v-text-field
            v-model="form.deposit"
            @input="formatPrice('deposit', $event)"
            dense
            outlined
            hide-details
            placeholder="0 LAK"
            class="custom-input"
          ></v-text-field>
        </v-col>

        <v-col cols="12" sm="6" class="mt-2 pl-sm-2">
          <div class="field-label">ຮູບແບບການຊຳລະ</div>
          <v-select
            v-model="form.paymentMethod"
            :items="paymentOptions"
            dense
            outlined
            hide-details
            placeholder="ຮູບແບບການຊຳລະ"
            class="custom-input"
          ></v-select>
        </v-col>

        <v-col cols="12" class="mt-2">
          <div class="field-label">ໝາຍເຫດ</div>
          <v-textarea
            v-model="form.note"
            outlined
            rows="3"
            hide-details
            placeholder="ໝາຍເຫດ..."
            class="custom-input"
          ></v-textarea>
        </v-col>
      </v-row>
    </v-container>
  </v-card-text>

  <!-- Action Footer -->
  <v-card-actions class="px-6 pt-3 pb-2 justify-end">
    <v-btn
      outlined
      color="grey darken-1"
      class="rounded-lg text-none font-weight-medium px-6 mr-2"
      @click="close"
    >
      ຍົກເລີກ
    </v-btn>
    <v-btn
      color="#064D8D"
      dark
      depressed
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
  name: 'ReportCreate',
  data() {
    return {
      show: false,
      typeOptions: [
        { text: 'ຫ້ອງນ້ອຍ', value: 'small' },
        { text: 'ຫ້ອງໃຫຍ່', value: 'large' },
      ],
      genderOptions: ['ຊາຍ', 'ຍິງ'],
      paymentOptions: ['ເງິນສົດ', 'ໂອນເງິນ', 'ບັດເຄຣດິດ'],
      form: this.emptyForm(),
    };
  },
  methods: {
    formatPrice(field, value) {
      const digits = String(value || '').replace(/\D/g, '')
      this.form[field] = digits.replace(/\B(?=(\d{3})+(?!\d))/g, ',')
    },
    emptyForm() {
      return {
        roomName: '',
        type: '',
        pricePerMonth: '',
        firstName: '',
        lastName: '',
        gender: '',
        birthDate: '',
        phoneNumber: '',
        idNumber: '',
        dateStart: '',
        dateEnd: '',
        deposit: '',
        paymentMethod: '',
        note: '',
      }
    },
    close() {
      this.show = false
      this.$nextTick(() => {
        this.form = this.emptyForm()
      })
    },
    save() {
      const roomName = this.form.roomName ? this.form.roomName.trim() : ''
      const firstName = this.form.firstName ? this.form.firstName.trim() : ''
      const lastName = this.form.lastName ? this.form.lastName.trim() : ''

      if (!roomName || !firstName) {
        return
      }

      const roomPrice = Number(String(this.form.pricePerMonth || '').replace(/,/g, '')) || 0
      const record = {
        id: Date.now(),
        roomId: Date.now(),
        name: roomName,
        room: roomName,
        lessee: `${firstName} ${lastName}`.trim(),
        leaseTime: [this.form.dateStart, this.form.dateEnd].filter(Boolean).join(' - ') || '—',
        overdue: 0,
        status: 0,
        roomPrice,
        waterPrice: 0,
        electricityPrice: 0,
        wasteFees: 0,
        total: roomPrice,
        payment: roomPrice,
        details: this.form.note || `${this.form.dateStart} - ${this.form.dateEnd}`,
        leaseContract: `LC-${Date.now()}`,
        firstName,
        lastName,
        phoneNumber: this.form.phoneNumber || '',
        ...this.form,
      }

      this.$emit('created', record)
      this.close()
    },
  },
}
</script>

<style scoped>
.main-image-box {
  border: 1px dashed #bdbdbd;
  border-radius: 8px;
  height: 220px;
  cursor: pointer;
  background: #fafafa;
}
.sub-image-box {
  border: 1px dashed #bdbdbd;
  border-radius: 8px;
  height: 90px;
  cursor: pointer;
  background: #fafafa;
}
.icon-upload-box {
  border: 1px dashed #bdbdbd;
  border-radius: 6px;
  height: 44px;
  width: 44px;
  cursor: pointer;
  background: #fafafa;
  margin-top: 2px;
}
.label-text {
  font-size: 13px;
  color: #616161;
  margin-bottom: 4px;
}
.add-btn {
  min-width: 90px;
  background-color: #fff !important;
  font-weight: 500;
  margin-top: 24px;
}
</style>

