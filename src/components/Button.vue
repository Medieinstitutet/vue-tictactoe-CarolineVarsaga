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
    background-color: #ae5f0c; 
    width: 300px; 

    &:hover {
      background-color: #d57c2d;
      border: 1px solid #ff8c00; 
    }

    &:active {
      background-color: #ae5f0c; 
    }
  }
</style>