<template>
  <v-row no-gutters class="login-page">

    <!-- =====================================================
         LEFT SIDE: SYSTEM INFORMATION
    ====================================================== -->
    <v-col
      cols="12"
      md="6"
      class="login-left d-flex flex-column align-center justify-center"
    >
      <div class="login-left-content">

        <!-- Logo + Brand -->
        <div class="d-flex align-center justify-center mb-4">
          <v-img
            src="/Homhuen-1.png"
            max-width="150"
            contain
            class="mr-3"
          ></v-img>

          <span class="brand-text">
            HomHuen
          </span>
        </div>

        <!-- Description -->
        <p class="subtitle-text text-center mb-8">
          ລະບົບຄຸ້ມຄອງທ້ອງແຖວສຳລັບ Admin
          ໃນການຈັດການທ້ອງເຊົ່າ,
          ຜູ້ເຊົ່າ ແລະ ລາຍງານ.
        </p>

        <!-- Features -->
        <div class="feature-list">

          <div class="feature-item">
            <span class="check-badge">
              <v-icon
                color="#0d3b73"
                x-small
              >
                mdi-check-bold
              </v-icon>
            </span>

            <span>
              ຈັດການທ້ອງແຖວແລະ ຜູ້ເຊົ່າ
            </span>
          </div>

          <div class="feature-item">
            <span class="check-badge">
              <v-icon
                color="#0d3b73"
                x-small
              >
                mdi-check-bold
              </v-icon>
            </span>

            <span>
              ຕິດຕາມການຊຳລະ ແລະ ໃບບິນ
            </span>
          </div>

          <div class="feature-item">
            <span class="check-badge">
              <v-icon
                color="#0d3b73"
                x-small
              >
                mdi-check-bold
              </v-icon>
            </span>

            <span>
              ລາຍງານການເຊົ່າ ແລະ ຊຳລະ
            </span>
          </div>

        </div>
      </div>

      <!-- Decorative Circle -->
      <div class="login-circle"></div>

    </v-col>


    <!-- =====================================================
         RIGHT SIDE: LOGIN FORM
    ====================================================== -->
    <v-col
      cols="12"
      md="6"
      class="d-flex align-center justify-center login-right"
    >

      <v-card
        class="pa-8 login-card"
        elevation="4"
        max-width="420"
        width="100%"
      >

        <!-- Login Header -->
        <div class="text-center mb-6">

          <h2 class="login-title">
            ເຂົ້າສູ່ລະບົບ
          </h2>

          <p class="login-subtitle">
            ຍິນດີຕ້ອນຮັບເຂົ້າສູ່ລະບົບ Admin
          </p>

        </div>


        <!-- =================================================
             LOGIN FORM
        ================================================== -->
        <v-form
          ref="form"
          @submit.prevent="handleLogin"
        >

          <!-- ================= PHONE ================= -->
          <div class="mb-1">

            <label class="field-label">
              Phone Number
            </label>

            <v-text-field
              v-model="phone"
              placeholder="020xxxxxxxx"
              prepend-inner-icon="mdi-phone-outline"
              outlined
              dense
              :rules="phoneRules"
              required
            />

          </div>


          <!-- ================= PASSWORD ================= -->
          <div class="mb-1">

            <label class="field-label">
              Password
            </label>

            <v-text-field
              v-model="password"
              placeholder="xxxxxxxxxx"
              prepend-inner-icon="mdi-lock-outline"
              :append-icon="
                showPassword
                  ? 'mdi-eye'
                  : 'mdi-eye-off'
              "
              :type="
                showPassword
                  ? 'text'
                  : 'password'
              "
              outlined
              dense
              :rules="passwordRules"
              required
              @click:append="
                showPassword = !showPassword
              "
            />

          </div>


          <!-- ================= ERROR MESSAGE ================= -->
          <v-alert
            v-if="errorMsg"
            type="error"
            dense
            class="mb-3"
          >
            {{ errorMsg }}
          </v-alert>


          <!-- ================= LOGIN BUTTON ================= -->
          <v-btn
            :loading="loading"
            :disabled="loading"
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
import axios from 'axios'
import Swal from 'sweetalert2'

