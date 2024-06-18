<script setup lang="ts">
  import { ref } from "vue"; 
  import Button from "../Button.vue";
  import GameField from "../game-board/GameField.vue";

  interface IHighscoreProps {
    playerX: string; 
    playerO: string; 
    playerXScore: number; 
    playerOScore: number; 
  }

  const props = defineProps<IHighscoreProps>(); 

  const leader = () => {
    if (props.playerXScore > props.playerOScore) {
      return props.playerX;
    } else if (props.playerOScore > props.playerXScore) {
      return props.playerO; 
    } else {
      return "no one";
    }
  }

  const winner = leader(); 
  const showGameField = ref<boolean>(false);

  const backToGame = () => {
    showGameField.value = true; 
  }
</script>

<template>
  <div class="highscore-container" v-show="!showGameField">
    <h2>Highscore</h2> 
    <p>And the best of the best is... {{ winner }} !</p>
    <table>
      <tr>
        <th>Name</th>
        <th>Points</th>
      </tr>
      <tr>
        <td>{{ playerX }}</td>
        <td>{{ playerXScore ?? 0 }}</td>
      </tr>
      <tr>
        <td>{{ playerO }}</td>
        <td>{{ playerOScore ?? 0 }}</td>
      </tr>
    </table>
    <Button @click="backToGame">Game board</Button>
  </div>
  
  <GameField 
    v-if="showGameField"
    :playerX="playerX"
    :playerO="playerO"
  /> 
</template>

<style scoped>
  @keyframes pulse {
    0% {
      transform: scale(1) rotate(0deg);
    }
    50% {
      transform: scale(1.1) rotate(-5deg);
    }
    100% {
      transform: scale(1) rotate(0deg);
    }
  }

  .highscore-container {
    display: flex; 
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 1rem; 
    border: 0.5rem dotted; 
    border-radius: 20px;
    height: 25rem;
    width: 25rem;
  }

  h2 {
    text-transform: uppercase;
    animation: pulse 1.8s infinite;
    display: inline-block;
  }

  tr {
    display: grid;
    grid-template-columns: repeat(2, 1fr); 
    gap: 5rem; 
  }
</style>