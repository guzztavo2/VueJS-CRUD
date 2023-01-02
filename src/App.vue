<script setup lang="ts">
import Header from './components/Header.vue';
import Render from './components/Render.vue';

import {Informacao} from './model/Model.Informacao.vue';

let info = new Informacao('aa');

</script>

<template>
  <div id="app">
    <Header></Header>
    <Render></Render>
  </div>
</template>

<style scoped>
header {
  line-height: 1.5;
  max-height: 100vh;
}


@media (min-width: 1024px) {
    header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  nav {
    text-align: left;
    margin-left: -1rem;
    font-size: 1rem;

    padding: 1rem 0;
    margin-top: 1rem;
  }
}
</style>
