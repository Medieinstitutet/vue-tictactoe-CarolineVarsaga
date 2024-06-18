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

<style scoped lang="scss">
  button {
    background-color: rgb(174, 95, 12); 
    width: 300px; 

    &:hover {
      background-color: rgb(213, 124, 45);
      border: 1px solid rgb(255, 140, 0); 
    }

    &:active {
      background-color: rgb(174, 95, 12); 
    }
  }
</style>