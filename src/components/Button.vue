<script setup lang="ts">

  const props = defineProps({
    onClick: Function,
  });

  const handleClick = () => {
    if (props.onClick) {
      props.onClick();
    }
  };
</script>

<template>
  <button @click="handleClick">
    <slot></slot>
  </button>
</template>

<style scoped></style>