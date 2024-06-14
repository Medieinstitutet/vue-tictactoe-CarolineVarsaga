<script setup lang="ts">

defineProps<{ winner: string | null }>();
</script>

<template>
  <div v-if="winner" class="winner">
    {{ winner }} wins!
  </div>
</template>

<style scoped>
  .winner {
    font-size: 2rem;
    color: rgb(85, 193, 85); 
  }
</style>