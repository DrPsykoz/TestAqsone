<template>
  <v-container>
    <!-- Animation chargement des données -->
    <v-overlay v-if="isSearching">
      <v-progress-circular
        indeterminate
        color="secondary"
      ></v-progress-circular>
    </v-overlay>

    <!-- Erreur lors de la recuperation des données -->
    <v-overlay v-if="error">
      <v-alert prominent type="error">
        <p>Merci de patienter avant d'afficher de nouvelles recettes.</p>
      </v-alert>
    </v-overlay>

    <!-- Barre de recherche -->
    <v-container class="d-inline-flex justify-center align-end">
      <v-text-field
        v-model="search"
        append-icon="search"
        label="Rechercher une recette"
        single-line
        hide-details
        v-on:keyup.enter="fetchData()"
      ></v-text-field>
      <v-btn class="ml-3" @click="fetchData()"> Rechercher </v-btn>
    </v-container>

    <!-- Parametres de la recherche -->
    <v-container fluid class="d-flex justify-space-around">
      <v-checkbox
        v-model="settings.alcoholFree"
        :label="`Recettes sans alcool`"
        @change="fetchData()"
      ></v-checkbox>
      <v-checkbox
        v-model="settings.glutenFree"
        :label="`Recettes sans gluten`"
        @change="fetchData()"
        disabled
      ></v-checkbox>
    </v-container>

    <!-- Affichage des resultats -->
    <v-container class="d-flex justify-center" fluid fill-height>
      <Recipe
        v-for="(recipe, id) in recipes"
        :key="'recipe-' + id"
        :recipe="recipe"
      />
      <v-alert prominent v-if="recipes.length === 0">
        <p>Aucun resultat trouvé</p>
      </v-alert>
    </v-container>

    <!-- Bouton pour afficher plus de resultats -->
    <v-container class="d-flex justify-center" style="flex-direction: column">
      <v-btn
        v-if="recipes.length != 0 && !searchDisabled"
        @click="fetchMoreData()"
        >Rechercher plus de recettes</v-btn
      >
    </v-container>
  </v-container>
</template>

<script>
const axios = require("axios");
import Recipe from "./Recipe.vue";
export default {
  components: {
    Recipe,
  },
  data() {
    return {
      app: {
        id: "535cb0a2",
        key: "2299a77198ee63113858b9cd4aadd1a6",
      },

      search: "",

      recipes: [],

      error: false,
      isSearching: false,

      settings: {
        alcoholFree: false,
        glutenFree: false,
        searchDisabled: true,
        maxDataPerSearch: 25,
      },
    };
  },
  methods: {
    getDataUrl() {
      let url = `https://api.edamam.com/search?q=${this.search}&app_id=${
        this.app.id
      }&app_key=${this.app.key}&from=${this.recipes.length}&to=${
        this.recipes.length + this.settings.maxDataPerSearch
      }`;

      if (this.settings.alcoholFree) url += "&health=alcohol-free";
      if (this.settings.glutenFree) url += "&health=gluten-free";

      return url;
    },
    getData() {
      return axios
        .get(this.getDataUrl())
        .then((response) => response.data.hits.map((x) => x.recipe));
    },
    fetchData() {
      if (this.recipes.search === "") return;
      this.isSearching = true;
      this.getData()
        .then((data) => {
          this.recipes = data;
          this.searchDisabled = false;
        })
        .catch((error) => {
          console.log(error);
          this.error = true;

          setTimeout(() => {
            this.error = false;
          }, 2000);
        })
        .finally(() => {
          this.isSearching = false;
        });
    },
    fetchMoreData() {
      this.isSearching = true;
      this.getData()
        .then((data) => {
          var sizeBeforeUpdate = this.recipes.length;

          // Verification que la liste des recettes ne contient
          // pas deja une des valeurs retourné par l'API
          var currentLinks = this.recipes.map((x) => x.url);
          var recipesData = data.filter((x) => !currentLinks.includes(x.url));

          this.recipes = [...this.recipes, ...recipesData];

          if (this.recipes.length === sizeBeforeUpdate) {
            this.searchDisabled = true;
          }
        })
        .catch((error) => {
          console.log(error);
          this.error = true;
          setTimeout(() => {
            this.error = false;
          }, 2000);
        })
        .finally(() => {
          this.isSearching = false;
        });
    },
  },
};
</script>

<style>
</style>