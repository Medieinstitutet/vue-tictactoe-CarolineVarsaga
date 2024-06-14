<script setup lang="ts">

</script>

<template>
  <div class="highscore-container">
    <h2>Highscore</h2>  
    <table>
      <tr>
        <th>Name</th>
        <th>Points</th>
      </tr>
      <tr>
        <td>Caroline</td>
        <td>0</td>
      </tr>
      <tr>
        <td>T</td>
        <td>2</td>
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
