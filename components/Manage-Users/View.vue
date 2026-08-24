<template>
  <v-data-table
    :headers="headers"
    :items="banners"
    :search="search"
    sort-by="order"
    class="banner-table"
  >
    <template v-slot:item.no="{ item }">
      {{ banners.indexOf(item) + 1 }}
    </template>

    <template v-slot:item.image="{ item }">
      <v-img
        :src="item.image"
        width="70"
        height="45"
        class="rounded my-2"
        cover
      ></v-img>
    </template>

    <template v-slot:item.link="{ item }">
      <a :href="item.link" target="_blank" class="link-text">{{ item.link }}</a>
    </template>

    <template v-slot:item.active="{ item }">
      <v-chip
        :color="item.active ? 'success' : 'grey lighten-1'"
        :text-color="item.active ? 'white' : 'black'"
        small
        label
        class="status-chip"
        @click="$emit('toggle-status', item)"
      >
        {{ item.active ? 'ເປີດໃຊ້ງານ' : 'ປິດ' }}
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
  name: 'ManageUsersView',
  props: {
    banners: {
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
      { text: 'ຊື່', value: 'name', sortable: false },
      { text: 'Phone Number', value: 'phoneNumber', sortable: false },
      { text: 'Email', value: 'email', sortable: false },
      { text: 'ຕຳແໜ່ງ', value: 'position', sortable: false },
      { text: 'ສະຖານະ', value: 'active' },
      { text: 'ແກ້ໄຂ', value: 'edit', sortable: false, align: 'center' },
      { text: 'ລິບ', value: 'delete', sortable: false, align: 'center' },
    ],
  }),
}
</script>

<style scoped>
.link-text {
  color: #1976d2;
  text-decoration: none;
}
.link-text:hover {
  text-decoration: underline;
}
.status-chip {
  cursor: pointer;
}
.banner-table >>> thead tr th {
  background-color: #1976d2 !important;
  color: #ffffff !important;
}
</style>