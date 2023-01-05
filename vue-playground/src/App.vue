<template>
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark mb-5">
    <div class="container">
      <a class="navbar-brand" href="#">Rick and Morty - <small>Built with Vue.js</small></a>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav">
          <li class="nav-item" v-for="navItem in navItems">
            <a @click="currentComponent = navItem.component; setActiveNavItem(navItem);" class="nav-link" :class="{ active: navItem === activeNavItem }" aria-current="page" href="#">{{navItem.label}}</a>
          </li>
          <!-- <li class="nav-item">
            <a  class="nav-link" aria-current="page" href="#">{{currentComponent}}</a>
          </li> -->
        </ul>
      </div>
    </div>
  </nav>

  <component :is="currentComponent"></component>
</template>

<script>
import Characters from './components/Characters.vue';
import Episodes from './components/Episodes.vue';
import Locations from './components/Locations.vue';

export default {
  data() {
    return {
      navItems: [
        {label: 'Characters', component: 'characters'},
        {label: 'Episodes', component: 'episodes'},
        {label: 'Locations', component: 'locations'},
      ],
      currentComponent: 'characters'
    };
  },
  components: {
    'characters': Characters,
    'episodes': Episodes,
    'locations': Locations
  },
  methods: {
    setActiveNavItem(navItem) {
      this.activeNavItem = navItem;
      this.currentComponent = navItem.component;
    }
  }
};
</script>