<script setup lang="ts">

  interface IButtonProps {
    onClick: Function;
  }
  const props = defineProps<IButtonProps>(); 

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