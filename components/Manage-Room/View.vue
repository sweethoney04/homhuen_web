<template>
  <v-data-table
    :headers="headers"
    :items="rooms"
    :search="search"
    class="customer-table"
  >
    <template v-slot:item.no="{ item }">
      {{ rooms.indexOf(item) + 1 }}
    </template>

    <template v-slot:item.image="{ item }">
      <v-img
        v-if="item.image"
        :src="item.image || item.cover || item.imageRoom"
        width="70"
        height="45"
        contain
        class="my-2"
      ></v-img>
      <span v-else>-</span>
    </template>

    <template v-slot:item.roomName="{ item }">
      {{ item.roomName || item.name || '-' }}
    </template>

    <template v-slot:item.type="{ item }">
      {{ item.type || item.roomType || '-' }}
    </template>

    <template v-slot:item.pricePerMonth="{ item }">
      {{ item.pricePerMonth || item.price || '-' }}
    </template>

    <template v-slot:item.description="{ item }">
      {{ item.description || item.descriptions || '-' }}
    </template>

    <template v-slot:item.status="{ item }">
      <v-chip
        :color="statusColor(item.status)"
        text-color="white"
        small
        label
      >
        {{ item.status }}
      </v-chip>
    </template>

    <template v-slot:item.edit="{ item }">
      <v-icon small class="mr-2" @click="$emit('edit-item', item)">
        mdi-pencil
      </v-icon>
    </template>

    <template v-slot:item.delete="{ item }">
      <v-icon small @click="$emit('delete-item', item)">
        mdi-delete
      </v-icon>
    </template>

    <template v-slot:no-data>
      <v-btn color="primary" @click="$emit('reset')">Reset</v-btn>
    </template>
  </v-data-table>
</template>

<script>
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
    statusColor(status) {
      if (status === 'available') return 'success'
      if (status === 'unavailable') return 'error'
      return 'primary'
    },
  },
}
</script>

<style scoped>
.customer-table >>> thead tr th {
  background-color: #064d8d !important;
  color: #ffffff !important;
}
</style>