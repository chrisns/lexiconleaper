// eslint-disable-next-line vue/multi-word-component-names
<script setup lang="ts">
import { ref, computed } from 'vue'

import games from '../assets/games.json'

const randomGame = ref(false)
const randomLetter = ref(false)
const showInstructions = ref(false)
var game = ref(Object.keys(games)[0])
var letterIndex = ref(0)
var letters = Array.from({ length: 26 }, (_, i) => String.fromCharCode(i + 97))

const playLetter = computed(() => letters[letterIndex.value])
const hints = computed(() =>
  // @ts-ignore: Unreachable code error
  games[game.value].filter((item) => item.charAt(0).toLowerCase() == playLetter.value)
)
const showHints = ref(false)
const timeAllowance = 10
const countdown = ref(timeAllowance)

setInterval(() => {
  if (countdown.value > 0) {
    countdown.value -= 1
  }
}, 1000)

function randomizeGame() {
  game.value = Object.keys(games)[Math.floor(Math.random() * Object.keys(games).length)]
}

function play() {
  if (randomGame.value == true) randomizeGame()

  showHints.value = false

  const viableLetters =
    // @ts-ignore: 7053
    games[game.value].length > 0
      ? // @ts-ignore: 7053
        Array.from(new Set(games[game.value].map((item) => item.charAt(0).toLowerCase())))
      : letters

  if (randomLetter.value == true)
    letterIndex.value = Math.floor(Math.random() * viableLetters.length)
  else if (letterIndex.value == viableLetters.length - 1) letterIndex.value = 0
  else letterIndex.value++

  countdown.value = timeAllowance
  // @ts-expect-error
  gtag('event', 'play', {
    game: game.value,
    letter: playLetter.value,
    randomGame: randomGame.value,
    randomLetter: randomLetter.value
  })
}
function reload() {
  location.reload()
}

randomizeGame()
</script>

<template>
  <div id="category">{{ game }}</div>
  <div id="letter" @click="play">{{ playLetter.toLocaleUpperCase() }}</div>
  <div id="countdown">{{ countdown }}</div>

  <div id="buttons">
    <button @click="play">Next</button><br />
    <button @click="randomGame = !randomGame" :class="{ on: randomGame }">Random Category</button>
    <button @click="randomLetter = !randomLetter" :class="{ on: randomLetter }">
      Random Letter
    </button>
    <button @click="reload">Restart</button>
    <button @click="showHints = !showHints" :class="{ on: showHints }">Hints</button>
    <button @click="showInstructions = !showInstructions" :class="{ on: showInstructions }">
      How to play
    </button>
  </div>
  <ul id="hints" v-if="showHints">
    <li v-for="hint in hints" :key="hint">
      {{ hint }}
    </li>
  </ul>
  <div id="instructions" v-if="showInstructions">
    <h2>How to play</h2>
    <p>You will be presented with a category and a letter.</p>
    <p>
      You or someone in your group must name something that matches the category that begins with
      the letter before the time runs out
    </p>
    <p>You will be presented a random category when the game starts.</p>
    <p>You will not get a letter if there isn't a valid answer</p>
    <p>
      If you want to mix things up you can select to have a
      <a @click="randomGame = !randomGame">random category</a> and/or
      <a @click="randomLetter = !randomLetter">random letter</a> on each play
    </p>
    <p>If you run out of ideas, you can <a @click="showHints = !showHints">show the hints</a></p>
  </div>
</template>

<style scoped>
button.on {
  background-color: green;
  color: white;
}
button {
  transition:
    background-color 0.5s ease,
    color 0.5s ease;
  background-color: rgb(175, 175, 175);
  font-size: 2em;
  margin: 0.2em;
  padding: 0.2em;
}
#category {
  font-size: 4rem;
}
#instructions {
  text-align: center;
  max-width: 500px;
  margin: 0 auto;
  color: white;
}
#instructions a {
  text-decoration: underline;
}
#letter {
  font-size: 20rem;
  text-align: center;
  -webkit-user-select: none; /* Safari */
  -ms-user-select: none; /* IE 10 and IE 11 */
  user-select: none; /* Standard syntax */
}
#buttons {
  display: block;
  text-align: center;
}
#countdown {
  font-size: 4rem;
  position: absolute;
  top: 5px;
  right: 5px;
}
#hints li {
  list-style: none;
  display: inline-block;
  background: rgb(188, 187, 187);
  margin: 0.2rem;
  padding: 0.2rem;
}
#letter:hover,
button:hover {
  cursor: pointer;
}
</style>
