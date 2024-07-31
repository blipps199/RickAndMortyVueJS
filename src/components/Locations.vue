<template>
  <div class="container p-2">
    <div class="row g-2">
      <h1>Locations</h1>
      <div class="list-group">
        <div v-for="location in locations" href="#" class="list-group-item list-group-item-action" aria-current="true">
          <div class="d-flex w-100 justify-content-between">
            <h5 class="mb-1">{{location.name}}</h5>
            <small>{{location.dimension}}</small>
          </div>
          <p class="mb-1">{{location.type}}</p>
          <template v-if="location.residents.length != 0">
            <small>
              Residents: <span v-for="(resident, index) in location.residents" :key="resident.id">{{resident.name}}<template v-if="index !== location.residents.length - 1">, </template></span>
            </small>
          </template>
          <template v-else>
            <small>Residents: None</small>
          </template>
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
      locations: [],
      pageInfo: {},
      page: 1
    };
  },
  methods: {
    // This method gets all charcters on the specific page
    fetchLocations() {
      const query = `
        query($page: Int) {
          locations(page: $page) {
            info {
              pages
              next
              prev
            }
            results {
              name
              type
              dimension
              residents {
                id
                name
              }
            }
          }
        }
      `;

      axios.post('https://rickandmortyapi.com/graphql', { query, variables: { page: this.page } })
        .then(response => {
          this.locations = response.data.data.locations.results;
          this.pageInfo = response.data.data.locations.info;
        })
        .catch(error => {
          console.error(error);
        });
        
      // Scroll to top
      window.scrollTo(0, 0);
    },

    // Paginate to the previous page
    prevPage() {
      if (this.pageInfo.prev) {
        this.page--;
        this.fetchLocations();
      }
    },

    // Paginate to the next page
    nextPage() {
      if (this.pageInfo.next) {
        this.page++;
        this.fetchLocations();
      }
    },
  },
  created() {
    this.fetchLocations();
  }
};
</script>