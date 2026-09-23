<template>
  <v-data-table
    :headers="headers"
    :items="rooms"
    :search="search"
    :custom-filter="filterRooms"
    :loading="loading"
    class="customer-table"
  >
    <template v-slot:item.no="{ item }">
      {{ rooms.indexOf(item) + 1 }}
    </template>

    <template v-slot:item.image="{ item }">
      <v-img
        v-if="item.cover || item.image || item.imageRoom"
        :src="formatImageUrl(item.cover || item.image || item.imageRoom)"
        width="70"
        height="45"
        contain
        class="my-2"
      ></v-img>
      <span v-else>-</span>
    </template>

    <template v-slot:item.roomName="{ item }">
      {{ item.name || item.roomName || '-' }}
    </template>

    <!-- ປະເພດຫ້ອງ (0: ຫ້ອງນ້ອຍ, 1: ຫ້ອງໃຫຍ່) -->
    <template v-slot:item.type="{ item }">
      <span v-if="item.roomType === 1 || item.type === 'large'">ຫ້ອງໃຫຍ່</span>
      <span v-else-if="item.roomType === 0 || item.type === 'small'">ຫ້ອງນ້ອຍ</span>
      <span v-else>{{ item.type || '-' }}</span>
    </template>

    <!-- ສະຖານະການເປີດ/ວ່າງ (available) -->
    <template v-slot:item.status="{ item }">
      <v-chip
        :color="item.available ? 'success' : 'error'"
        text-color="white"
        small
        label
      >
        {{ item.available ? 'ເປີດ / ວ່າງ' : 'ປິດ / ບໍ່ວ່າງ' }}
      </v-chip>
    </template>

    <template v-slot:item.pricePerMonth="{ item }">
      {{ formatPrice(item.price || item.pricePerMonth) }} ກີບ
    </template>

    <template v-slot:item.description="{ item }">
      <span class="description-text">{{ formatDescription(item) }}</span>
    </template>

    <template v-slot:item.edit="{ item }">
      <v-icon small class="mr-2" color="primary" @click="$emit('edit-item', item)">
        mdi-pencil
      </v-icon>
    </template>

    <template v-slot:item.delete="{ item }">
      <v-icon small color="error" @click="$emit('delete-item', item)">
        mdi-delete
      </v-icon>
    </template>
  </v-data-table>
</template>

<script>
const API_BASE = 'http://localhost:8000'

export default {
  name: 'ManageRoomView',
  props: {
    rooms: {
      type: Array,
      default: () => [],
    },
    search: {
      type: String,
      default: '',
    },
    loading: {
      type: Boolean,
      default: false,
    },
  },
  data: () => ({
    headers: [
      { text: 'ລຳດັບ', value: 'no', sortable: false, width: '70' },
      { text: 'ຮູບຫ້ອງ', value: 'image', sortable: false },
      { text: 'ຊື່ຫ້ອງ', value: 'roomName', sortable: false },
      { text: 'ປະເພດ', value: 'type', sortable: false },
      { text: 'ສະຖານະ', value: 'status' },
      { text: 'ຄ່າເຊົ່າ / ເດືອນ', value: 'pricePerMonth', sortable: false },
      { text: 'ລາຍລະອຽດ', value: 'description', sortable: false },
      { text: 'ແກ້ໄຂ', value: 'edit', sortable: false, align: 'center' },
      { text: 'ລົບ', value: 'delete', sortable: false, align: 'center' },
    ],
  }),
  methods: {
    filterRooms(value, search, item) {
      if (!search) return true

      const keyword = String(search).trim().toLowerCase()
      if (!keyword) return true

      const roomType =
        item.roomType === 1 || item.roomType === '1' || item.type === 'large'
          ? 'large ຫ້ອງໃຫຍ່'
          : 'small ຫ້ອງນ້ອຍ'
      const availability = item.available ? 'available ເປີດ ວ່າງ' : 'unavailable ປິດ'
      const searchableText = [
        item.name,
        item.roomName,
        item.description,
        item.descriptions,
        item.detail,
        item.details,
        item.price,
        item.pricePerMonth,
        roomType,
        availability,
      ]
        .filter((field) => field !== null && field !== undefined)
        .join(' ')
        .toLowerCase()

      return searchableText.includes(keyword)
    },
    formatImageUrl(path) {
      if (!path) return ''
      if (/^https?:\/\//.test(path) || path.startsWith('blob:')) return path
      return API_BASE + path
    },
    formatPrice(val) {
      if (!val) return '0'
      return Number(val).toLocaleString()
    },
    formatDescription(item) {
      const description =
        item.description ?? item.descriptions ?? item.detail ?? item.details
      return description && String(description).trim() ? description : '-'
    },
  },
}
</script>

<style scoped>
.customer-table >>> thead tr th {
  background-color: #064d8d !important;
  color: #ffffff !important;
}

.description-text {
  display: inline-block;
  white-space: nowrap;
  width: 50px;
  overflow: hidden;
  text-overflow: ellipsis;
  vertical-align: middle;
}
</style>