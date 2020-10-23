<template>
  <v-app>

    <v-app-bar app hide-on-scroll>
      <v-toolbar-title >
        Log Daily
      </v-toolbar-title>
    </v-app-bar>

    <v-main>
      <v-container fluid>
        <router-view/>
      </v-container>
    </v-main>

    <v-footer app>
      Log Daily - Jordan Newlands 
    </v-footer>


  </v-app>
</template>
