<template>
  <div>
    <h1 v-if="!startGame">{{ timer }}</h1>
    <div class="selected-word" v-else>
      <div class="game-screen" v-if="!gameFinished">
        <h3>{{ roundIndex }} / 10</h3>
        <div class="timer">
          <h3>{{ displaySeconds }}</h3>

          :

          <h3>{{ scoreTimerMiliSeconds }} ms</h3>
        </div>
        <h1>{{ selectedWord }}</h1>
        <h2 class="typed-word">{{ typedWord }}</h2>
      </div>
      <div class="game-screen-done" v-else>
        <h1>Done!</h1>
        <h1>Your score: {{ calculateScore() }}</h1>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, onMounted, computed } from "vue";
const timer = ref(3);
const startGame = ref(false);
const gameFinished = ref(false);
const roundIndex = ref(1);
const guessedWords = ref(0);
const listOfWords = [
  {
    word: "infamous",
    antonym: [
      "friendly",
      "gentle",
      "good",
      "kind",
      "nice",
      "pleasant",
      "wonderful",
    ],
  },
  {
    word: "big",
    antonym: ["tiny", "little", "small"],
  },
  {
    word: "child",
    antonym: ["adult", "parent"],
  },
  {
    word: "bad",
    antonym: ["ok", "good", "great"],
  },
  {
    word: "honest",
    antonym: ["corrupt", "deceitful", "deceptive", "devious", "dishonest"],
  },
  {
    word: "cool",
    antonym: ["hot", "warm"],
  },
];
const scoreTimerMiliSeconds = ref(0);
const scoreTimerSeconds = ref(0);
const selectedWord = ref(generateWord());
const typedWord = ref("");
let interval;
const displaySeconds = computed(() => {
  if (scoreTimerSeconds.value < 10) return "0:" + scoreTimerSeconds.value;
  else return scoreTimerSeconds;
});

watch(typedWord, (newValue) => {
  const randomWord = listOfWords.findIndex((w, i) => {
    if (w.word === selectedWord.value) return i;
  });
  console.log(randomWord);
  listOfWords[randomWord].antonym.forEach((word) => {
    if (typedWord.value.toLowerCase() === word) {
      guessedWords.value++;
      nextWord();
    }
  });
});
watch(startGame, (newValue) => {
  if (newValue === true) {
    interval = setInterval(() => {
      if (scoreTimerMiliSeconds.value >= 60) {
        scoreTimerMiliSeconds.value = 0;
        scoreTimerSeconds.value++;
      }
      scoreTimerMiliSeconds.value++;
      console.log("veikiu");
    }, 10);
  } else {
    clearInterval(interval);
  }
});
watch(roundIndex, (newValue) => {
  if (newValue === 11) {
    clearInterval(interval);
    gameFinished.value = true;
  }
});

const calculateScore = () => {
  const scoreForGuessedWords = guessedWords.value * 100;
  const timePenalty =
    scoreTimerSeconds.value + scoreTimerMiliSeconds.value * 0.1;
  const finalScore = Math.ceil(scoreForGuessedWords / timePenalty);
  return finalScore;
};

function nextWord() {
  typedWord.value = "";
  selectedWord.value = generateWord();
  roundIndex.value++;
}

function generateWord() {
  const randomWords = Math.ceil(Math.random() * (listOfWords.length - 1) + 0);
  console.log("random index" + randomWords);
  const word = listOfWords[randomWords].word;
  console.log(word);
  return word;
}

onMounted(() => {
  if (startGame) window.addEventListener("keydown", typeWord);
});

const typeWord = (e) => {
  let key = String.fromCharCode(e.keyCode).toUpperCase();
  if (e.keyCode === 8 && typedWord.value === "") return;
  if (e.keyCode === 8 && typedWord.value.length > 0) {
    const sliced = typedWord.value.slice(0, -1);
    typedWord.value = sliced.trim();
  } else {
    typedWord.value += key;
  }
};

const timerUntilGameBeggins = setInterval(() => {
  timer.value--;
  if (timer.value === 0) {
    setTimeout(() => {
      clearInterval(timerUntilGameBeggins);
      startGame.value = true;
    }, 500);
  }
}, 1000);
</script>

<style lang="scss" scoped>
@import "./../../assets/variables.scss";
.timer {
  position: absolute;
  right: 10%;
  top: 5%;
  font-size: 1.6rem;
  display: flex;
  gap: 0.5rem;
  justify-content: center;
  // width: 100%;
}

.selected-word {
  position: relative;
  min-height: 30rem;
  background-color: $english-violet;
  min-width: 30vw;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  padding: $default-padding;
  border-radius: 15px;
}

.typed-word {
  position: absolute;
  bottom: 25%;
  left: 50%;
  transform: translateX(-50%);
  font-size: 3rem;
}
</style>
