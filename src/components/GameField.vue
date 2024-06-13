<script setup lang="ts">
import { ref, onMounted, computed } from 'vue';
import WinningPlayer from './WinningPlayer.vue';

const BOARD_SIZE = 3; 
const cells = ref<Array<string>>(new Array(BOARD_SIZE * BOARD_SIZE).fill(""));

const playerX = ref<string>(localStorage.getItem('playerX') || 'Player X');
const playerO = ref<string>(localStorage.getItem('playerO') || 'Player O'); 

const isXNext = ref<boolean>(true);

const winner = ref<string | null>(null); 

const currentPlayerName = computed(() => isXNext.value ? playerX.value : playerO.value);

const cellClicked = (index: number) => {
  if (cells.value[index] !== "" || winner.value) return; 
  cells.value[index] = isXNext.value ? 'X' : 'O';
  checkWinner();
  if (!winner.value && isBoardFull()) {
    winner.value = 'No one';
  }
  if (!winner.value) {
    isXNext.value = !isXNext.value;
  } 
}

const checkWinner = () => {
  const winningCombinations = [
    [0, 1, 2], 
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8], 
    [2, 4, 6]
  ];

  winningCombinations.forEach(combination => {
    const [a, b, c] = combination;
    if (cells.value[a] && cells.value[a] === cells.value[b] && cells.value[a] === cells.value[c]) {
      winner.value = cells.value[a] === 'X' ? playerX.value : playerO.value;
    }
  });
};

const isBoardFull = () => {
  return cells.value.every(cell => cell !== '');
};

onMounted(() => {
  isXNext.value = Math.random() < 0.5;
});

</script>

<template>
  <WinningPlayer v-if="winner" :winner="winner" />
  <h2 v-else>Your turn, {{ currentPlayerName }}!</h2>
  <div class="board">
    <div v-for="(cell, index) in cells" :key="index" class="cell" @click="cellClicked(index)"> 
      {{ cell }}
    </div>
  </div>

  <div class="button-container">
    <button>View highscore</button>
    <button>Back to start</button>
  </div>

</template>

<style scoped>
  .board {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    width: 300px; 
    margin: 20px auto;
  }

  .cell {
    display: flex;
    align-items: center;
    justify-content: center;
    border: 1px solid #ccc;
    height: 100px; 
    font-size: 5rem; 
    cursor: pointer;
  }

  .button-container {
    display: flex;
    flex-direction: column;
    gap: 0.5rem; 
  }

</style>