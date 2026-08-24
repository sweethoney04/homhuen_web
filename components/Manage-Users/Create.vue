<template>
  <v-dialog v-model="show" max-width="500px">
    <template v-slot:activator="{ on, attrs }">
      <v-btn color="primary" dark v-bind="attrs" v-on="on">
        ເພີ່ມ ພະນັກງານ
      </v-btn>
    </template>

    <v-card>
      <v-card-title>
        <span class="text-h6">ເພີ່ມ Banner</span>
      </v-card-title>

      <v-card-text>
        <v-container>
          <v-row>
            <v-col cols="12">
              <v-file-input
                v-model="form.imageFile"
                label="ຮູບ Banner"
                prepend-icon="mdi-image"
                accept="image/*"
                show-size
                @change="onImageChange"
              ></v-file-input>
              <v-img
                v-if="form.image"
                :src="form.image"
                max-height="140"
                contain
                class="mb-2 grey lighten-3"
              ></v-img>
            </v-col>

            <v-col cols="12">
              <v-text-field v-model="form.topic" label="Topic"></v-text-field>
            </v-col>

            <v-col cols="12">
              <v-text-field v-model="form.link" label="Link"></v-text-field>
            </v-col>

            <v-col cols="12" sm="6">
              <v-text-field
                v-model.number="form.order"
                type="number"
                label="ລຳດັບສະແດງ"
              ></v-text-field>
            </v-col>

            <v-col cols="12" sm="6" class="d-flex align-center">
              <span class="mr-3">ສະຖານະ</span>
              <v-switch v-model="form.active" color="success" hide-details inset></v-switch>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>

      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn text @click="close">Cancel</v-btn>
        <v-btn color="primary" text @click="save">Save</v-btn>
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
    }
  },
  methods: {
    emptyForm() {
      return {
        topic: '',
        link: '',
        order: 1,
        active: true,
        image: '',
        imageFile: null,
      }
    },
    onImageChange(file) {
      if (file) {
        this.form.image = URL.createObjectURL(file)
      }
    },
    close() {
      this.show = false
      this.$nextTick(() => {
        this.form = this.emptyForm()
      })
    },
    save() {
      // TODO: ຮ້ອງ API ສ້າງ Banner ໃໝ່ ຕົວຢ່າງ:
      // const { data } = await this.$axios.post('/banners', this.form)
      this.$emit('created', { ...this.form })
      this.close()
    },
  },
}
</script>