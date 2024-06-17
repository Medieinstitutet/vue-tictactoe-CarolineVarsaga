<script setup lang="ts">
  import { ref, watch } from "vue"

  interface IAddPlayerProps {
    playerX: string; 
    playerO: string; 
  }

  const props = defineProps<IAddPlayerProps>(); 
  const emits = defineEmits(["playerSaved"]); 

  const inputName = ref<string>("");
  const playerSpanText = ref<string>("Player X: ");

  const localPlayerX = ref<string>(props.playerX);
  const localPlayerO = ref<string>(props.playerO);

  const updatePlayerText = (isPlayerX: boolean, playerValue: string) => {
    if (isPlayerX) {
      localPlayerX.value = playerValue;
      playerSpanText.value = "Player O: ";
    } else {
      localPlayerO.value = playerValue;
    }
    inputName.value = "";
};

  watch(() => props.playerX, (newVal) => {
    localPlayerX.value = newVal;
  });

  watch(() => props.playerO, (newVal) => {
    localPlayerO.value = newVal;
  }); 

  const savePlayerName = () => {
    if (inputName.value.trim()) {
      const isPlayerX = !localPlayerX.value;
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