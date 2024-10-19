<template>
  <div class="container-fluid bg-dark min-vh-100">
    <!-- <div class="container bg-dark min-vh-100"> -->
    <div class="row justify-content-center bg-gradient">
      <div class="col-6 text-center card-img">
        <img src="/images/Pokemon-logo.png" alt="Pokemon-logo" />
      </div>
    </div>
    <div class="row justify-content-center">
      <div class="col-6 mt-5">
        <input
          type="text"
          placeholder="search pokemon"
          class="form-control"
          v-model="searchQuery"
          @input="searchPokemon"
        />
      </div>
    </div>
    <div class="content m-5 mb-0">
      <div class="view-all text-white" v-if="!viewDetail">
        <div class="row g-4">
          <div
            class="col-12 col-lg-2 col-md-4"
            v-for="pokemon in pokemonList"
            @click="viewDetailPokemon(pokemon.name)"
          >
            <div
              class="card text-bg-dark bg-gradient text-center fs-4 border-2 border-dark-subtle"
            >
              <img :src="pokemon.sprites.front_default" alt="" />
              <label for="" class="mb-4">{{ pokemon.name }}</label>
            </div>
          </div>
        </div>
      </div>
      <div class="view-detail text-white" v-else>
        <div>
          <button @click="viewDetail = false" class="rounded-3 px-4">
            back
          </button>
        </div>
        <div class="row g-3 pt-3">
          <div class="col-12 col-md-6">
            <div
              class="card text-bg-dark bg-gradient text-center border-2 border-dark-subtle"
            >
              <img
                :src="
                  selectedPokemon.sprites
                    ? selectedPokemon.sprites.front_default
                    : ''
                "
                alt=""
              />
            </div>
          </div>
          <div class="col-12 col-md-6">
            <div class="text-bg-dark text-center fs-5">
              <h1>
                {{ selectedPokemon.name }}
              </h1>
              <span>
                Type:
                <!-- <Text v-for="detail in selectedPokemon.types">
                  {{ detail.type.name }}
                </Text> -->
                <Text>
                  {{
                    selectedPokemon.types
                      .map((detail) => detail.type.name)
                      .join(', ')
                  }}
                </Text> </span
              ><br />
              <span>
                Skills:
                <Text>
                  {{
                    selectedPokemon.moves
                      .map((detail) => detail.move.name)
                      .join(', ')
                  }}
                </Text>
              </span>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- </div> -->
  </div>
</template>

<script>
import { Text } from 'vue';

export default {
  data() {
    return {
      searchQuery: '',
      pokemonList: [],
      viewDetail: false,
      selectedPokemon: '',
    };
  },
  methods: {
    async getListPokemon() {
      try {
        const response = await fetch(
          'https://pokeapi.co/api/v2/pokemon?limit=30'
        );
        const data = await response.json();
        const results = data.results;

        results.forEach(this.getEachPokemon);
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    },
    async getEachPokemon(pokemon) {
      const response = await fetch(
        'https://pokeapi.co/api/v2/pokemon/' + pokemon.name
      );
      const data = await response.json();

      this.pokemonList.push(data);
      this.pokemonList.sort((a, b) => a.id - b.id);
    },
    searchPokemon() {
      console.log(`Searching for: ${this.searchQuery}`);
    },
    async viewDetailPokemon(pokemonName) {
      this.viewDetail = true;
      const response = await fetch(
        'https://pokeapi.co/api/v2/pokemon/' + pokemonName
      );
      const data = await response.json();
      console.log(data);
      this.selectedPokemon = data;
    },
  },
  mounted() {
    this.getListPokemon();
  },
};
</script>

<style scoped>
.card {
  cursor: pointer;
}
</style>
