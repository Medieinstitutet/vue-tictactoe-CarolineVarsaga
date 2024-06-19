<script setup lang="ts">
  import { ref } from "vue"
  import AddPlayer from "./AddPlayer.vue"; 
  import GameField from "../../components/game-board/GameField.vue";

  const playerX = ref<string>(localStorage.getItem("playerX") || "");
  const playerO = ref<string>(localStorage.getItem("playerO") || "");

  const handlePlayerSaved = () => {
    playerX.value = localStorage.getItem("playerX") || "";
    playerO.value = localStorage.getItem("playerO") || "";
  }; 

</script>

<template>
  <div>
    <AddPlayer 
      v-if="!playerX || !playerO" 
      @playerSaved="handlePlayerSaved" 
      :playerX="playerX"
      :playerO="playerO"
    />
    <GameField v-else 
      :playerX="playerX"
      :playerO="playerO"
      :isSinglePlayer="true"
    />
  </div>
</template>

<style scoped>

</style>
