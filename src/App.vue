<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <h2>Test Aqsone VueJS + Vuetify</h2>
    </v-app-bar>

    <v-main>
      <Recipes />
    </v-main>
  </v-app>
</template>

<script>
import Recipes from "./components/Recipes";

export default {
  name: 'App',

  components: {
    Recipes,
  },
};
</script>
