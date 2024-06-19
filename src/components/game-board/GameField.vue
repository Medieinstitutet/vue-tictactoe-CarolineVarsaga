<script setup lang="ts">
  import { ref, computed, onMounted, onUnmounted } from "vue";
  import WinningPlayer from "./WinningPlayer.vue";
  import LandingPage from "../landing-page/LandingPage.vue";
  import Cell from "./Cell.vue";
  import Highscore from "../highscore-page/Highscore.vue";
  import Button from "../Button.vue";

  interface IGameBoardProps {
    playerX: string; 
    playerO: string; 
    isSinglePlayer: boolean;
  }
  
  const props = defineProps<IGameBoardProps>(); 
  const BOARD_SIZE = 3; 

  let initialState = {
    isXNext: true,
    cells: new Array(BOARD_SIZE * BOARD_SIZE).fill(""),
    winner: null
  };
  
  const localStorageGameKey = "tic-tac-toe-game";

  if (localStorage.getItem(localStorageGameKey)) {
    try {
      initialState = JSON.parse(localStorage.getItem(localStorageGameKey) || "");
    } catch (error) {
      console.error("Error parsing localStorage data:", error);
    }
  } 
 
  const playerXScore = ref<number>(parseInt(localStorage.getItem("playerXScore") ?? "0", 10));
  const playerOScore = ref<number>(parseInt(localStorage.getItem("playerOScore") ?? "0", 10)); 

  let cells = ref<string[]>(initialState.cells);
  let isXNext = ref<boolean>(initialState.isXNext);
  let winner = ref<string | null>(initialState.winner);

  const currentPlayerName = computed(() => isXNext.value ? props.playerX : props.playerO);

  const showLandingPage = ref<boolean>(false);
  const showHighscore = ref<boolean>(false); 
  const isSinglePlayer = ref(props.isSinglePlayer);

  const saveGameState = () => {
    const gameState = JSON.stringify({ isXNext: isXNext.value, cells: cells.value, winner: winner.value });
    localStorage.setItem(localStorageGameKey, gameState);
  };

  const loadGameState = () => {
    const gameState = localStorage.getItem(localStorageGameKey);
    if (gameState) {
      const parsedState = JSON.parse(gameState);
      isXNext.value = parsedState.isXNext; 
      cells.value = parsedState.cells; 
      winner.value = parsedState.winner; 
    } else {
      randomizeStartPlayer();
    }
  };



  const makeMove = (index: number, player: string) => {
    cells.value[index] = player;
    checkWinner();
  };

  const handlePostMove = () => {
    if (winner.value) {
      saveGameState();
    } else if (isBoardFull()) {
      winner.value = "No one";
      saveGameState();
    } else {
      isXNext.value = !isXNext.value;
      if (isSinglePlayer.value && currentPlayerName.value === "Computer" && !winner.value) {
        setTimeout(computerMove, 500); 
      }
      saveGameState();
    }
  };

  const computerMove = () => {
    if (!winner.value && currentPlayerName.value === "Computer") {
      const emptyCells = cells.value
        .map((cell, index) => (cell === "" ? index : null))
        .filter(index => index !== null);

      if (emptyCells.length > 0) {
        const randomIndex = emptyCells[Math.floor(Math.random() * emptyCells.length)] as number;
        const computerSymbol = localStorage.getItem("playerX") === "Computer" ? "X" : "O";
        makeMove(randomIndex, computerSymbol);
        handlePostMove();
      }
    }   
  };

  const cellClicked = (index: number) => {
    if (cells.value[index] === "" && !winner.value && currentPlayerName.value !== "Computer") {
      makeMove(index, isXNext.value ? "X" : "O");
      handlePostMove();
    }
  };

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
        const winningPlayer = cells.value[a] === "X" ? localStorage.getItem("playerX") : localStorage.getItem("playerO");
        winner.value = winningPlayer;

        updateScore(winningPlayer);

        localStorage.setItem("winner", winner.value || "");
        saveGameState();
      }
    }
  };    

  const updateScore = (winningPlayer: string | null) => {
    winningPlayer === localStorage.getItem("playerX")
      ? (playerXScore.value += 1, localStorage.setItem("playerXScore", playerXScore.value.toString()))
      : winningPlayer === localStorage.getItem("playerO")
      ? (playerOScore.value += 1, localStorage.setItem("playerOScore", playerOScore.value.toString()))
      : null;
  };

  const isBoardFull = () => {
    return cells.value.every(cell => cell !== "");
  }; 

  const playAgain = () => {
    cells.value.fill("");
    localStorage.removeItem("tic-tac-toe-game");
    winner.value = null;
    randomizeStartPlayer();
    if (isSinglePlayer.value && !isXNext.value) {
      setTimeout(computerMove, 500);
    }
  };

  const randomizeStartPlayer = () => {
    isXNext.value = Math.random() < 0.5;    
  };

  const backToStartButton = () => {
    localStorage.clear(); 
    winner.value = null; 
    cells.value.fill(""); 
    isXNext.value = true; 
    showLandingPage.value = true; 
  }

  const viewHighscore = () => {
    showHighscore.value = true; 
  } 

  localStorage.getItem("playerX") && localStorage.getItem("playerO") ? loadGameState() : backToStartButton();

  onMounted(() => {
    window.addEventListener("beforeunload", saveGameState);
    if (isSinglePlayer.value && !isXNext.value) {
      setTimeout(computerMove, 500);
    }
  });

  onUnmounted(() => {
    window.removeEventListener("beforeunload", saveGameState);
  });
