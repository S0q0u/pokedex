<template>
  <div class="container-fluid bg-dark min-vh-100">
    <div class="row justify-content-center bg-gradient">
      <div class="col-6 text-center card-img">
        <img src="/images/Pokemon-logo.png" alt="Pokemon-logo" />
      </div>
    </div>
    <div class="row justify-content-center">
      <div class="col-6 mt-5">
        <input
          type="text"
          id="search"
          placeholder="search pokemon"
          class="form-control"
          v-model="searchQuery"
          @input="searchPokemon"
        />
        <div class="text-white mt-3">
          suggestion:
          <span
            class="suggested-pokemon"
            @click="viewDetailPokemon(pokemon.name)"
            v-for="pokemon in suggestedPokemon"
          >
            {{ pokemon.name }} |
          </span>
        </div>
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
                {{
                  selectedPokemon.types
                    .map((detail) => detail.type.name)
                    .join(', ')
                }} </span
              ><br />
              <span>
                Skills:

                {{
                  selectedPokemon.moves
                    .map((detail) => detail.move.name)
                    .join(', ')
                }}
              </span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      searchQuery: '',
      pokemonList: [],
      viewDetail: false,
      selectedPokemon: '',
      pokemonGen1: [],
      suggestedPokemon: '',
    };
  },
  methods: {
    async getListPokemon() {
      try {
        const response = await fetch(
          'https://pokeapi.co/api/v2/pokemon?limit=30'
        );
        const data = await response.json();
        const result = data.results;

        result.forEach(this.getEachPokemon);
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
    async viewDetailPokemon(pokemonName) {
      this.viewDetail = true;
      const response = await fetch(
        'https://pokeapi.co/api/v2/pokemon/' + pokemonName
      );
      const data = await response.json();
      console.log(data);
      this.selectedPokemon = data;
    },
    async getPokemonGen1() {
      const response = await fetch(
        'https://pokeapi.co/api/v2/pokemon?limit=151'
      );
      const data = await response.json();
      const result = data.results;

      this.pokemonGen1 = result;
    },
  },
  watch: {
    searchQuery() {
      if (this.searchQuery === '') {
        this.suggestedPokemon = '';
      } else {
        const filteredPokemon = this.pokemonGen1.filter((pokemon) => {
          return pokemon.name.includes(this.searchQuery);
        });
        this.suggestedPokemon = filteredPokemon.slice(0, 3);
      }
    },
  },
  mounted() {
    this.getListPokemon();
    this.getPokemonGen1();
  },
};
</script>

<style scoped>
.card,
.suggested-pokemon {
  cursor: pointer;
}
</style>
