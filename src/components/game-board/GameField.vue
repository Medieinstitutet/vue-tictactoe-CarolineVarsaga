<script setup lang="ts">
  import { ref, onMounted, computed } from "vue";
  import WinningPlayer from "./WinningPlayer.vue";
  import LandingPage from "../landing-page/LandingPage.vue";

  const BOARD_SIZE = 3; 
  const cells = ref<Array<string>>(new Array(BOARD_SIZE * BOARD_SIZE).fill(""));

  const playerX = ref<string>(localStorage.getItem("playerX") || "Player X");
  const playerO = ref<string>(localStorage.getItem("playerO") || "Player O"); 

  const isXNext = ref<boolean>(true);

  const winner = ref<string | null>(null); 

  const currentPlayerName = computed(() => isXNext.value ? playerX.value : playerO.value);

  const showLandingPage = ref<boolean>(false);

  const cellClicked = (index: number) => {
    if (cells.value[index] !== "" || winner.value) return; 
    cells.value[index] = isXNext.value ? "X" : "O";
    checkWinner();
    if (!winner.value && isBoardFull()) {
      winner.value = "No one";
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
        winner.value = cells.value[a] === "X" ? playerX.value : playerO.value;
      }
    });
  };

  const isBoardFull = () => {
    return cells.value.every(cell => cell !== "");
  };

  const playAgain = () => {
    cells.value.fill("");
    winner.value = null; 
    randomizeStartPlayer(); 
  }

  const randomizeStartPlayer = () => {
    isXNext.value = Math.random() < 0.5;
  };

  const backToStartButton = () => {
    localStorage.removeItem('playerX'); 
    localStorage.removeItem('playerO'); 
    winner.value = null; 
    cells.value.fill(''); 
    isXNext.value = true; 
    showLandingPage.value = true; 
  }

  /* onMounted(randomizeStartPlayer); */ 
  if (!localStorage.getItem('playerX') || !localStorage.getItem('playerO')) {
    backToStartButton();
  } else {
    randomizeStartPlayer();
  } 
</script>

<template>
  <div class="gameBoard" v-if="!showLandingPage">
    <h1>Tic Tac Toe</h1>
    <WinningPlayer v-if="winner" :winner="winner" />
    <h2 v-else>Your turn, {{ currentPlayerName }}!</h2>

    <div class="board">
      <div v-for="(cell, index) in cells" :key="index" class="cell" @click="cellClicked(index)"> 
        {{ cell }}
      </div>
    </div>

    <div class="button-container">
      <button>View highscore</button>
      <button v-if="winner" @click="playAgain">Play again</button>
      <button @click="backToStartButton">Back to start</button>
    </div>
  </div>
  <LandingPage v-if="showLandingPage" />
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