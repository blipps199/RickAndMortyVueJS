<template>
  <div class="container">
    <div class="row">
      <h1>Characters</h1>
      <div class="container mb-3">
        <div class="row g-3">
          <div class="input-group input-group-sm">
            <span class="input-group-text" id="inputGroup-sizing-sm">Search for character names(i.e. "Rick")</span>
            <input v-model="searchTerm" type="text" class="form-control" aria-label="Sizing example input" aria-describedby="inputGroup-sizing-sm">
          </div>
        </div>
      </div>
      <div class="character col-md-3 mb-4" v-for="character in characters" v-bind:key="character.id" v-on:click="fetchCharacter(character.id)" data-bs-toggle="modal" data-bs-target="#characterModal">
        <div class="border p-3 h-100">
          <template v-if="character.image"><img class="w-100 mb-2" :src="character.image"></template>
          <template v-if="character.name"><h5>{{ character.name }}</h5></template>
          <template v-if="character.species"><h6 class="mb-0">{{ character.species }}</h6></template>
        </div>
      </div>
    </div>
  </div>
  <div class="container mb-5">
    <div class="row">

      <div class="col-6">
        <button class="btn btn-primary w-100" @click="prevPage" v-if="pageInfo.prev">Prev</button>
      </div>

      <div class="col-6">
        <button class="btn btn-primary w-100" @click="nextPage" v-if="pageInfo.next">Next</button>
      </div>

    </div>
  </div>

  <!-- Modal -->
  <div class="modal fade" id="characterModal" tabindex="-1" aria-labelledby="characterModalLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="characterModalLabel"><h3 class="mb-0">{{character.name}}</h3></h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
        <template v-if="character.name">
          <template v-if="character.image"><img class="w-100 mb-2" :src="character.image"></template>
          <h6>Status: 
            <span v-bind:class="{ 
              'text-danger': character.status === 'Dead', 
              'text-success': character.status === 'Alive', 
              'text-warning': character.status === 'unknown' 
              }">{{character.status}}
            </span>
          </h6>
          <h6>Species: {{character.species}}</h6>
          <h6>Type: {{character.type}}</h6>
          <h6>Gender: {{character.gender}}</h6>
          <h6>Location: {{character.location.name}}</h6>
          <h6>Episodes:</h6>
          <ul class="ps-4">
            <li  data-bs-dismiss="modal" aria-label="Close" v-for="episode in character.episode">{{episode.id}} - {{episode.name}} | {{episode.air_date}}</li>
          </ul>
        </template>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      characters: [],
      character: [],
      pageInfo: {},
      page: 1,
      searchTerm: ''
    };
  },
  methods: {
    // This method gets all charcters on the specific page
    fetchCharacters() {
      const query = `
        query($page: Int, $search: String) {
          characters(page: $page, filter: { name: $search }) {
            results {
              id
              name
              species
              image
            }
            info {
              prev
              next
            }
          }
        }
      `;

      axios.post('https://rickandmortyapi.com/graphql', { query, variables: { page: this.page, search: this.searchTerm } })
        .then(response => {
          this.characters = response.data.data.characters.results;
          this.pageInfo = response.data.data.characters.info;
        })
        .catch(error => {
          console.error(error);
        });

      // Scroll to top
      window.scrollTo(0, 0);
    },

    // This method gets the data of once character
    fetchCharacter(id) {
      const query = `
        query($id: ID!) {
          character(id: $id) {
            id
            name
            status
            species
            type
            gender
            image
            episode {
              id
              name
              air_date
            }
            location {
              name
            }
          }
        }
      `;

    axios.post('https://rickandmortyapi.com/graphql', { query, variables: { id: id } })
      .then(response => {
        this.character = response.data.data.character;
        console.log(this.character);
      })
      .catch(error => {
        console.error(error);
      });

    },

    // Paginate to the previous page
    prevPage() {
      if (this.pageInfo.prev) {
        this.page--;
        this.fetchCharacters();
      }
    },

    // Paginate to the next page
    nextPage() {
      if (this.pageInfo.next) {
        this.page++;
        this.fetchCharacters();
      }
    }
  },
  watch: {
    searchTerm: {
      handler() {
        clearTimeout(this.searchTimer);
        this.searchTimer = setTimeout(() => {
          this.fetchCharacters();
        }, 250);
      },
      deep: true
    }
  },
  created() {
    this.fetchCharacters();
  }
};
</script>