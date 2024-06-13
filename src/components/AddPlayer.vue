<script setup lang="ts">
import { ref, defineEmits } from "vue"

const emits = defineEmits(['playerSaved']);

const inputName = ref<string>("");
const playerX = ref<string>(localStorage.getItem('playerX') || '');
const playerO = ref<string>(localStorage.getItem('playerO') || '');
const playerSpanText = ref<string>("Player X: ");

const updatePlayerText = (isPlayerX: boolean, playerValue: string) => {
  if (isPlayerX) {
    playerX.value = playerValue;
    playerSpanText.value = 'Player O: ';
  } else {
    playerO.value = playerValue;
  }
  inputName.value = '';
};

const savePlayerName = () => {
  if (inputName.value.trim()) {
    const isPlayerX = !playerX.value;
    const playerKey = isPlayerX ? "playerX" : "playerO";
    const playerValue = inputName.value;

    localStorage.setItem(playerKey, playerValue);
    updatePlayerText(isPlayerX, playerValue); 
    emits("playerSaved"); 
  } else {
    alert("Please, enter a player name");
  }
}
</script>

<template>
  <h2>Enter player name</h2>
  <div>
    <span>{{ playerSpanText }}</span> 
    <input v-model="inputName" type="text" minlength="1"/>
    <button @click="savePlayerName">Save</button>
  </div>
</template>

<style scoped></style>


/*
Starta ett spel när man trycker på Spara andra gången 
Visa spelkomponenten 
*/