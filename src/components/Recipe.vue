<template>
  <v-hover v-slot="{ hover }">
    <v-card elevation="2" width="300" class="ma-3 mb-0">
      <v-img :src="recipe.image" max-height="150" @click.stop="dialog = true">
        <!-- Affichage hover -->
        <v-fade-transition fluid width="100">
          <v-overlay
            v-if="hover"
            absolute
            color="secondary"
            style="cursor: pointer"
            opacity="0.6"
          >
            <h3 class="text-center">Voir le detail</h3>
          </v-overlay>
        </v-fade-transition>

        <!-- Informations sur la recette  -->
        <v-dialog v-model="dialog" max-width="700">
          <v-card>
            <v-card-title class="headline">
              {{ recipe.label }}
            </v-card-title>

            <v-card-text>
                <v-container>
                  <v-chip v-for="(healthLabel,id) in recipe.healthLabels" :key="'healthLabel-'+id" class="mr-2 mb-3">{{ healthLabel }}</v-chip>
                </v-container>
                <v-container>
                    <h3>Ingredients</h3>
                    <ul>
                        <li
                        v-for="(ingredient,id) in recipe.ingredientLines"
                        :key="'ingredient-'+id"
                        >
                        {{ ingredient }}
                        </li>
                    </ul>
                </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="red darken-1" text @click="dialog = false">
                Fermer
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-img>
      <v-card-text class="title font-weight-bold secondary--text">
          {{ recipe.label }}
        </v-card-text>
    </v-card>
  </v-hover>
</template>

<script>
export default {
  props: {
    recipe: Object,
  },
  data() {
    return {
      dialog: false,
    };
  },
};
</script>

<style>
</style>