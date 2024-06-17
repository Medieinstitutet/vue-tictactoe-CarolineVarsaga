<script setup lang="ts">
  import { ref, computed, onMounted, onUnmounted } from "vue";
  import WinningPlayer from "./WinningPlayer.vue";
  import LandingPage from "../landing-page/LandingPage.vue";
  import Cell from "./Cell.vue";
  //import Highscore from "../highscore-page/Highscore.vue";
  import Button from "../Button.vue";
  //import GameBoard from "./GameBoard.vue";

  interface IGameBoardProps {
    playerX: string; 
    playerO: string; 
    BOARD_SIZE: number; 
  }
  
  const props = defineProps<IGameBoardProps>(); 

  let initialState = {
    isXNext: true,
    cells: new Array(props.BOARD_SIZE * props.BOARD_SIZE).fill(""),
    winner: null
  };

  
  const localStorageKey = 'tic-tac-toe-game';

  if (localStorage.getItem(localStorageKey)) {
    try {
      initialState = JSON.parse(localStorage.getItem(localStorageKey) || '');
    } catch (error) {
      console.error('Error parsing localStorage data:', error);
    }
  } 


  const playerXScore = ref<number>(parseInt(localStorage.getItem('playerXScore') ?? '0', 10));
  const playerOScore = ref<number>(parseInt(localStorage.getItem('playerOScore') ?? '0', 10)); 

  const cells = ref<string[]>(initialState.cells);
  const isXNext = ref<boolean>(initialState.isXNext);
  const winner = ref<string | null>(initialState.winner);


  const currentPlayerName = computed(() => isXNext.value ? props.playerX : props.playerO);

  const showLandingPage = ref<boolean>(false);
  const showHighscore = ref<boolean>(false); 

  const saveGameState = () => {
    const gameState = JSON.stringify({ isXNext: isXNext.value, cells: cells.value, winner: winner.value });
    localStorage.setItem(localStorageKey, gameState);
  };

  const cellClicked = (index: number) => {
    if (cells.value[index] !== "" || winner.value) return; 
    cells.value[index] = isXNext.value ? "X" : "O";
    saveGameState();
    checkWinner();
    if (!winner.value && isBoardFull()) {
      winner.value = "No one";
    }
    if (!winner.value) {
      isXNext.value = !isXNext.value;
    } 
  }     

  const checkWinner = () => {
    if (winner.value) return;

    const winningCombinations = [
      [0, 1, 2], [3, 4, 5], [6, 7, 8],
      [0, 3, 6], [1, 4, 7], [2, 5, 8],
      [0, 4, 8], [2, 4, 6]
    ];

    for (const combination of winningCombinations) {
      const [a, b, c] = combination;
        
      if (cells.value[a] && cells.value[a] === cells.value[b] && cells.value[a] === cells.value[c]) {
        const winningPlayer = cells.value[a] === "X" ? localStorage.getItem('playerX') : localStorage.getItem('playerO');
        winner.value = winningPlayer;

        if (winner.value === localStorage.getItem('playerX')) {
          playerXScore.value += 1;
          localStorage.setItem('playerXScore', playerXScore.value.toString());
        } else if (winner.value === localStorage.getItem('playerO')) {
          playerOScore.value += 1;
          localStorage.setItem('playerOScore', playerOScore.value.toString());
        }

        localStorage.setItem('winner', winner.value ?? '');
        /* const emits = defineEmits(['winner']);
        emits("winner");  */
        break; 
      }
    }
  };  

  const isBoardFull = () => {
    return cells.value.every(cell => cell !== "");
  }; 

  const playAgain = () => {
    cells.value.fill("");
    winner.value = null; 
    randomizeStartPlayer(); 
    /* const emits = defineEmits(['play-again']);
    emits("play-again"); */
  }

  const randomizeStartPlayer = () => {
    isXNext.value = Math.random() < 0.5;
  };

  const backToStartButton = () => {
    localStorage.removeItem('playerX'); 
    localStorage.removeItem('playerO'); 
    localStorage.removeItem('playerXScore');
    localStorage.removeItem('playerOScore'); 
    localStorage.removeItem('winner'); 
    winner.value = null; 
    cells.value.fill(''); 
    isXNext.value = true; 
    showLandingPage.value = true; 
  }

  const viewHighscore = () => {
    showHighscore.value = true; 
  } 

  if (!localStorage.getItem('playerX') || !localStorage.getItem('playerO')) {
    backToStartButton();
  } else {
    randomizeStartPlayer();
  } 

  onMounted(() => {
    window.addEventListener('beforeunload', saveGameState);
  });

  onUnmounted(() => {
    window.removeEventListener('beforeunload', saveGameState);
  });

</script>

<template>
  <div class="gameBoard" v-if="!showLandingPage">
    <h1>Tic Tac Toe</h1>
    <WinningPlayer v-if="winner" :winner="winner" />
    <h2 v-else>Your turn, {{ currentPlayerName }}!</h2>

    <div class="board">
      <Cell v-for="(cell, index) in cells"
        :key="index"
        :cell="cell"
        :index="index"
        @click="cellClicked(index)"
      />
    </div> 
    <div class="button-container">
      <!-- <Button @click="viewHighscore">View highscore</Button> -->
      <Button v-if="winner" @click="playAgain">Play again</Button>
      <Button @click="backToStartButton">Back to start</Button>
    </div> 
  </div>
  <LandingPage v-if="showLandingPage" />
  <!-- <Highscore v-if="showHighscore" />  -->
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