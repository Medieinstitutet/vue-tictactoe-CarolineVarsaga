
<script setup lang="ts">
import { defineProps } from 'vue';

defineProps<{ winner: string | null }>();
</script>

<template>
  <div v-if="winner" class="winner">
    {{ winner }} wins!
  </div>
</template>

<style scoped>
  .winner {
    margin-top: 20px;
    font-size: 24px;
    color: green;
  }
</style>