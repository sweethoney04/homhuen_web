<template>
  <v-dialog v-model="show" max-width="700px" persistent>
    <template v-slot:activator="{ on, attrs }">
      <v-btn color="#064D8D" dark v-bind="attrs" v-on="on">
        ເພີ່ມຫ້ອງແຖວ
      </v-btn>
    </template>

    <v-card>
      <v-card-title
        class="white--text d-flex justify-space-between align-center"
        style="background-color: #064d8d"
      >
        <span class="text-h6"> <div>ເພີ່ມ / ແກ້ໄຂ ຫ້ອງແຖວ</div></span>
        <v-btn icon dark @click="close">
          <v-icon>mdi-close</v-icon>
        </v-btn>
      </v-card-title>

      <v-card-text class="pt-4">
        <v-container>
          <!-- ຮູບຫຼັກ -->
          <v-row>
            <v-col cols="12">
              <div
                class="main-image-box d-flex align-center justify-center"
                @click="$refs.mainImageInput.click()"
              >
                <v-img
                  v-if="form.image"
                  :src="form.image"
                  height="220"
                  contain
                  class="rounded"
                />
                <div v-else class="text-center grey--text">
                  <v-icon size="40">mdi-image-plus</v-icon>
                  <div>ອັບໂຫລດຮູບຫຼັກ</div>
                </div>
                <input
                  ref="mainImageInput"
                  type="file"
                  accept="image/*"
                  class="d-none"
                  @change="onImageChange($event, 'image')"
                />
              </div>
            </v-col>
          </v-row>

          <!-- ຮູບຍ່ອຍ 3 ຮູບ -->
          <v-row>
            <v-col v-for="(img, idx) in form.subImages" :key="idx" cols="4">
              <div
                class="sub-image-box d-flex align-center justify-center"
                @click="$refs['subImageInput' + idx][0].click()"
              >
                <v-img
                  v-if="img"
                  :src="img"
                  height="90"
                  contain
                  class="rounded"
                />
                <v-icon v-else color="grey">mdi-image-plus</v-icon>
                <input
                  :ref="'subImageInput' + idx"
                  type="file"
                  accept="image/*"
                  class="d-none"
                  @change="onSubImageChange($event, idx)"
                />
              </div>
            </v-col>
          </v-row>

          <!-- ຂໍ້ມູນຫ້ອງ -->
          <v-row class="mt-2">
            <v-col cols="12">
              <div class="text-subtitle-1 font-weight-bold">
                <div>ຂໍ້ມູນຫ້ອງ</div>
              </div>
            </v-col>

            <v-col cols="12" sm="6">
              <div class="label-text">ຊື່ຫ້ອງແຖວ</div>
              <v-text-field
                v-model="form.roomName"
                dense
                outlined
                hide-details
                placeholder="ຊື່ຫ້ອງແຖວ"
              ></v-text-field>
            </v-col>

            <v-col cols="12" sm="6">
              <div class="label-text">ຄ່າເຊົ່າຕໍ່ເດືອນ</div>
              <v-text-field
                v-model="form.pricePerMonth"
                @input="formatPrice"
                dense
                outlined
                hide-details
                suffix="ກີບ"
                placeholder="0"
              ></v-text-field>
            </v-col>

            <v-col cols="12" sm="6">
              <div class="label-text">ສະຖານະ</div>
              <v-select
                v-model="form.availability"
                :items="availabilityOptions"
                item-text="text"
                item-value="value"
                dense
                outlined
                hide-details
                placeholder="ເປີດ / ວ່າງ"
              ></v-select>
            </v-col>

            <v-col cols="12" sm="6">
              <div class="label-text">ປະເພດຫ້ອງ</div>
              <v-select
                v-model="form.type"
                :items="typeOptions"
                item-text="text"
                item-value="value"
                dense
                outlined
                hide-details
                placeholder="ຫ້ອງນ້ອຍ / ຫ້ອງໃຫຍ່"
              ></v-select>
            </v-col>

            <v-col cols="12" sm="4">
              <div class="label-text">ຂະໜາດ</div>
              <v-text-field
                v-model="form.size"
                dense
                outlined
                hide-details
                placeholder="ຂະໜາດຫ້ອງ"
              ></v-text-field>
            </v-col>

            <v-col cols="12" sm="4">
              <div class="label-text">ຈຳນວນຫ້ອງນອນ</div>
              <v-text-field
                v-model="form.bedrooms"
                type="number"
                min="0"
                dense
                outlined
                hide-details
                placeholder="0"
              ></v-text-field>
            </v-col>

            <v-col cols="12" sm="4">
              <div class="label-text">ຈຳນວນຫ້ອງນ້ຳ</div>
              <v-text-field
                v-model="form.bathrooms"
                type="number"
                min="0"
                dense
                outlined
                hide-details
                placeholder="0"
              ></v-text-field>
            </v-col>

            <v-col cols="12">
              <div class="label-text-description">ລາຍລະອຽດ</div>
              <v-textarea
                v-model="form.description"
                outlined
                dense
                rows="4"
                hide-details
                placeholder="ລາຍລະອຽດຫ້ອງແຖວ..."
              ></v-textarea>
            </v-col>

            <!-- Icon + ຊື່ + Add (ລາຍການ facility/amenity) -->
            <v-col cols="12">
              <v-row align="center" class="mt-2" no-gutters>
                <v-col cols="auto" class="mr-4">
                  <div class="label-text">Icon</div>
                  <div
                    class="icon-upload-box d-flex align-center justify-center"
                    @click="$refs.iconInput.click()"
                  >
                    <v-img
                      v-if="newFeature.icon"
                      :src="newFeature.icon"
                      height="28"
                      width="28"
                      contain
                    />
                    <v-icon v-else color="grey" size="22"
                      >mdi-paperclip-plus</v-icon
                    >
                    <input
                      ref="iconInput"
                      type="file"
                      accept="image/*"
                      class="d-none"
                      @change="onIconChange"
                    />
                  </div>
                </v-col>

                <v-col class="mr-4">
                  <div class="label-text">ຊື່</div>
                  <v-text-field
                    v-model="newFeature.name"
                    dense
                    outlined
                    hide-details
                    height="44"
                  ></v-text-field>
                </v-col>

                <v-col cols="auto">
                  <v-btn
                    outlined
                    color="#064D8D"
                    height="44"
                    class="add-btn"
                    @click="addFeature"
                  >
                    Add
                  </v-btn>
                </v-col>
              </v-row>

              <!-- ລາຍການ features ທີ່ Add ແລ້ວ -->
              <v-chip
                v-for="(f, i) in form.features"
                :key="i"
                class="mt-2 mr-2"
                close
                @click:close="removeFeature(i)"
              >
                <v-avatar left v-if="f.icon">
                  <v-img :src="f.icon"></v-img>
                </v-avatar>
                {{ f.name }}
              </v-chip>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>

      <v-card-actions class="pb-4 px-5">
        <v-spacer></v-spacer>
        <v-btn
          color="#064D8D"
          dark
          style="width: 150px; height: 42px"
          :loading="loading"
          @click="save"
        >
          Save
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  name: "ManageRoomCreate",
  data() {
    return {
      show: false,
      loading: false,
      newFeature: {
        icon: "",
        iconFile: null,
        name: "",
      },
      // "ວ່າງ / ບໍ່ວ່າງ" maps to the backend's `available` field.
      // This is intentionally separate from the backend's `status` field,
      // which tracks paid/unpaid billing state and is managed elsewhere.
      availabilityOptions: [
        { text: "ເປີດ / ວ່າງ", value: "available" },
        { text: "ປິດ / ບໍ່ວ່າງ", value: "unavailable" },
      ],
      typeOptions: [
        { text: "ຫ້ອງນ້ອຍ", value: "small" },
        { text: "ຫ້ອງໃຫຍ່", value: "large" },
      ],
      form: this.emptyForm(),
    };
  },
  methods: {
    formatPrice(value) {
      const digits = String(value || "").replace(/\D/g, "");
      this.form.pricePerMonth = digits.replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },
    emptyForm() {
      return {
        roomName: "",
        pricePerMonth: "",
        availability: "available",
        type: "",
        size: "",
        bedrooms: "",
        bathrooms: "",
        description: "",
        image: "",
        imageFile: null,
        subImages: ["", "", ""],
        subImageFiles: [null, null, null],
        features: [],
      };
    },
    onImageChange(e, key) {
      const file = e.target.files[0];
      if (file) {
        this.form[key] = URL.createObjectURL(file);
        this.form.imageFile = file;
      }
    },
    onSubImageChange(e, idx) {
      const file = e.target.files[0];
      if (file) {
        this.$set(this.form.subImages, idx, URL.createObjectURL(file));
        this.form.subImageFiles[idx] = file;
      }
    },
    onIconChange(e) {
      const file = e.target.files[0];
      if (file) {
        this.newFeature.icon = URL.createObjectURL(file);
        this.newFeature.iconFile = file;
      }
    },
    addFeature() {
      if (!this.newFeature.name) return;
      this.form.features.push({ ...this.newFeature });
      this.newFeature = { icon: "", iconFile: null, name: "" };
    },
    removeFeature(i) {
      this.form.features.splice(i, 1);
    },
    close() {
      this.show = false;
      this.$nextTick(() => {
        this.form = this.emptyForm();
      });
    },
    async save() {
      try {
        if (!this.form.roomName || !this.form.roomName.trim()) {
          alert("ກະລຸນາປ້ອນຊື່ຫ້ອງແຖວ");
          return;
        }

        this.loading = true;

        const formData = new FormData();
        formData.append("name", this.form.roomName.trim());
        formData.append(
          "price",
          this.form.pricePerMonth.replace(/,/g, "") || "0"
        );
        const description = this.form.description || "";
        formData.append("description", description);
        formData.append("descriptions", description);
        // Backend expects roomType as 0 (small) or 1 (large), not a string.
        formData.append("roomType", this.form.type === "large" ? "1" : "0");
        formData.append("size", this.form.size || "");
        formData.append("bedrooms", this.form.bedrooms || "0");
        formData.append("bathrooms", this.form.bathrooms || "0");
        // Availability maps to the `available` field, kept separate from
        // the backend's billing `status` field.
        formData.append(
          "available",
          this.form.availability === "available" ? "true" : "false"
        );

        if (this.form.imageFile) {
          formData.append("cover", this.form.imageFile);
        }

        if (this.form.subImageFiles && this.form.subImageFiles.length > 0) {
          this.form.subImageFiles.forEach((file) => {
            if (file) {
              formData.append("images", file);
            }
          });
        }

        // Send feature names + a hasIcon flag so the backend can line up
        // uploaded icon files (only features with an icon add one) with
        // the right feature, in order.
        const featuresMeta = this.form.features.map((f) => ({
          name: f.name,
          hasIcon: false,
        }));
        formData.append("features", JSON.stringify(featuresMeta));

        if (!localStorage.getItem("token")) {
          throw new Error("Authentication token is missing");
        }

        // Let Axios/browser set the multipart boundary automatically.
        const response = await this.$axios.post("/admin/rooms", formData);

        if (response.data.success) {
          this.$emit("created", response.data.data || response.data);
          this.close();
        } else {
          throw new Error(response.data.message || "Room could not be saved");
        }
      } catch (error) {
        console.error("Save room error:", error);
        alert(
          error.response?.status === 401
            ? "Session expired. Please log in again."
            : error.response?.data?.message ||
                error.message ||
                "ເກີດຂໍ້ຜິດພາດໃນການບັນທຶກຂໍ້ມູນ"
        );
      } finally {
        this.loading = false;
      }
    },
  },
};
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

  .label-text-description {
    font-size: 13px;
    color: #616161;
    margin-bottom: 4px;
    white-space: nowrap;
    width: 150px;
    overflow: hidden;
    text-overflow: ellipsis;
  }
}
.add-btn {
  min-width: 90px;
  background-color: #fff !important;
  font-weight: 500;
  margin-top: 24px;
}
</style>
