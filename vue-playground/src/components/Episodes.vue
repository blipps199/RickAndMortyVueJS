<template>
  <div class="container p-2">
    <div class="row g-2">
      <h1>Episodes</h1>
      <div class="list-group">
        <div v-for="episode in episodes" href="#" class="list-group-item list-group-item-action" aria-current="true">
          <div class="d-flex w-100 justify-content-between">
            <h5 class="mb-1">{{episode.name}}</h5>
            <small>{{episode.air_date}}</small>
          </div>
          <p class="mb-1">{{episode.episode}}</p>
          <small>
            Characters: <span v-for="(character, index) in episode.characters" :key="character.id">{{character.name}}<template v-if="index !== episode.characters.length - 1">, </template></span>
          </small>
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
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      episodes: [],
      pageInfo: {},
      page: 1
    };
  },
  methods: {
    // This method gets all charcters on the specific page
    fetchEpisodes() {
      const query = `
        query($page: Int) {
          episodes(page: $page) {
            info {
              pages
              next
              prev
            }
            results {
              name
              episode
              air_date
              characters {
                id
                name
              }
            }
          }
        }
      `;

      axios.post('https://rickandmortyapi.com/graphql', { query, variables: { page: this.page } })
        .then(response => {
          this.episodes = response.data.data.episodes.results;
          this.pageInfo = response.data.data.episodes.info;
        })
        .catch(error => {
          console.error(error);
        });
        
      // Scroll to top
      window.scrollTo(0, 0);
    },

    //Paginate to the previous page
    prevPage() {
      if (this.pageInfo.prev) {
        this.page--;
        this.fetchEpisodes();
      }
    },

    // Paginate to the next page
    nextPage() {
      if (this.pageInfo.next) {
        this.page++;
        this.fetchEpisodes();
      }
    },
  },
  created() {
    this.fetchEpisodes();
  }
};
</script>