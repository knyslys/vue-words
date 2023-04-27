<template>
  <container class="game-menu">
    <Transition name="game-appear" mode="out-in">
      <div class="game-menu-selector" v-if="!gameStarted">
        <button @click="gameStarted = true">Play</button>
        <button>How to play?</button>
      </div>
      <main-game v-else @go-back="backToMenu"></main-game>
    </Transition>
  </container>
</template>

<script setup>
import Container from "@/components/UI/Container.vue";
import MainGame from "@/components/Game/MainGame.vue";
import { ref, onMounted, watch } from "vue";
const gameStarted = ref(false);
const backToMenu = () => {
  gameStarted.value = false;
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
  padding: calc($default-padding - 1rem);
  border-radius: 10px;
  background-color: $imperial-red;
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
  gap: 3rem;
  align-items: center;
  border-radius: 15px;
  padding: $default-padding;
  box-shadow: 0 10px 10px rgba(0, 0, 0, 0.5);
}

.game-appear-leave-active {
  animation: test 0.3s linear 0.3s;
}
.game-appear-enter-active {
  animation: test 0.3s linear reverse;
}
.game-appear-leave-active button {
  animation: test2 0.3s forwards;
}

.game-appear-enter-from button {
  opacity: 0;
}

.game-appear-enter-active button {
  transition: 5s all linear;
}

.game-appear-enter-to button {
  opacity: 1;
}
@keyframes test {
  0% {
    transform: scaleX(1);
  }
  50% {
    transform: scaleX(0.3);
  }
  100% {
    transform: scale(0);
  }
}
@keyframes test2 {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}
</style>
