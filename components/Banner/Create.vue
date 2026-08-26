<template>
  <v-dialog v-model="show" max-width="650px" persistent>
    <template v-slot:activator="{ on, attrs }">
      <v-btn color="#064D8D" dark v-bind="attrs" v-on="on">
        ເພີ່ມ Banner
      </v-btn>
    </template>

    <v-card class="rounded-lg overflow-hidden pb-4">
      <!-- Header Bar -->
      <v-card-title
        class="white--text d-flex justify-space-between align-center px-6 py-3"
        style="background-color: #064d8d"
      >
        <span class="text-subtitle-1 font-weight-medium">ເພີ່ມ / ແກ້ໄຂ Banner</span>
        <v-btn icon dark small @click="close">
          <v-icon size="18">mdi-close</v-icon>
        </v-btn>
      </v-card-title>

      <!-- Form Content -->
      <v-card-text class="pt-6 px-6">
        <v-container class="pa-0">
          <!-- Image Upload Area -->
          <v-row dense>
            <v-col cols="12">
              <div
                class="image-upload-box d-flex flex-column align-center justify-center"
                @click="$refs.imageInput.click()"
              >
                <v-img
                  v-if="form.image"
                  :src="form.image"
                  max-height="160"
                  contain
                  class="rounded"
                />
                <div v-else class="text-center caption-text d-flex align-center justify-center">
                  <v-icon color="#9E9E9E" class="mr-2" size="20">mdi-image-outline</v-icon>
                  <span>ອັບໂຫລດຮູບພາບ (ແນະນຳ 1600×600 px)</span>
                </div>
                <input
                  ref="imageInput"
                  type="file"
                  accept="image/*"
                  class="d-none"
                  @change="onImageChange"
                />
              </div>
            </v-col>
          </v-row>

          <!-- Form Inputs -->
          <v-row class="mt-4" dense>
            <!-- 1. Banner Title & Link -->
            <v-col cols="12" sm="6" class="pr-sm-2">
              <div class="field-label">ຊື່ຫົວຂໍ້ Banner</div>
              <v-text-field
                v-model="form.topic"
                dense
                outlined
                hide-details
                placeholder="ຊື່ຫົວຂໍ້ Banner"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <v-col cols="12" sm="6" class="pl-sm-2">
              <div class="field-label">Link</div>
              <v-text-field
                v-model="form.link"
                dense
                outlined
                hide-details
                type="url"
                placeholder="link"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <!-- 2. Display Order & Status -->
            <v-col cols="12" sm="6" class="mt-3 pr-sm-2">
              <div class="field-label">ລຳດັບການສະແດງ</div>
              <v-text-field
                v-model.number="form.order"
                dense
                outlined
                hide-details
                type="number"
                placeholder="1"
                class="custom-input"
              ></v-text-field>
            </v-col>

            <v-col cols="12" sm="6" class="mt-3 pl-sm-2">
              <div class="field-label">ສະຖານະ</div>
              <v-select
                v-model="form.active"
                :items="[
                  { text: 'ເປີດໃຊ້ງານ', value: true },
                  { text: 'ປິດໃຊ້ງານ', value: false }
                ]"
                item-text="text"
                item-value="value"
                dense
                outlined
                hide-details
                class="custom-input"
              ></v-select>
            </v-col>

            <!-- 3. Banner Type -->
            <v-col cols="12" class="mt-3">
              <div class="field-label">Banner Type</div>
              <v-select
                v-model="form.type"
                :items="typeOptions"
                item-text="text"
                item-value="value"
                dense
                outlined
                hide-details
                class="custom-input"
              ></v-select>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>

      <!-- Action Footer -->
      <v-card-actions class="px-6 pt-2 pb-2">
        <v-spacer></v-spacer>
        <v-btn
          color="#064D8D"
          dark
          depressed
          class="px-8 rounded-lg text-none font-weight-regular"
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
  name: "BannerCreate",
  data() {
    return {
      show: false,
      form: this.emptyForm(),
      typeOptions: [
        { text: "ຂະໜາດນ້ອຍ (Small)", value: 0 },
        { text: "ຂະໜາດໃຫຍ່ (Large)", value: 1 },
      ],
    };
  },
  methods: {
    emptyForm() {
      return {
        id: null,
        topic: "",
        link: "",
        order: 1,
        type: 0,
        active: true,
        image: "",
        imageFile: null,
      };
    },
    onImageChange(event) {
      const file = event.target.files[0];
      if (file) {
        this.form.image = URL.createObjectURL(file);
        this.form.imageFile = file;
      }
    },
    close() {
      this.show = false;
      this.$nextTick(() => {
        this.form = this.emptyForm();
      });
    },
    save() {
      if (!this.form.topic || !this.form.link) {
        this.$emit("error", "Please fill in all required fields");
        return;
      }
      this.$emit("created", { ...this.form });
      this.close();
    },
  },
};
</script>

<style scoped>
.image-upload-box {
  border: 1px dashed #bdbdbd;
  border-radius: 8px;
  height: 180px;
  cursor: pointer;
  background-color: #fafafa;
  transition: all 0.2s ease-in-out;
}

.image-upload-box:hover {
  border-color: #064d8d;
  background-color: #f4f7fa;
}

.caption-text {
  font-size: 13px;
  color: #9e9e9e;
}

.field-label {
  font-size: 13px;
  color: #424242;
  margin-bottom: 6px;
  font-weight: 400;
}

/* Customize Vuetify inputs layout to match Figma rounded style */
::v-deep .custom-input .v-input__control .v-input__slot {
  min-height: 40px !important;
  border-radius: 6px;
  background-color: #ffffff !important;
}

::v-deep .v-text-field--outlined fieldset {
  border-color: #e0e0e0;
}

::v-deep .custom-input .v-select__selections {
  font-size: 13px;
}

::v-deep .custom-input input {
  font-size: 13px;
}
</style>