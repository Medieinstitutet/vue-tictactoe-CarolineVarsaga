<script setup lang="ts">
  import { ref, watch } from "vue"
  import Button from "../Button.vue";

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

  const isSinglePlayer = ref<boolean>(true); 

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
      alert("Please, enter your player name");
    }
  } 

  const handlePlayerModeChange = (event: Event) => {
    const target = event.target as HTMLInputElement;
    isSinglePlayer.value = target.id === "onePlayerRadio";
  };
  
</script>

<template>
  <h1>Tic Tac Toe</h1>
  <div class="howManyPlayers">
    <h2>How many players?</h2>
    <div class="one-player">
      <input type="radio" class="radio" id="onePlayerRadio" v-model="isSinglePlayer" @change="handlePlayerModeChange"/>
      <label>1 player (against computer)</label>
    </div>
    <div class="two-players">
      <input type="radio" class="radio" id="twoPlayersRadio" v-model="isSinglePlayer" :value="false" @change="handlePlayerModeChange"/> 
      <label>2 players (local)</label>
    </div>
  </div>
  <h2>Enter player name</h2>
  <div class="login">
    <span class="playerNameText">{{ playerSpanText }}</span> 
    <input v-model="inputName" type="text" minlength="1"/>
    <Button @click="savePlayerName" class="saveButton">Save</Button>
  </div> 

</template>

<style scoped>
 @keyframes pulse {
    0% {
      transform: scale(1) rotate(0deg);
    }
    50% {
      transform: scale(1.2) rotate(-7deg);
    }
    100% {
      transform: scale(1) rotate(0deg);
    }
  }

  h1 {
    animation: pulse 1.8s infinite;
    display: inline-block;
  }

  .howManyPlayers {
    display: flex; 
    flex-direction: column; 
    gap: 1rem; 
    padding-bottom: 2rem; 
    border-bottom: 2px dashed #cc8a10; 
  }

  .radio {
    accent-color: #cc8a10; 
    width: 30px;
    height: 30px;
  }

  .one-player, .two-players{
    display: flex;
    align-self: flex-start; 
    align-items: center;
    justify-content: center;
  }

  label {
    margin-left: 0.5rem; 
  } 

  .login {
    display: flex; 
    flex-direction: column;
    gap: 1rem; 
    align-items: center; 
  }

  .playerNameText {
    font-size: 1.5rem;
  }

  input {
    height: 25px; 
    border-radius: 8px;
    border: 1px solid #ccc; 
    width: 250px;
    background-color: rgb(51, 51, 51);
    color: #ccc; 
  }

  .saveButton {
    width: 250px;
  }
</style>