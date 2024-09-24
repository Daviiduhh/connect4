<script setup lang="ts">
import { ref } from "vue";

// Define the types for the game state
type Player = "red" | "blue" | undefined;

const turn = ref<Player>("red"), // Define 'turn' as a ref of type Player
  tiles = ref<Player[]>(new Array(49).fill(undefined)), // Tiles is an array of Player type
  winner = ref<Player>(undefined); // Winner can be "red", "blue", or undefined

// Set tile logic
const setTile = (position: number) => {
  if (winner.value) return; // Prevent further moves if there's a winner

  // Place the tile from bottom to top in the selected column
  for (let index = 6; index >= 0; index--) {
    const tileIndex = index * 7 + position - 1;
    if (tiles.value[tileIndex] === undefined) {
      tiles.value[tileIndex] = turn.value;
      // Check for a win after placing the tile
      if (checkWin(index, position - 1)) {
        winner.value = turn.value;
        break;
      }
      changeTurn();
      break;
    }
  }
};

// Switch turn between players
const changeTurn = () => {
  turn.value = turn.value === "red" ? "blue" : "red";
};

// Function to check for a win
const checkWin = (row: number, col: number): boolean => {
  const player = tiles.value[row * 7 + col];
  if (!player) return false; // Avoid undefined check errors

  return (
    checkDirection(row, col, 0, 1, player) || // Horizontal
    checkDirection(row, col, 1, 0, player) || // Vertical
    checkDirection(row, col, 1, 1, player) || // Diagonal (\)
    checkDirection(row, col, 1, -1, player)   // Diagonal (/)
  );
};

// Function to check a specific direction
const checkDirection = (
  row: number,
  col: number,
  rowOffset: number,
  colOffset: number,
  player: Player
): boolean => {
  let count = 1;

  count += countConsecutive(row, col, rowOffset, colOffset, player); // Check in one direction
  count += countConsecutive(row, col, -rowOffset, -colOffset, player); // Check in the opposite direction

  return count >= 4; // If 4 or more in a row, we have a winner
};

// Function to count consecutive tiles of the same player in one direction
const countConsecutive = (
  row: number,
  col: number,
  rowOffset: number,
  colOffset: number,
  player: Player
): number => {
  let count = 0;
  let r = row + rowOffset;
  let c = col + colOffset;

  // Count while inside grid and the tile belongs to the same player
  while (r >= 0 && r < 7 && c >= 0 && c < 7 && tiles.value[r * 7 + c] === player) {
    count++;
    r += rowOffset;
    c += colOffset;
  }

  return count;
};

const newGame = () => {
  tiles.value.fill(undefined)
  winner.value = undefined
  turn.value = 'red'
}
</script>

<template>
  <header :class="turn">
    <div v-if="!winner">
      <h2>{{ turn }}'s turn</h2>
    </div>
    <div v-else>
      <h2>{{ winner }} wins!</h2>
      <button @click="newGame">New game</button>
    </div>
  </header>


  <!-- Selector Row -->
  <main class="selector">
    <span v-for="n in 7" @click="setTile(n)" :key="n" class="casilla" :class="turn">
      <svg xmlns="http://www.w3.org/2000/svg" height="14" width="8.5" viewBox="0 0 320 512">
        <path fill="#fdf0d5"
          d="M2 334.5c-3.8 8.8-2 19 4.6 26l136 144c4.5 4.8 10.8 7.5 17.4 7.5s12.9-2.7 17.4-7.5l136-144c6.6-7 8.4-17.2 4.6-26s-12.5-14.5-22-14.5l-72 0 0-288c0-17.7-14.3-32-32-32L128 0C110.3 0 96 14.3 96 32l0 288-72 0c-9.6 0-18.2 5.7-22 14.5z" />
      </svg>
    </span>
  </main>

  <!-- Game Board -->
  <main class="tablero">
    <span v-for="tile in tiles" class="casilla" :class="tile"></span>
  </main>
</template>

<style scoped>
.selector, .tablero {
  max-width: 90%;
}

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

header {
  width: 100%;
}

header.blue {
  background-color: #669bbc;
} 

header.red {
  background-color: #780000;
} 

header div {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 1rem;
}
</style>