export default {
  name: 'LoginPage',

  layout: 'auth',

  data() {
    return {
      // ================================
      // Form data
      // ================================
      phone: '',
      password: '',

      // ================================
      // UI state
      // ================================
      showPassword: false,
      loading: false,
      errorMsg: '',

      // ================================
      // Validation rules
      // ================================
      phoneRules: [
        v =>
          !!v ||
          'ກະລຸນາປ້ອນ ເບີໂທ'
      ],

      passwordRules: [
        v =>
          !!v ||
          'ກະລຸນາປ້ອນ Password'
      ]
    }
  },


  methods: {

    // =====================================================
    // LOGIN
    // =====================================================
    async handleLogin() {

      // -----------------------------------------------
      // 1. Validate form
      // Vuetify 2
      // -----------------------------------------------
      if (!this.$refs.form.validate()) {
        return
      }


      // -----------------------------------------------
      // 2. Start loading
      // -----------------------------------------------
      this.loading = true
      this.errorMsg = ''


      try {

        // ---------------------------------------------
        // 3. Send request to backend
        // ---------------------------------------------
        const response = await axios.post(
          'http://localhost:8000/api/admin/login',
          {
            phone: this.phone,
            password: this.password
          }
        )


        // ---------------------------------------------
        // 4. Debug response
        // ---------------------------------------------
        console.log(
          '===================================='
        )

        console.log(
          'Login response:',
          response.data
        )

        console.log(
          '===================================='
        )


        // ---------------------------------------------
        // 5. Check login success
        // ---------------------------------------------
        if (response.data.success) {

          // -------------------------------------------
          // Save token
          // -------------------------------------------
          localStorage.setItem(
            'token',
            response.data.token
          )


          // -------------------------------------------
          // Save user
          // -------------------------------------------
          localStorage.setItem(
            'user',
            JSON.stringify(
              response.data.user
            )
          )


          // -------------------------------------------
          // 6. Redirect to dashboard
          // -------------------------------------------
          this.$router.push('/')

        } else {

          // -------------------------------------------
          // Backend returned success = false
          // -------------------------------------------
          this.errorMsg =
            response.data.message ||
            'Phone ຫຼື Password ບໍ່ຖືກຕ້ອງ'
        }


      } catch (error) {

        // =============================================
        // ERROR HANDLING
        // =============================================

        console.error(
          'Login error:',
          error
        )


        // ---------------------------------------------
        // Backend returned an error
        // ---------------------------------------------
        if (error.response) {

          console.log(
            'Backend status:',
            error.response.status
          )

          console.log(
            'Backend data:',
            error.response.data
          )


          this.errorMsg =
            error.response.data?.message ||
            'Phone ຫຼື Password ບໍ່ຖືກຕ້ອງ'


        } else if (error.request) {

          // -------------------------------------------
          // Request sent but no response
          // -------------------------------------------
          this.errorMsg =
            'ບໍ່ສາມາດຮັບຂໍ້ມູນຈາກ Server ໄດ້'


        } else {

          // -------------------------------------------
          // Other error
          // -------------------------------------------
          this.errorMsg =
            'ບໍ່ສາມາດເຊື່ອມຕໍ່ກັບ Server ໄດ້'
        }


      } finally {

        // ---------------------------------------------
        // 7. Stop loading
        // ---------------------------------------------
        this.loading = false

      }
    }
  }
}
</script>


<style scoped>

/* =====================================================
   LOGIN PAGE
===================================================== */

.login-page {
  min-height: 100vh;
  margin: 0;
}


/* =====================================================
   LEFT SIDE
===================================================== */

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


/* =====================================================
   BRAND
===================================================== */

.brand-text {
  color: #ffffff;
  font-size: 28px;
  font-weight: 700;
}


/* =====================================================
   DESCRIPTION
===================================================== */

.subtitle-text {
  color: #d7e3f2;
  font-size: 20px;
  line-height: 1.6;
}


/* =====================================================
   FEATURES
===================================================== */

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


/* =====================================================
   CHECK ICON
===================================================== */

.check-badge {
  display: inline-flex;
  align-items: center;
  justify-content: center;

  width: 22px;
  height: 22px;
  min-width: 22px;

  background-color: #e8edf2;
  border-radius: 5px;

  margin-right: 10px;
}


/* =====================================================
   DECORATIVE CIRCLE
===================================================== */

.login-circle {
  position: absolute;

  bottom: -120px;
  right: -100px;

  width: 320px;
  height: 320px;

  border-radius: 50%;

  background-color: rgba(
    255,
    255,
    255,
    0.08
  );
}


/* =====================================================
   RIGHT SIDE
===================================================== */

.login-right {
  background-color: #f5f7fa;
  min-height: 100vh;
}


/* =====================================================
   LOGIN CARD
===================================================== */

.login-card {
  border-radius: 12px;
}


/* =====================================================
   LOGIN TITLE
===================================================== */

.login-title {
  color: #0d3b73;
  font-weight: 700;
  margin-bottom: 4px;
}


.login-subtitle {
  color: #7a8794;
  font-size: 13px;
}


/* =====================================================
   FORM LABEL
===================================================== */

.field-label {
  display: block;

  font-size: 14px;
  font-weight: 500;

  margin-bottom: 2px;

  color: #333333;
}

</style>