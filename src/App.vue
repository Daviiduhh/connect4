<script setup lang="ts">
import { ref } from "vue";

const turn = ref('red'),
  tiles = ref(new Array(49))

const setTile = (position: number) => {
  for (let index = 6; index >= 0; index--) {
    if (tiles.value[(index * 7) + position - 1] === undefined) {
      tiles.value[(index * 7) + position - 1] = turn.value
      changeTurn()
      break
    }
  }
}

const changeTurn = () => {
  if (turn.value == "red")
    turn.value = "blue"
  else if (turn.value == "blue")
    turn.value = "red"
}
</script>

<template>
  <h2>{{ turn }}</h2>
  <main class="selector">
    <span v-for="n in 7" @click="setTile(n)" :key="n" class="casilla" :class="turn">
      <svg xmlns="http://www.w3.org/2000/svg" height="14" width="8.5" viewBox="0 0 320 512">
        <path fill="#fdf0d5"
          d="M2 334.5c-3.8 8.8-2 19 4.6 26l136 144c4.5 4.8 10.8 7.5 17.4 7.5s12.9-2.7 17.4-7.5l136-144c6.6-7 8.4-17.2 4.6-26s-12.5-14.5-22-14.5l-72 0 0-288c0-17.7-14.3-32-32-32L128 0C110.3 0 96 14.3 96 32l0 288-72 0c-9.6 0-18.2 5.7-22 14.5z" />
      </svg>
    </span>
  </main>
  <main class="tablero">
    <span v-for="tile in tiles" class="casilla" :class="tile"></span>
  </main>
</template>

<style scoped>
.selector {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 0.5rem;

  background-color: #fdf0d510;
  padding: 1rem;
  border-radius: 15px;
}

.tablero {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  grid-template-rows: repeat(7, 1fr);
  gap: 0.5rem;

  background-color: #fdf0d5;
  padding: 1rem;
  border-radius: 15px;
}

.casilla {
  width: 3em;
  aspect-ratio: 1;
  border-radius: 50%;
  background-color: #242424;
  display: flex;
  align-items: center;
  justify-content: center;
}

.selector .casilla {
  cursor: pointer;
}

.selector .casilla.blue:hover {
  background-color: #669bbc;
}

.selector .casilla.red:hover {
  background-color: #780000;
}

.tablero .casilla.blue {
  background-color: #003049;
}

.tablero .casilla.red {
  background-color: #c1121f;
}
</style>
