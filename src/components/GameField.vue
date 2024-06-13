<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'

const BOARD_SIZE = 3; 
const cells = ref<Array<string>>(new Array(BOARD_SIZE * BOARD_SIZE).fill(""));

const playerX = ref<string>(localStorage.getItem('playerX') || 'Player X');
const playerO = ref<string>(localStorage.getItem('playerO') || 'Player O'); 

const isXNext = ref<boolean>(true);

const currentPlayerName = computed(() => isXNext.value ? playerX.value : playerO.value);

const cellClicked = (index: number) => {
  if (cells.value[index] !== "") return; 
  cells.value[index] = isXNext.value ? 'X' : 'O';
  isXNext.value = !isXNext.value;
}

onMounted(() => {
  isXNext.value = Math.random() < 0.5;
});

</script>

<template>

  <h2>Game on</h2>
  <h2>Your turn, {{ currentPlayerName }}!</h2>

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