</script>

<template>
  <div class="gameBoard" v-show="!showLandingPage && !showHighscore">
    <h1>Tic Tac Toe</h1>
    <WinningPlayer v-if="winner" :winner="winner" />
    <h2 v-else>Your turn, {{ currentPlayerName }}!</h2>

    <div class="board">
      <Cell v-for="(cell, index) in cells"
        :class="{ 'cell': true, 'x': cell === 'X', 'o': cell === 'O' }"
        :key="index"
        :cell="cell"
        :index="index"
        @click="cellClicked(index)"
      />      
    </div> 
    <div class="button-container">
      <Button @click="viewHighscore">View highscore</Button>
      <Button v-if="winner" @click="playAgain">Play again</Button>
      <Button @click="backToStartButton" class="backToStartButton">Back to start</Button>
    </div> 
  </div>
  <LandingPage v-if="showLandingPage" />
  <Highscore 
    v-if="showHighscore" 
    :playerX="playerX"
    :playerO="playerO"
    :playerXScore="playerXScore"
    :playerOScore="playerOScore"
  />  
</template>

<style scoped lang="scss">
  @keyframes pulseX {
    0% {
      transform: scale(1);
    }
    50% {
      transform: scale(1.1);
      background-color: rgb(96, 57, 116);
    }
    100% {
      transform: scale(1);
    }
  }
  @keyframes pulseO {
    0% {
      transform: scale(1);
    }
    50% {
      transform: scale(1.1);
      background-color: rgb(116, 97, 57);
    }
    100% {
      transform: scale(1);
    }
  }

  .board {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    width: 300px; 
    margin: 20px auto;

    .x {
      animation: pulseX 0.5s;    
      color: rgb(146, 146, 255); 
    }

    .o {
      animation: pulseO 0.5s;    
      color: rgb(250, 196, 95); 
    }
  }

  .button-container {
      display: flex;
      flex-direction: column;
      gap: 0.5rem; 

      .backToStartButton {
        background-color: rgb(107, 14, 14); 
        margin-top: 0.8rem; 

      &:hover {
        background-color: rgb(142, 28, 28); 
        border: 1px solid rgb(213, 8, 8); 
      }

      &:active {
        background-color: rgb(87, 8, 8); 
      }
    } 
  }   
</style>