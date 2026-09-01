<template>
  <v-app>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      color="white"
      fixed
      app
      width="300"
    >
      <div class="sidebar-logo d-flex align-center px-4 py-4">
        <v-img src="/Homhuen-1.png" max-width="60" contain class="mr-2"></v-img>
        <span class="brand-text">HomHuen</span>
      </div>

      <v-divider></v-divider>

      <v-list nav dense class="pa-2">
        <template v-for="(item, i) in items">
          <v-list-group
            v-if="item.children"
            :key="'group-' + i"
            no-action
          >
            <template v-slot:activator>
              <v-list-item-icon>
                <v-img
                  v-if="isSvgIcon(item.icon)"
                  :src="item.icon"
                  max-width="40"
                  max-height="40"
                  contain
                />
                <v-icon v-else size="24">{{ item.icon }}</v-icon>
              </v-list-item-icon>
              <v-list-item-title>{{ item.title }}</v-list-item-title>
            </template>

            <v-list-item
              v-for="(child, ci) in item.children"
              :key="'child-' + ci"
              :to="child.to"
              link
              router
              exact
              class="sidebar-item sidebar-subitem"
              active-class="sidebar-item--active"
            >
              <v-list-item-icon>
                <v-img
                  v-if="isSvgIcon(child.icon)"
                  :src="child.icon"
                  max-width="40"
                  max-height="40"
                  contain
                />
                <v-icon v-else size="24">{{ child.icon }}</v-icon>
              </v-list-item-icon>
              <v-list-item-title>{{ child.title }}</v-list-item-title>
            </v-list-item>
          </v-list-group>

          <v-list-item
            v-else
            :key="'item-' + i"
            :to="item.to"
            link
            router
            exact
            class="sidebar-item"
            active-class="sidebar-item--active"
          >
            <v-list-item-icon>
              <v-img
                v-if="isSvgIcon(item.icon)"
                :src="item.icon"
                max-width="40"
                max-height="40"
                contain
              />
              <v-icon v-else size="24">{{ item.icon }}</v-icon>
            </v-list-item-icon>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item>
        </template>
      </v-list>

      <template v-slot:append>
        <v-divider></v-divider>
        <v-list nav dense class="pa-2">
          <v-list-item link class="sidebar-item" @click="handleLogout">
            <v-list-item-icon>
              <v-icon size="24">mdi-logout</v-icon>
            </v-list-item-icon>
            <v-list-item-title>Logout</v-list-item-title>
          </v-list-item>
        </v-list>
      </template>
    </v-navigation-drawer>

    <v-app-bar :clipped-left="clipped" color="white" elevation="1" fixed app>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-spacer />

      <v-btn icon class="mr-2">
        <v-icon>mdi-bell-outline</v-icon>
      </v-btn>

      <v-divider vertical class="mx-3" inset></v-divider>

      <v-avatar size="36" class="mr-2">
        <v-img :src="user.avatar || defaultAvatar"></v-img>
      </v-avatar>

      <div class="user-info mr-2">
        <div class="user-name">{{ user.name }}</div>
        <div class="user-role">{{ user.role }}</div>
      </div>
    </v-app-bar>

    <v-main class="app-main">
      <v-container fluid class="pa-6">
        <Nuxt />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data() {
    return {
      clipped: false,
      drawer: true,
      miniVariant: false,
      defaultAvatar: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQ6nBO8Qw1Qq4sJHuWaTnOsMUIZFA4scakzfvlI8f6yPqfNgIyLpnpqicg&s=10',
      user: {
        name: 'Honey Chitlatda',
        role: 'Admin',
        avatar: '',
      },

      items: [
        { icon: 'mdi-view-dashboard', title: 'Dashboard', to: '/' },
        { icon: 'mdi-account-multiple', title: 'ຈັດການພະນັກງານ', to: '/ManageUsers' },
        { icon: 'mdi-account-circle', title: 'ຈັດການລູກຄ້າ', to: '/ManageCustomer' },
        { icon: 'mdi-file-document', title: 'ລາຍງານຫ້ອງ', to: '/report/reportSell' },
        {
          icon: 'mdi-web',
          title: 'ຈັດການເວັບໄຊ',
          children: [
            { icon: 'mdi-door-sliding', title: 'ຈັດການຫ້ອງ', to: '/ManageRoom' },
            { icon: 'mdi-map', title: 'About Us', to: '/AboutUs' },
            { icon: 'mdi-application-outline', title: 'ຈັດການໜ້າ Banner', to: '/Banner' },
            { icon: 'mdi-message-processing-outline', title: 'ຈັດການຂໍ້ມູນຕິດຕໍ່', to: '/CustomerContact' },
          ],
        },
      ],
    }
  },
  methods: {
    isSvgIcon(icon) {
      return typeof icon === 'string' && icon.toLowerCase().endsWith('.svg')
    },
    handleLogout() {
      this.$router.push('/login')
    },
  },
}
</script>

<style scoped>
.app-main {
  background-color: #f5f7fa;
}

.sidebar-logo {
  background-color: #ffffff;
  justify-content: center;
}

.brand-text {
  font-size: 22px;
  font-weight: 700;
  color: #0d3b73;
}

/* ✅main and child */
.sidebar-item,
.sidebar-subitem {
  border-radius: 8px;
  margin-bottom: 10px;
  min-height: 48px;
  padding: 0 16px;
}

.sidebar-subitem {
  margin-bottom: 6px;
}

/* ✅white active */
.sidebar-item--active {
  background-color: #0d3b73 !important;
  color: #ffffff !important;
}

/* a distance between icon and text */
.sidebar-item ::v-deep .v-list-item__icon,
.sidebar-subitem ::v-deep .v-list-item__icon {
  margin-right: 16px !important;
  align-items: center;
  margin-left: 15px;
}

/* font-size */
.sidebar-item ::v-deep .v-list-item__title {
  font-size: 15px !important;
  font-weight: 500;
  margin-left: 16px !important;
}

.sidebar-subitem ::v-deep .v-list-item__title {
  font-size: 15px !important;
  font-weight: 400;
}

/* ✅ Indent ໃຫ້ເມນູລູກ (submenu) ຢູ່ຫ່າງຈາກຂອບຊ້າຍ */
::v-deep .v-list-group__items .sidebar-subitem {
  padding-left: 40px !important;
}

/* website-manage icon */
::v-deep .v-list-group__header.v-list-item {
  border-radius: 8px;
  min-height: 48px;
  padding: 0 16px;
  margin-left: 15px;
}

/* blue icon */
.sidebar-item .v-icon,
.sidebar-subitem .v-icon {
  color: #0d3b73 !important;
}

/* icon when it is active */
.sidebar-item--active .v-icon {
  color: #ffffff !important;
}

/* drop down icon*/
::v-deep .v-list-group__header .v-icon {
  color: #0d3b73 !important;
}

.user-info {
  line-height: 1.2;
}

.user-name {
  font-weight: 600;
  font-size: 14px;
  color: #1a1a1a;
}

.user-role {
  font-size: 12px;
  color: #7a8794;
}
::v-deep .v-list-group__header .v-list-item__title {
  font-size: 15px !important;
  font-weight: 500;
}
</style>