<script setup lang="ts">

  let playerXScore: number = parseInt(localStorage.getItem('playerXScore') ?? '0', 10);
  let playerOScore: number = parseInt(localStorage.getItem('playerOScore') ?? '0', 10);
  const playerX = localStorage.getItem('playerX');
  const playerO = localStorage.getItem('playerO');

  const leader = () => {
    if (playerXScore > playerOScore) {
      return playerX;
    } else if (playerOScore > playerXScore) {
      return playerO; 
    } else {
      return "no one";
    }
  }

  const winner = leader(); 

</script>

<template>
  <div class="highscore-container">
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
  </div>
</template>

<style scoped>
  .highscore-container {
    display: flex; 
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }

  tr {
    display: grid;
    grid-template-columns: repeat(2, 1fr); 
    gap: 5rem; 
  }
</style>
