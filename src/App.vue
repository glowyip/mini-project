<template>
  <v-app style="max-width:425px">
    <v-app-bar
      app
      absolute
      elevate-on-scroll
      scroll-target="#scrolling-techniques-7"
      color="#bd1569"
      dark
    >
      <div class="d-flex align-center">
        <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
        <v-toolbar-title>My Wallet</v-toolbar-title>
      </div>

      <v-spacer></v-spacer>

      <v-btn icon>
          <v-icon>mdi-magnify</v-icon>
      </v-btn>
      <v-btn icon>
          <v-icon>mdi-dots-vertical</v-icon>
      </v-btn>
    </v-app-bar>

    <v-navigation-drawer
      v-model="drawer"
      absolute
      bottom
      temporary
    >
      <v-list-item>
        <v-list-item-content>
          <v-list-item-title class="text-h6">
            Application
          </v-list-item-title>
          <v-list-item-subtitle>
            subtext
          </v-list-item-subtitle>
        </v-list-item-content>
      </v-list-item>

      <v-divider></v-divider>

      <!-- <v-list
        dense
        nav
      >
        <v-list-item
          v-for="item in items"
          :key="item.title"
          link
        >
          <v-list-item-icon>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list> -->
    </v-navigation-drawer>

    <v-main>
      <homeView></homeView>
    </v-main>
  </v-app>
</template>

<script>
import homeView from './components/mainView.vue'

export default {
  name: 'App',
  components :{

    homeView,
  },
  data: () => ({
    drawer :false,
  }),  
};
</script>
<style lang='sass'>
 $color-pack : false
 @import '~vuetify/src/styles/main.sass'
</style>
