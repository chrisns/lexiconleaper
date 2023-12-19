// eslint-disable-next-line vue/multi-word-component-names
<script setup lang="ts">
import { ref, computed } from 'vue'

import games from '../assets/games.json'

const randomGame = ref(false)
const randomLetter = ref(false)
var game = ref(Object.keys(games)[0])
var letterIndex = ref(0)
var letters = Array.from({ length: 26 }, (_, i) => String.fromCharCode(i + 97))

const playLetter = computed(() => letters[letterIndex.value])
// @ts-ignore: Unreachable code error
const hints = computed(() => games[game.value].filter((item) => item.charAt(0).toLowerCase() == playLetter.value))
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

  const viableLetters =
    // @ts-ignore: 7053
    games[game.value].length > 0
      ? // @ts-ignore: 7053
        Array.from(new Set(games[game.value].map((item) => item.charAt(0).toLowerCase())))
      : letters

  if (randomLetter.value == true)
    letterIndex.value = Math.floor(Math.random() * viableLetters.length)
  else if (letterIndex.value >= viableLetters.length) letterIndex.value = 0
  else letterIndex.value++

  countdown.value = timeAllowance
  showHints.value = false
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
    <button @click="showHints = !showHints" :class="{ on: showHints }">Hints</button>
  </div>
  <ul id="hints" v-if="showHints">
    <li v-for="hint in hints" :key="hint">
      {{ hint }}
    </li>
  </ul>
</template>

<style scoped>
button.on {
  background-color: green;
}
button {
  transition: background-color 0.5s ease;
  background-color: rgb(175, 175, 175);
  font-size: 2em;
  margin: 0.2em;
  padding: 0.2em;
}
#category {
  font-size: 4rem;
}
#letter {
  font-size: 20rem;
  text-align: center;
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
</style>
