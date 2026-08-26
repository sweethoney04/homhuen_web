<template>
  <v-data-table
    :headers="headers"
    :items="customers"
    :search="search"
    :loading="loading"
    class="customer-table"
  >
    <template v-slot:item.no="{ item }">
      {{ customers.indexOf(item) + 1 }}
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
  name: 'CustomerView',
  props: {
    customers: {
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
      { text: 'ຊື່ລູກຄ້າ', value: 'customerName' },
      { text: 'ເບີໂທ', value: 'phoneNumber', sortable: false },
      { text: 'ຫ້ອງທີ່ສົນໃຈ', value: 'roomInterested' },
      { text: 'ຊ່ອງທາງທີ່ຕິດຕໍ່ເຂົ້າມາ', value: 'channel', sortable: false },
      { text: 'ທີ່ຕິດຕໍ່ຄັ້ງທຳອິດ', value: 'firstContactDate' },
      { text: 'ນັດໝາຍຄັ້ງຕໍ່ໄປ', value: 'nextAppointmentDate' },
      { text: 'ຜູ້ຮັບຜິດຊອບ', value: 'responsible' },
      { text: 'ສະຖານະ', value: 'status' },
      { text: 'ແກ້ໄຂ', value: 'edit', sortable: false, align: 'center' },
      { text: 'ລິບ', value: 'delete', sortable: false, align: 'center' },
    ],
  }),
  methods: {
    statusColor(status) {
      if (status === 'ກຳລັງຕິດຕໍ່') return 'success'
      if (status === 'ປິດການຂາຍແລ້ວ') return 'amber darken-2'
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