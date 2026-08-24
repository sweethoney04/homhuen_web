<template>
  <v-row no-gutters class="login-page">
    <!-- ============ ຝັ່ງຊ້າຍ: ຂໍ້ມູນແນະນຳລະບົບ ============ -->
    <v-col cols="12" md="6" class="login-left d-flex flex-column align-center justify-center">
      <div class="login-left-content">
        <div class="d-flex align-center justify-center mb-4">
          <v-img
            src="/Homhuen-1.png"
            max-width="150"
            contain
            class="mr-3"
          ></v-img>
          <span class="brand-text">HomHuen</span>
        </div>

        <p class="subtitle-text text-center mb-8">
          ລະບົບຄຸ້ມຄອງທ້ອງແຖວສຳລັບ Admin ໃນການຈັດການທ້ອງເຊົ່າ,
          ຜູ້ເຊົ່າ ແລະ ລາຍງານ.
        </p>

        <div class="feature-list">
          <div class="feature-item">
            <span class="check-badge"><v-icon color="#0d3b73" x-small>mdi-check-bold</v-icon></span>
            <span>ຈັດການທ້ອງແຖວແລະ ຜູ້ເຊົ່າ</span>
          </div>
          <div class="feature-item">
            <span class="check-badge"><v-icon color="#0d3b73" x-small>mdi-check-bold</v-icon></span>
            <span>ຕິດຕາມການຊຳລະ ແລະ ໃບບິນ</span>
          </div>
          <div class="feature-item">
            <span class="check-badge"><v-icon color="#0d3b73" x-small>mdi-check-bold</v-icon></span>
            <span>ລາຍງານການເຊົ່າ ແລະ ຊຳລະ</span>
          </div>
        </div>
      </div>

      <div class="login-circle"></div>
    </v-col>

    <!-- ============ ຝັ່ງຂວາ: ຟອມ Login ============ -->
    <v-col cols="12" md="6" class="d-flex align-center justify-center login-right">
      <v-card class="pa-8 login-card" elevation="4" max-width="420" width="100%">
        <div class="text-center mb-6">
          <h2 class="login-title">ເຂົ້າສູ່ລະບົບ</h2>
          <p class="login-subtitle">ຍິນດີຕ້ອນຮັບເຂົ້າສູ່ລະບົບ Admin</p>
        </div>

        <v-form ref="form" @submit.prevent="handleLogin">
          <div class="mb-1">
            <label class="field-label">Phone Number</label>
            <v-text-field
              v-model="phone"
              placeholder="020xxxxxxxx"
              prepend-inner-icon="mdi-email-outline"
              outlined
              dense
              :rules="[v => !!v || 'ກະລຸນາປ້ອນ ເບີໂທ']"
              required
            />
          </div>

          <div class="mb-1">
            <label class="field-label">Password</label>
            <v-text-field
              v-model="password"
              placeholder="xxxxxxxxxx"
              prepend-inner-icon="mdi-lock-outline"
              :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
              :type="showPassword ? 'text' : 'password'"
              outlined
              dense
              :rules="[v => !!v || 'ກະລຸນາປ້ອນ Password']"
              required
              @click:append="showPassword = !showPassword"
            />
          </div>

          <v-alert v-if="errorMsg" type="error" dense class="mb-3">
            {{ errorMsg }}
          </v-alert>

          <v-btn
            :loading="loading"
            color="#0d3b73"
            dark
            block
            large
            depressed
            type="submit"
          >
            ເຂົ້າສູ່ລະບົບ
          </v-btn>
        </v-form>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
import axios from 'axios' // ຫຼືຖ້າໃຊ້ Nuxt ສາມາດໃຊ້ this.$axios ໄດ້ເລີຍ

export default {
  name: 'LoginPage',
  layout: 'auth',
  data() {
    return {
      phone: '',
      password: '',
      showPassword: false,
      loading: false,
      errorMsg: '',
    }
  },
  methods: {
    async handleLogin() {
      // 1. ກວດສອບ Validation ຂອງ Form
      if (!this.$refs.form.validate()) return

      this.loading = true
      this.errorMsg = ''

      try {
        // 2. send request API to backend 
        const response = await axios.post('http://localhost:8000/admin/login', {
          phone: this.phone,
          password: this.password
        })
        

        // if (response.data.success) {
        //   // 3. ເກັບ Token ໄວ້ localstorage (ຫຼື Cookie / Vuex)
        //   localStorage.setItem('token', response.data.token)
        //   localStorage.setItem('user', JSON.stringify(response.data.user))

        //   // 4. Redirect ໄປໜ້າ Dashboard
        //   this.$router.push('/')
        // }
      } catch (error) {
        // 5. ຈັດການ Error response
        if (error.response && error.response.data) {
          this.errorMsg = error.response.data.message || 'Phone ຫຼື Password ບໍ່ຖືກຕ້ອງ'
        } else {
          this.errorMsg = 'ບໍ່ສາມາດເຊື່ອມຕໍ່ກັບ Server ໄດ້'
        }
      } finally {
        this.loading = false
      }
    },
  },
}
</script>

<style scoped>
.login-page {
  min-height: 100vh;
  margin: 0;
}

.login-left {
  background-color: #0d3b73;
  min-height: 100vh;
  position: relative;
  overflow: hidden;
  padding: 40px;
}

.login-left-content {
  max-width: 420px;
  z-index: 2;
}

.brand-text {
  color: #ffffff;
  font-size: 28px;
  font-weight: 700;
}

.subtitle-text {
  color: #d7e3f2;
  font-size: 20px;
  line-height: 1.6;
}

.feature-list {
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.feature-item {
  display: flex;
  align-items: center;
  color: #ffffff;
  font-size: 16px;
}

.login-circle {
  position: absolute;
  bottom: -120px;
  right: -100px;
  width: 320px;
  height: 320px;
  border-radius: 50%;
  background-color: rgba(255, 255, 255, 0.08);
}

.login-right {
  background-color: #f5f7fa;
  min-height: 100vh;
}

.login-card {
  border-radius: 12px;
}

.login-title {
  color: #0d3b73;
  font-weight: 700;
  margin-bottom: 4px;
}

.login-subtitle {
  color: #7a8794;
  font-size: 13px;
}

.field-label {
  display: block;
  font-size: 14px;
  font-weight: 500;
  margin-bottom: 2px;
  color: #333333;
}

.check-badge {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 22px;
  height: 22px;
  min-width: 22px;
  background-color: #E8EDF2;
  border-radius: 5px;
  margin-right: 10px;
}
</style>