<script>
export default {
  data() {
    return {
      pokemons: [],
      selectedPokemon: null,
    };
  },
  created() {
    this.fetchPokemons();
  },
  methods: {
    async fetchPokemons() {
      try {
        const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=1015');
        const data = await response.json();
        this.pokemons = await Promise.all(data.results.map(async (pokemon) => {
          const details = await fetch(pokemon.url);
          return await details.json();
        }));
      } catch (error) {
        console.error('Error fetching Pokémon:', error);
      }
    },
    showDetails(pokemon) {
      this.selectedPokemon = pokemon;
    },
    closeDetails() {
      this.selectedPokemon = null;
    },
  },
};
</script>

<template>
  <div id="app" class="container mt-5">
    <div class="row">
      <div class="col-md-4" v-for="pokemon in pokemons" :key="pokemon.id">
        <div class="card mb-4">
          <img :src="pokemon.sprites.front_default" class="card-img-top" alt="Pokemon Image" />
          <div class="card-body">
            <h5 class="card-title">{{ pokemon.name }}</h5>
            <p class="card-text">Tipo: {{ pokemon.types.map(type => type.type.name).join(', ') }}</p>
            <button @click="showDetails(pokemon)" class="btn btn-primary">Detalhes</button>
          </div>
        </div>
      </div>
    </div>

    <div v-if="selectedPokemon" class="modal fade show" style="display: block;" tabindex="-1" role="dialog">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">{{ selectedPokemon.name }}</h5>
            <button type="button" class="close" @click="closeDetails" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <img :src="selectedPokemon.sprites.front_default" class="img-fluid" />
            <p><strong>Tipos:</strong> {{ selectedPokemon.types.map(type => type.type.name).join(', ') }}</p>
            <p><strong>Altura:</strong> {{ selectedPokemon.height }}</p>
            <p><strong>Peso:</strong> {{ selectedPokemon.weight }}</p>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" @click="closeDetails">Fechar</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.text-decoration-line-through {
  text-decoration: line-through;
}
</style>