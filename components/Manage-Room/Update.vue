<template>
  <v-dialog v-model="show" max-width="700px" persistent>
    <v-card>
      <v-card-title
        class="white--text d-flex justify-space-between align-center"
        style="background-color: #064D8D;"
      >
        <span class="text-h6">ແກ້ໄຂ ຫ້ອງແຖວ</span>
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
            <v-col
              v-for="(img, idx) in form.subImages"
              :key="idx"
              cols="4"
            >
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
              <div class="text-subtitle-1 font-weight-bold">ຂໍ້ມູນຫ້ອງ</div>
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
                dense
                outlined
                hide-details
                suffix="ກີບ"
                type="number"
                placeholder="0"
              ></v-text-field>
            </v-col>

            <v-col cols="12" sm="6">
              <div class="label-text">ສະຖານະ</div>
              <v-select
                v-model="form.status"
                :items="statusOptions"
                item-text="text"
                item-value="value"
                dense
                outlined
                hide-details
                placeholder="ວ່າງ / ບໍ່ວ່າງ"
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
                placeholder="ຫ້ອງນອນນ້ອຍ / ຫ້ອງນອນໃຫຍ່"
              ></v-select>
            </v-col>

            <v-col cols="12">
              <div class="label-text">ລາຍລະອຽດ</div>
              <v-textarea
                v-model="form.description"
                outlined
                dense
                rows="4"
                hide-details
                placeholder="ລາຍລະອຽດຫ້ອງແຖວ..."
              ></v-textarea>
            </v-col>

            <!-- Icon + ຊື່ + Add -->
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
                    <v-icon v-else color="grey" size="22">mdi-image-plus</v-icon>
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
          style="width: 193px; height: 52px;"
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
  name: 'ManageRoomUpdate',
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
      newFeature: {
        icon: '',
        iconFile: null,
        name: '',
      },
      statusOptions: [
        { text: 'ເປີດ / ວ່າງ', value: 'available' },
        { text: 'ປິດ / ບໍ່ວ່າງ', value: 'unavailable' },
      ],
      typeOptions: [
        { text: 'ຫ້ອງນ້ອຍ', value: 'small' },
        { text: 'ຫ້ອງໃຫຍ່', value: 'large' },
      ],
      form: this.buildForm(this.item),
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
      this.form = this.buildForm(val)
    },
  },
  methods: {
    buildForm(item) {
      return {
        id: item.id ?? null,
        roomName: item.roomName ?? item.name ?? '',
        pricePerMonth: item.pricePerMonth ?? item.price ?? '',
        status: item.status ?? '',
        type: item.type ?? item.roomType ?? '',
        description: item.description ?? item.descriptions ?? '',
        image: item.image ?? item.cover ?? item.imageRoom ?? '',
        imageFile: null,
        subImages: item.subImages || item.images
          ? [...(item.subImages || item.images)]
          : ['', '', ''],
        subImageFiles: [null, null, null],
        features: item.features ? [...item.features] : [],
      }
    },
    onImageChange(e, key) {
      const file = e.target.files[0]
      if (file) {
        this.form[key] = URL.createObjectURL(file)
        this.form.imageFile = file
      }
    },
    onSubImageChange(e, idx) {
      const file = e.target.files[0]
      if (file) {
        this.$set(this.form.subImages, idx, URL.createObjectURL(file))
        this.form.subImageFiles[idx] = file
      }
    },
    onIconChange(e) {
      const file = e.target.files[0]
      if (file) {
        this.newFeature.icon = URL.createObjectURL(file)
        this.newFeature.iconFile = file
      }
    },
    addFeature() {
      if (!this.newFeature.name) return
      this.form.features.push({ ...this.newFeature })
      this.newFeature = { icon: '', iconFile: null, name: '' }
    },
    removeFeature(i) {
      this.form.features.splice(i, 1)
    },
    close() {
      this.show = false
    },
    save() {
      // TODO: ຮ້ອງ API ອັບເດດຂໍ້ມູນຫ້ອງແຖວ ຕົວຢ່າງ:
      // await this.$axios.put(`/rooms/${this.form.id}`, this.form)
      this.$emit('updated', { ...this.form })
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
.add-btn {
  min-width: 90px;
  background-color: #fff !important;
  font-weight: 500;
}
.label-text {
  font-size: 13px;
  color: #616161;
  margin-bottom: 4px;
}
</style>