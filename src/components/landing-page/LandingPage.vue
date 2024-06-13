<script setup lang="ts">
import { ref } from "vue"
import AddPlayer from "./AddPlayer.vue"; 
import GameApp from "../../components/game-board/GameApp.vue";

const playerX = ref<string>(localStorage.getItem("playerX") || "");
const playerO = ref<string>(localStorage.getItem("playerO") || "");

const handlePlayerSaved = () => {
  playerX.value = localStorage.getItem("playerX") || "";
  playerO.value = localStorage.getItem("playerO") || "";
}; 
</script>

<template>
  <div>
    <AddPlayer v-if="!playerX || !playerO" @playerSaved="handlePlayerSaved" />
    <GameApp v-else />
  </div>
</template>

<style scoped>

</style>
