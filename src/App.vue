<template>
  <div id="app">
    <div class="hero is-white is-gradient is-bold">
      <div class="hero-body">
        <h1 class="title">
          <span class="has-text-success">THE RICK AND MORTY API</span>&nbsp;
          <span class="subtitle">https://rickandmortyapi.com/</span>
        </h1>

        <div class="field has-addons is-pulled-right">
          <div class="control">
            <input
              v-model="search"
              type="text"
              class="input is-rounded"
              v-on:keyup.enter="searchData"
            >
          </div>
          <div class="control">
            <button class="button is-success is-rounded" v-on:click="searchData">Buscar</button>
          </div>
        </div>
      </div>
    </div>
    <div class="container">
      <div class="columns is-desktop is-mobile is-tablet is-multiline is-centered">
        <character
          @showModal="showModal"
          v-for="character of characters"
          v-bind:key="character.id"
          v-bind:character="character"
        />
      </div>

      <nav class="pagination is-centered" role="navigation" aria-label="pagination">
        <ul class="pagination-list">
          <li>
            <a class="pagination-previous" v-on:click="changePage(page-1);">Anterior</a>
          </li>
          <li>
            <a class="pagination-link is-current">{{page}}</a>
          </li>
          <li>
            <a class="pagination-next" v-on:click="changePage(page+1);">Siguiente</a>
          </li>
        </ul>
      </nav>
    </div>

    <div class="modal" :class="{'is-active': modal}" v-if="modal">
      <div class="modal-background" @click="modal = false"></div>
      <div class="modal-card">
        <header class="modal-card-head">
          <p class="modal-card-title">Acerca de: {{currentCharacter.name}}</p>
        </header>
        <div class="modal-card-body">
          <p>Genero:</p>
          <strong>{{currentCharacter.gender}}</strong>
          <p>Estado:</p>
          <strong>{{currentCharacter.status}}</strong>
          <p>Raza:</p>
          <strong>{{currentCharacter.species}}</strong>
          <p>Tipo:</p>
          <strong>{{currentCharacter.type}}</strong>
        </div>
        <footer class="modal-card-foot">
          <buttton class="button" @click="modal = false">Cerrar</buttton>
        </footer>
      </div>
    </div>
  </div>
</template>

<script>
// Librerias
import axios from "axios";
// Componentes
import character from "./components/Character";

export default {
  name: "app",
  components: {
    character
  },
  data: function() {
    return {
      characters: [],
      page: 1,
      pages: 1,
      search: "",
      modal: false,
      currentCharacter: {}
    };
  },
  created() {
    this.fetch();
  },
  methods: {
    fetch() {
      const params = {
        page: this.page,
        name: this.search
      };
      let result = axios
        // https://rickandmortyapi.com/api/character/?page=
        .get("https://rickandmortyapi.com/api/character", { params })
        .then(res => {
          this.characters = res.data.results;
          this.pages = res.data.info.pages;
          console.log(res.data);
        })
        .catch(err => {
          console.log(err);
        });
    },
    changePage(page) {
      this.page = page <= 0 || page > this.pages ? this.page : page;
      this.fetch();
    },
    searchData() {
      this.page = 1;
      this.fetch();
    },
    showModal(id) {
      this.fetchOne(id);
    },
    async fetchOne(id) {
      let result = await axios.get(
        `https://rickandmortyapi.com/api/character/${id}/`
      );

      this.currentCharacter = result.data;
      this.modal = true;
    }
  }
};
</script>

