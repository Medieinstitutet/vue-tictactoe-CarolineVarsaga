<script setup lang="ts">
  import { ref, watch, computed, onMounted } from "vue"
  import Button from "../Button.vue";

  interface IAddPlayerProps {
    playerX: string; 
    playerO: string; 
  }

  const props = defineProps<IAddPlayerProps>(); 
  const emits = defineEmits(["playerSaved", "modeSelected"]); 

  const inputName = ref<string>("");
  const playerSpanText = ref<string>("Player X: ");

  const localPlayerX = ref<string>(props.playerX);
  const localPlayerO = ref<string>(props.playerO);

  const isSinglePlayer = ref<boolean>(true); 

  const isNotSinglePlayer = computed<boolean>({
    get() {
      return !isSinglePlayer.value;
    },
    set(value) {
      isSinglePlayer.value = !value;
    }
  });

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
    const playerValue = inputName.value.trim();

    if (playerValue) {
      const isPlayerXTaken = localStorage.getItem("playerX");
      const isPlayerOTaken = localStorage.getItem("playerO");

      if (playerValue.toLowerCase() === "computer") {
        alert("Name 'Computer' is reserved. Please choose a different name.");
        return; 
      }

      if (isSinglePlayer.value) {
        if (!isPlayerXTaken) {
          handleModeSelection(true);
          localStorage.setItem("playerX", playerValue);
          emits("playerSaved");
        } else if (!isPlayerOTaken) {
          handleModeSelection(true);
          localStorage.setItem("playerO", playerValue);
          emits("playerSaved");
        }
      } else if (isNotSinglePlayer.value) {
        const isPlayerX = playerSpanText.value === "Player X: ";
        const playerKey = isPlayerX ? "playerX" : "playerO";

        if (!localStorage.getItem(playerKey)) {
          localStorage.setItem(playerKey, playerValue);
          updatePlayerText(isPlayerX, playerValue);
          emits("playerSaved");
        }
      }
    } else {
      alert("Please, enter your player name");
    }
  };

  const handleModeSelection = (mode: boolean) => {
    isSinglePlayer.value = mode;
    emits("modeSelected", mode);
    if (mode) {
      randomizeStartPlayer();
    } else {
      localStorage.clear(); 
      localPlayerX.value = "";
      localPlayerO.value = "";      
    }
  };

  const randomizeStartPlayer = () => {
    const isPlayerXTaken = localStorage.getItem("playerX");
    const isPlayerOTaken = localStorage.getItem("playerO");

    if (!isPlayerXTaken && !isPlayerOTaken) {
      const randomNumber = Math.random();
      if (randomNumber < 0.5) {
        localPlayerX.value = "Computer";
        localPlayerO.value = "";
        localStorage.setItem("playerX", localPlayerX.value);
        localStorage.setItem("playerO", localPlayerO.value);
      } else {
        localPlayerX.value = ""; 
        localPlayerO.value = "Computer";
        localStorage.setItem("playerX", localPlayerX.value);
        localStorage.setItem("playerO", localPlayerO.value);
      }
    } else if (!isPlayerXTaken) {
      localPlayerX.value = "Computer";
      localStorage.setItem("playerX", localPlayerX.value);
    } else if (!isPlayerOTaken) {
      localPlayerO.value = "Computer";
      localStorage.setItem("playerO", localPlayerO.value);
    }
  };

  onMounted(() => {
    handleModeSelection(true); 
  });
</script>

<template>
  <h1>Tic Tac Toe</h1>
  <div class="howManyPlayers">
    <h2>How many players?</h2>
    <div class="one-player">
      <input type="radio" class="radio" id="onePlayerRadio" v-model="isSinglePlayer" :value=true @change="handleModeSelection(true)" checked/>
      <label>1 player (against computer)</label>
    </div>
    <div class="two-players">
      <input type="radio" class="radio" id="twoPlayersRadio" v-model="isNotSinglePlayer" :value="true" @change="handleModeSelection(false)"/> 
      <label>2 players (local)</label>
    </div>
  </div>
  <h2>Enter player name</h2>
  <div class="login">
    <span v-if="isNotSinglePlayer" class="playerNameText">{{ playerSpanText }}</span> 
    <span v-else="isSinglePlayer"class="playerNameText">Your name:</span>
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