<template>
  <container>
    <nav>
      <div class="img-wrapper">
        <img src="images/synonym.png" alt="game logo" />
      </div>
      <ul>
        <li><router-link to="/">Main</router-link></li>
        <li><router-link to="/source">Source Code</router-link></li>
      </ul>
    </nav>
  </container>
</template>

<script setup>
import Container from "./UI/Container.vue";
</script>

<style lang="scss" scoped>
@import "../assets/variables.scss";
a {
  color: white;
}

li:first-child {
  // background-color: red;
}

li {
}
nav {
  display: flex;
  align-items: center;
}

img {
  width: 100%;
}

a {
  transition: 0.5s all linear;
}

a:hover {
  color: $cadet-gray;
}

.img-wrapper {
  width: clamp(20rem, 22.5%, 50rem);

  display: flex;
  justify-content: center;
  align-items: center;
}

ul {
  margin-left: auto;
  // justify-self: flex-end;
  display: flex;
  align-items: center;
  gap: 5rem;
}
</style>
