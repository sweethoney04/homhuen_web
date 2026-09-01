<!-- components/Banner/View.vue -->
<template>
  <v-data-table
    :headers="headers"
    :items="banners"
    :search="search"
    :loading="loading"
    sort-by="display_order"
    class="banner-table"
  >
    <template v-slot:item.no="{ item }">
      {{ banners.indexOf(item) + 1 }}
    </template>

    <template v-slot:item.image="{ item }">
      <v-img :src="item.image" width="70" height="45" class="rounded my-2" cover></v-img>
    </template>

    <template v-slot:item.link_url="{ item }">
      <a :href="item.link_url" target="_blank" class="link-text">{{ item.link_url }}</a>
    </template>

    <template v-slot:item.status="{ item }">
      <v-chip
        :color="item.status === 1 ? 'success' : 'grey lighten-1'"
        :text-color="item.status === 1 ? 'white' : 'black'"
        small
        label
        class="status-chip"
        @click="$emit('toggle-status', item)"
      >
        {{ item.status === 1 ? 'ເປີດໃຊ້ງານ' : 'ປິດ' }}
      </v-chip>
    </template>

    <template v-slot:item.edit="{ item }">
      <v-icon small class="mr-2" @click="$emit('edit-item', item)">mdi-pencil</v-icon>
    </template>

    <template v-slot:item.delete="{ item }">
      <v-icon small @click="$emit('delete-item', item)">mdi-delete</v-icon>
    </template>

    <template v-slot:no-data>
      <v-btn color="primary" @click="$emit('reset')">Reset</v-btn>
    </template>
  </v-data-table>
</template>

<script>
export default {
  name: 'BannerView',
  props: {
    banners: { type: Array, default: () => [] },
    search: { type: String, default: '' },
    loading: { type: Boolean, default: false },
  },
  data: () => ({
    headers: [
      { text: 'ລຳດັບ', value: 'no', sortable: false, width: '70' },
      { text: 'ຮູບ', value: 'image', sortable: false, width: '120' },
      { text: 'ຫົວຂໍ້', value: 'topic', sortable: false },
      { text: 'ລິ້ງ', value: 'link_url', sortable: false },
      { text: 'ລຳດັບ', value: 'display_order', sortable: false, width: '90' },
      { text: 'ສະຖານະ', value: 'status', sortable: false, align: 'center' },
      { text: 'ແກ້ໄຂ', value: 'edit', sortable: false, align: 'center' },
      { text: 'ລົບ', value: 'delete', sortable: false, align: 'center' },
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
  background-color: #064d8d !important;
  color: #ffffff !important;
}
</style>