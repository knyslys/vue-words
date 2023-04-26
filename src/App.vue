<script setup>
import { RouterLink, RouterView } from "vue-router";
import Container from "./components/UI/Container.vue";
import Navigation from "./components/Navigation.vue";
</script>

<template>
  <navigation></navigation>
  <main>
    <RouterView />
  </main>
</template>

<style lang="scss">
@import "./assets/variables.scss";

*,
*::after,
*::before {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

html {
  font-size: 62.5%;
}

body {
  min-height: 100vh;
  background-color: $rich-black;
  color: white;
  font-family: $font-family;
}
</style>
