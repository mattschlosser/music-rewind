<template>
  <v-app>
    <v-navigation-drawer app v-model="drawer" dark>
      <v-list>
        <v-list-item link to="/">Home</v-list-item>
        <v-list-item link to="/about">About</v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <v-app-bar-nav-icon
        @click="drawer = !drawer"
      />
      <v-toolbar-title>
        Music Rewind 
      </v-toolbar-title>

      <!-- <v-spacer></v-spacer> -->
    </v-app-bar>

    <v-main>
      <router-view></router-view>
    </v-main>
    <v-footer class='text-center'>
      <v-btn
        href="https://mattschlosser.me/"
        target="_blank"
        text
        block
      >
        <span class="mr-2">A Matt Schlosser Project</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-footer>
  </v-app>
</template>

<script>

export default {
  name: 'App',

  data: () => ({
    //
    drawer: false
  }),
};
</script>
