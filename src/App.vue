<template>
  <div class="container-fluid bg-dark">
    <div class="container bg-dark bg-gradient min-vh-100">
      <div class="row justify-content-center">
        <div class="col-6 text-center">
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
      <div class="content mt-5">
        <div class="view-all text-white">
          <div class="row g-4">
            <div class="col-12 col-md-4 col-lg-3">
              <div class="card">a</div>
            </div>
            <div class="col-12 col-md-4 col-lg-3">
              <div class="card">a</div>
            </div>
            <div class="col-12 col-md-4 col-lg-3">
              <div class="card">a</div>
            </div>
            <div class="col-12 col-md-4 col-lg-3">
              <div class="card">a</div>
            </div>
            <div class="col-12 col-md-4 col-lg-3">
              <div class="card">a</div>
            </div>
          </div>
        </div>
        <div class="view-detail">detail</div>
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
    },
    searchPokemon() {
      console.log(`Searching for: ${this.searchQuery}`);
    },
  },
  mounted() {
    this.getListPokemon();
  },
};
</script>

<style scoped>
/* Tambahkan CSS jika diperlukan */
</style>
