<template>
  <v-dialog v-model="show" max-width="700px" persistent>
    <v-card>
      <v-card-title
        class="white--text d-flex justify-space-between align-center"
        style="background-color: #064d8d"
      >
        <span class="text-h6">ແກ້ໄຂ ຫ້ອງແຖວ</span>
        <v-btn icon dark @click="close">
          <v-icon>mdi-close</v-icon>
        </v-btn>
      </v-card-title>

      <v-card-text class="pt-4">
        <v-container>
          <!-- ຮູບຫຼັກ (Cover / Main Image) -->
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

          <!-- ຮູບຍ່ອຍ 3 ຮູບ (Sub Images) -->
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

          <!-- ຂໍ້ມູນຫ້ອງ (Room Details) -->
          <v-row class="mt-2">
            <v-col cols="12">
              <div class="text-subtitle-1 font-weight-bold">ຂໍ້ມູນຫ້ອງ</div>
            </v-col>

            <!-- ຊື່ຫ້ອງແຖວ -->
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

            <!-- ຄ່າເຊົ່າຕໍ່ເດືອນ -->
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

            <!-- ສະຖານະ (Availability) -->
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

            <!-- ປະເພດຫ້ອງ (Room Type) -->
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

            <!-- ລາຍລະອຽດ (Description) -->
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

            <!-- Icon + ຊື່ + Add (Add Features / Amenities) -->
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

              <!-- Feature Chips -->
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
// Backend serves static files at this domain
const API_BASE = 'http://localhost:8000'

function toAbsoluteUrl(path) {
  if (!path) return ''
  if (/^https?:\/\//.test(path) || path.startsWith('blob:')) return path
  return API_BASE + (path.startsWith('/') ? path : '/' + path)
}

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
      loading: false,
      newFeature: {
        icon: '',
        iconFile: null,
        name: '',
      },
      availabilityOptions: [
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
    item: {
      handler(val) {
        this.form = this.buildForm(val || {})
      },
      deep: true,
      immediate: true,
    },
  },
  methods: {
    formatPrice(value) {
      const digits = String(value || '').replace(/\D/g, '')
      this.form.pricePerMonth = digits.replace(/\B(?=(\d{3})+(?!\d))/g, ',')
    },
    buildForm(item) {
      // Map roomType (1 or 'large') and available (true, 1, or 'available')
      const isLarge =
        item.roomType === 1 || item.roomType === '1' || item.type === 'large'
      const isAvailable =
        item.available === true ||
        item.available === 1 ||
        item.available === '1' ||
        item.availability === 'available'

      const existingImages = item.images || item.subImages || []
      const filledImages = [
        existingImages[0] ? toAbsoluteUrl(existingImages[0]) : '',
        existingImages[1] ? toAbsoluteUrl(existingImages[1]) : '',
        existingImages[2] ? toAbsoluteUrl(existingImages[2]) : '',
      ]

      return {
        id: item.id ?? null,
        roomName: item.name ?? item.roomName ?? '',
        pricePerMonth: item.price ?? item.pricePerMonth ?? '',
        availability: isAvailable ? 'available' : 'unavailable',
        type: isLarge ? 'large' : 'small',
        description: item.descriptions ?? item.description ?? '',
        image: toAbsoluteUrl(item.cover ?? item.image ?? item.imageRoom ?? ''),
        imageFile: null,
        subImages: filledImages,
        subImageFiles: [null, null, null],
        features: (item.features || []).map((f) => ({
          name: f.name,
          icon: toAbsoluteUrl(f.icon),
          serverIcon: f.icon || null, // Preserve relative path for backend
          iconFile: null,
        })),
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
      if (!this.newFeature.name.trim()) return
      this.form.features.push({
        name: this.newFeature.name.trim(),
        icon: this.newFeature.icon,
        serverIcon: null,
        iconFile: this.newFeature.iconFile,
      })
      this.newFeature = { icon: '', iconFile: null, name: '' }
    },
    removeFeature(i) {
      this.form.features.splice(i, 1)
    },
    close() {
      this.show = false
    },
    async save() {
      if (!this.form.id) {
        console.error('Update called without a valid room ID')
        return
      }

      if (!this.form.roomName || !this.form.roomName.trim()) {
        alert('ກະລຸນາປ້ອນຊື່ຫ້ອງແຖວ')
        return
      }

      try {
        this.loading = true

        const formData = new FormData()
        formData.append('name', this.form.roomName.trim())
        formData.append('price', String(this.form.pricePerMonth || '0').replace(/,/g, ''))
        formData.append('descriptions', this.form.description || '')
        formData.append('roomType', this.form.type === 'large' ? '1' : '0')
        formData.append(
          'available',
          this.form.availability === 'available' ? 'true' : 'false'
        )

        // Upload new cover image if updated
        if (this.form.imageFile) {
          formData.append('cover', this.form.imageFile)
        }

        // Upload new sub-images if updated
        this.form.subImageFiles.forEach((file) => {
          if (file) {
            formData.append('images', file)
          }
        })

        // Build features metadata for backend parseFeatures()
        const featuresMeta = this.form.features.map((f) => ({
          name: f.name,
          hasIcon: !!f.iconFile,
          existingIcon: f.iconFile ? null : f.serverIcon || null,
        }))
        formData.append('features', JSON.stringify(featuresMeta))

        // Append new feature icon files
        this.form.features.forEach((f) => {
          if (f.iconFile) {
            formData.append('featureIcons', f.iconFile)
          }
        })

        const response = await this.$axios.put(
          `/admin/rooms/${this.form.id}`,
          formData,
          {
            headers: {
              'Content-Type': 'multipart/form-data',
            },
          }
        )

        if (response.data.success) {
          this.$emit('updated')
          this.close()
        } else {
          throw new Error(response.data.message || 'Could not update room')
        }
      } catch (error) {
        console.error('Update room error:', error)
        alert(
          error.response?.data?.message || 'ເກີດຂໍ້ຜິດພາດໃນການແກ້ໄຂຂໍ້ມູນ'
        )
      } finally {
        this.loading = false
      }
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