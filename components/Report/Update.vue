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

        <div class="field">
          <label class="field-label">ຊື່ ແລະ ນາມສະກຸນ</label>
          <div class="input-wrap">
            <v-icon class="input-icon" size="18">mdi-account</v-icon>
            <input
              v-model="form.fullName"
              type="text"
              placeholder="ຊື່ ແລະ ນາມສະກຸນ"
              required
            />
          </div>
        </div>

        <div class="field-row">
          <div class="field">
            <label class="field-label">ເບີໂທລະສັບ</label>
            <div class="input-wrap">
              <v-icon class="input-icon" size="18">mdi-phone</v-icon>   
              <input
                v-model="form.phone"
                type="tel"
                placeholder="ເບີໂທ"
                required
              />
            </div>
          </div>

          <div class="field">
            <label class="field-label">ເພດ</label>
            <div class="input-wrap">
              <select v-model="form.gender" required>
                <option value="" disabled>ເພດ</option>
                <option value="male">ຊາຍ</option>
                <option value="female">ຍິງ</option>
                <option value="other">ອື່ນໆ</option>
              </select>
            </div>
          </div>
        </div>

        <div class="field-row">
          <div class="field">
            <label class="field-label">ເລກບັດປະຈຳຕົວ</label>
            <div class="input-wrap">
              <v-icon class="input-icon" size="18">mdi-card-account-details</v-icon>
              <input
                v-model="form.idCardNumber"
                type="text"
                placeholder="ເລກບັດປະຈຳຕົວ"
                required
              />
            </div>
          </div>

          <div class="field">
            <label class="field-label">ວັນເດືອນປີເກີດ</label>
            <div class="input-wrap">
              <v-icon class="input-icon" size="18">mdi-calendar</v-icon>
              <input v-model="form.birthDate" type="date" required />
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

<script setup>
import { reactive, ref, watch } from "vue";

const props = defineProps({
  visible: { type: Boolean, default: false },
  roomLabel: { type: String, default: "" }, // e.g. "ຫ້ອງ A01"
  tenant: {
    type: Object,
    default: () => ({
      fullName: "",
      phone: "",
      gender: "",
      idCardNumber: "",
      birthDate: "",
      startDate: "",
      endDate: "",
    }),
  },
});

const emit = defineEmits(["close", "save"]);

const saving = ref(false);

const form = reactive({
  fullName: "",
  phone: "",
  gender: "",
  idCardNumber: "",
  birthDate: "",
  startDate: "",
  endDate: "",
});

// keep form in sync whenever a different tenant record is passed in
watch(
  () => props.tenant,
  (newVal) => {
    Object.assign(form, {
      fullName: newVal?.fullName ?? "",
      phone: newVal?.phone ?? "",
      gender: newVal?.gender ?? "",
      idCardNumber: newVal?.idCardNumber ?? "",
      birthDate: newVal?.birthDate ?? "",
      startDate: newVal?.startDate ?? "",
      endDate: newVal?.endDate ?? "",
    });
  },
  { immediate: true, deep: true }
);

function handleClose() {
  emit("close");
}

async function handleSave() {
  saving.value = true;
  try {
    // Replace with your real API call, e.g.:
    // await api.put(`/contracts/${props.tenant.id}`, { ...form })
    emit("save", { ...form });
  } finally {
    saving.value = false;
  }
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