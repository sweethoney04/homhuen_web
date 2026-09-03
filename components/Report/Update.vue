<template>
  <div v-if="visible" class="modal-overlay" @click.self="handleClose">
    <div class="modal-box">
      <!-- Header -->
      <div class="modal-header" >
        <h3 class="modal-title">{{ roomLabel }} - ລາຍລະອຽດຜູ້ເຊົ່າ</h3>
        <button class="close-btn" @click="handleClose" aria-label="close">×</button>
      </div>

      <form class="modal-form" @submit.prevent="handleSave">
        <!-- Section: Tenant info -->
        <p class="section-label">ຂໍ້ມູນຜູ້ເຊົ່າ</p>

        <div class="field-row">
          <div class="field">
            <label class="field-label">ຊື່</label>
            <div class="input-wrap">
              <v-icon class="input-icon" size="18">mdi-account</v-icon>
              <input v-model="form.name" type="text" placeholder="ຊື່" required />
            </div>
          </div>
          <div class="field">
            <label class="field-label">ນາມສະກຸນ</label>
            <div class="input-wrap">
              <input v-model="form.lastname" type="text" placeholder="ນາມສະກຸນ" required />
            </div>
          </div>
        </div>

        <div class="field-row">
          <div class="field">
            <label class="field-label">ເບີໂທລະສັບ</label>
            <div class="input-wrap">
              <v-icon class="input-icon" size="18">mdi-phone</v-icon>   
              <input
                v-model="form.tel"
                type="tel"
                placeholder="ເບີໂທ"
                required
              />
            </div>
          </div>

        </div>

        <!-- Section: Contract period -->
        <p class="section-label section-label--spaced">ໄລຍະສັນຍາ</p>

        <div class="field-row">
          <div class="field">
            <label class="field-label">ເລີ່ມແຕ່ວັນທີ</label>
            <div class="input-wrap">
              <input
                v-model="form.startDate"
                type="date"
                placeholder="DD/MM/YYYY"
                required
              />
            </div>
          </div>

          <div class="field">
            <label class="field-label">ຮອດສຸດວັນທີ</label>
            <div class="input-wrap">
              <input
                v-model="form.endDate"
                type="date"
                placeholder="DD/MM/YYYY"
                required
              />
            </div>
          </div>
        </div>

        <button type="submit" class="save-btn" :disabled="saving">
          {{ saving ? "ກຳລັງບັນທຶກ..." : "ບັນທຶກ" }}
        </button>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ReportUpdate',
  props: {
    visible: { type: Boolean, default: false },
    roomLabel: { type: String, default: '' },
    tenant: { type: Object, default: () => ({}) },
  },
  data() {
    return {
      saving: false,
      form: { name: '', lastname: '', tel: '', startDate: '', endDate: '' },
    }
  },
  watch: {
    tenant: {
      immediate: true,
      deep: true,
      handler(value) {
        this.form = {
          name: value.name || '',
          lastname: value.lastname || '',
          tel: value.tel || value.phone || '',
          startDate: value.startDate || '',
          endDate: value.endDate || '',
        }
      },
    },
  },
  methods: {
    handleClose() {
      this.$emit('close')
    },
    async handleSave() {
      this.saving = true
      try {
        this.$emit('save', { ...this.form })
      } finally {
        this.saving = false
      }
    },
  },
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.35);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-box {
  background: #fff;
  width: 100%;
  max-width: 420px;
  border-radius: 12px;
  box-shadow: 0 12px 32px rgba(0, 0, 0, 0.18);
  overflow: hidden;
}

.modal-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px 20px;
  background-color: #064d8d;
  border-bottom: 1px solid #eee;
}

.modal-title {
  margin: 0;
  font-size: 15px;
  font-weight: 600;
  color: #fff;
}

.close-btn {
  background: none;
  border: none;
  font-size: 20px;
  line-height: 1;
  color: #fff;
  cursor: pointer;
}
.close-btn:hover {
  color: #e0e0e0;
}

.modal-form {
  padding: 18px 20px 20px;
}

.section-label {
  font-size: 13px;
  font-weight: 600;
  color: #374151;
  margin: 0 0 10px;
}

.section-label--spaced {
  margin-top: 18px;
}

.field {
  flex: 1;
  margin-bottom: 14px;
}

.field-row {
  display: flex;
  gap: 12px;
}

.field-label {
  display: block;
  font-size: 12px;
  color: #6b7280;
  margin-bottom: 6px;
}

.input-wrap {
  display: flex;
  align-items: center;
  border: 1px solid #d1d5db;
  border-radius: 8px;
  padding: 0 10px;
  background: #f9fafb;
}

.input-wrap input,
.input-wrap select {
  border: none;
  background: transparent;
  outline: none;
  width: 100%;
  padding: 9px 6px;
  font-size: 13px;
  color: #111827;
}

.input-icon {
  font-size: 14px;
  opacity: 0.7;
}

.save-btn {
  width: 100%;
  margin-top: 8px;
  padding: 11px;
  background: #064D8D;
  color: #fff;
  border: none;
  border-radius: 8px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.15s ease;
}

.save-btn:hover:not(:disabled) {
  background: #1d4ed8;
}

.save-btn:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}
</style>