<template>
  <div>
    <Transition name="game-appear">
      <h1 v-if="!startGame">{{ timer }}</h1>
      <div class="selected-word" v-else>
        <div class="game-screen" v-if="!gameFinished">
          <div class="game-info">
            <h3>{{ roundIndex }} / 10</h3>
            <h3 :class="getProcentageColor">
              {{ stringFamiliarProcentage }} %
            </h3>
          </div>
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
    </Transition>
  </div>
</template>

<script setup>
import { ref, watch, onMounted, computed } from "vue";
import * as stringSimilarity from "string-similarity";
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
  {
    word: "cool",
    antonym: ["hot", "warm"],
  },
  {
    word: "ignorance",
    antonym: [
      "talent",
      "wisdom",
      "literacy",
      "experience",
      "cognizance",
      "undearstanding",
    ],
  },
  {
    word: "talent",
    antonym: ["stupidity", "weakness", "incapacity", "ignorance", "inability"],
  },
  {
    word: "want",
    antonym: ["dislike", "hate", "plenty", "distaste"],
  },
  {
    word: "plenty",
    antonym: ["lack", "need", "few", "little", "scarcity"],
  },
  {
    word: "fat",
    antonym: ["skinny", "slight", "thin", "slim", "lean"],
  },
  {
    word: "elite",
    antonym: ["poor", "common", "lower", "inferior", "bad", "second-rate"],
  },
  {
    word: "worst",
    antonym: ["best"],
  },
  {
    word: "reduce",
    antonym: ["extend", "expand", "enlarge", "grow"],
  },
  {
    word: "innocent",
    antonym: ["guilty", "immoral", "sinful"],
  },
  {
    word: "evil",
    antonym: ["good", "lovely", "aiding", "attractive", "moral", "right"],
  },
  {
    word: "aiding",
    antonym: ["harmful", "hurting"],
  },
  {
    word: "strong",
    antonym: ["weak", "fragile", "sluggish"],
  },
  {
    word: "cheap",
    antonym: ["expensive", "costly"],
  },
  {
    word: "rude",
    antonym: ["polite", "gentle", "nice"],
  },
  {
    word: "bold",
    antonym: ["afraid", "coward", "cowardly", "fearful"],
  },
  {
    word: "busy",
    antonym: ["idle", "inactive", "unbusy"],
  },
  {
    word: "alive",
    antonym: ["dead"],
  },
  {
    word: "fresh",
    antonym: ["old", "stale", "used", "worn"],
  },
  {
    word: "quick",
    antonym: ["slow", "clumsy", "sluggish"],
  },
  {
    word: "diligent",
    antonym: ["lazy"],
  },
  {
    word: "dangerous",
    antonym: ["good", "healthy", "safe", "secure", "ok", "great"],
  },
  {
    word: "risky",
    antonym: ["certain", "healthy", "harmless"],
  },
  {
    word: "ugly",
    antonym: ["attractive", "beatiful", "pretty", "handsome"],
  },
  {
    word: "heavy",
    antonym: ["easy", "light"],
  },
  {
    word: "complex",
    antonym: ["simple"],
  },
  {
    word: "infinite",
    antonym: ["finite"],
  },
  {
    word: "pure",
    antonym: [
      "dark",
      "fake",
      "dishonest",
      "counterfeit",
      "unclear",
      "abnormal",
      "cloudy",
    ],
  },
  {
    word: "predictable",
    antonym: ["unpredictable"],
  },
  {
    word: "night",
    antonym: ["day"],
  },
  {
    word: "near",
    antonym: ["far", "distant", "away"],
  },
  {
    word: "past",
    antonym: ["after", "future", "current", "present"],
  },
  {
    word: "old",
    antonym: ["young"],
  },
  {
    word: "modern",
    antonym: ["old", "old-fashioned", "ancient", "outdated"],
  },
  {
    word: "sharp",
    antonym: ["blunt", "dull"],
  },
  {
    word: "smart",
    antonym: ["stupid", "dull", "foolish"],
  },
  {
    word: "pain",
    antonym: ["aid", "pleasure", "relief", "joy"],
  },
  {
    word: "begin",
    antonym: ["end", "stop", "halt", "crease"],
  },
  {
    word: "rise",
    antonym: ["descend", "drop"],
  },
];
const scoreTimerMiliSeconds = ref(0);
const scoreTimerSeconds = ref(0);
const selectedWord = ref(generateWord());
const typedWord = ref("");
const stringFamiliarProcentage = ref(0.0);

let interval;
const displaySeconds = computed(() => {
  if (scoreTimerSeconds.value < 10) return "0:" + scoreTimerSeconds.value;
  else return scoreTimerSeconds;
});

watch(typedWord, (newValue) => {
  const randomWord = listOfWords.findIndex((w, i) => {
    if (w.word === selectedWord.value) return i;
  });
  listOfWords[randomWord].antonym.forEach((word) => {
    if (typedWord.value.toLowerCase() === word) {
      guessedWords.value++;
      nextWord();
    }
  });

  const bestPercentage = stringSimilarity.findBestMatch(
    typedWord.value.toLowerCase(),
    listOfWords[randomWord].antonym
  );

  stringFamiliarProcentage.value = (
    bestPercentage.bestMatch.rating * 100
  ).toFixed(2);
});
watch(startGame, (newValue) => {
  if (newValue === true) {
    interval = setInterval(() => {
      if (scoreTimerMiliSeconds.value >= 60) {
        scoreTimerMiliSeconds.value = 0;
        scoreTimerSeconds.value++;
      }
      scoreTimerMiliSeconds.value++;
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

const getProcentageColor = computed(() => {
  return {
    red: stringFamiliarProcentage.value <= 25,
    yellow: stringFamiliarProcentage.value >= 60,
    green: stringFamiliarProcentage.value > 61,
  };
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
  const word = listOfWords[randomWords].word;
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
  console.log("selected " + selectedWord.value);
  console.log("typed " + selectedWord.value);
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
.dog {
  max-width: 70rem;
}
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

.game-screen {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.game-info {
  display: flex;
  justify-content: center;
  gap: 2rem;
  font-size: 1.6rem;
}
.red {
  color: red;
}
.yellow {
  color: yellow;
}
.green {
  color: green;
}

.game-appear-enter-active {
  animation: test 0.3s linear;
}
.game-appear-leave-active {
  animation: test 0.3s linear reverse;
}

@keyframes test {
  0% {
    transform: scaleX(0.3);
  }
  50% {
    transform: scaleX(1);
  }
  100% {
    transform: scale(1);
  }
}
</style>
