<template>
  <container>
    <nav>
      <ul>
        <li><router-link to="/">Home</router-link></li>
        <li><router-link to="/about">Home</router-link></li>
      </ul>
    </nav>
  </container>
</template>

<script setup>
import Container from "./UI/Container.vue";
</script>

<style lang="scss" scoped>
a {
  color: white;
}
</style>
