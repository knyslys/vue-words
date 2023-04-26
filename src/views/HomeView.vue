<template>
  <container class="game-menu">
    <div class="game-menu-selector" v-if="!gameStarted">
      <button @click="gameStarted = true">Play</button>
      <button>How to play?</button>
    </div>
    <main-game v-else></main-game>
  </container>
</template>

<script setup>
import Container from "@/components/UI/Container.vue";
import MainGame from "@/components/Game/MainGame.vue";
import { ref, onMounted, watch } from "vue";
const gameStart = ref(false);
const word = ref("");
const gameStarted = ref(false);
onMounted(() => {
  window.addEventListener("keydown", doTest);
});

const doTest = (e) => {
  let key = String.fromCharCode(e.keyCode).toUpperCase();
  if (e.keyCode === 8 && word.value === "") return;
  if (e.keyCode === 8 && word.value.length > 0) {
    const sliced = word.value.slice(0, -1);
    word.value = sliced.trim();
    console.log(word.value);
  } else {
    word.value += key;
    console.log(word.value);
  }
};
</script>

<style lang="scss">
@import "../assets/variables.scss";

.game-menu {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 50vh;
}

button {
  font-family: inherit;
  font-size: 3rem;
  padding: calc($default-padding - 2rem);
  border-radius: 10px;
  background-color: $rich-black;
  box-shadow: 0 5px 10px rgba(0, 0, 0, 0.2);
  font-weight: 600;
  cursor: pointer;
  border: none;
  color: #fff;
}

.game-menu-selector {
  background-color: $english-violet;
  display: flex;
  flex-direction: column;
  min-width: 50%;
  gap: 1rem;
  align-items: center;
  border-radius: 15px;
  padding: $default-padding;
  box-shadow: 0 10px 10px rgba(0, 0, 0, 0.5);
}
</style>
