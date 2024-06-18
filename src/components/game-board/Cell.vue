<script setup lang="ts">
  interface ICellProps {
    cell: string;
    index: number; 
    onClick: (index: number) => void; 
  }

  const props = defineProps<ICellProps>(); 

  const handleClick = () => {
    props.onClick(props.index);
  };  
</script>

<template>
  <div class="cell" @click="handleClick">
    {{ props.cell }}
  </div>
</template>

<style scoped lang="scss">
  .cell {
    display: flex;
    align-items: center;
    justify-content: center;
    border: 1px solid #ccc;
    height: 100px;
    font-size: 5rem;
    cursor: pointer;

    &:hover {
      background-color: rgb(66, 66, 66);
    }

    &:active {
      background-color: rgb(46, 46, 46);
    }

    &:first-child {
      border-radius: 20px 0 0 0;
    }

    &:nth-child(3) {
      border-radius: 0 20px 0 0;
    }

    &:nth-child(7) {
      border-radius:  0 0 0 20px;
    }

    &:nth-child(9) {
      border-radius: 0 0 20px 0;
    }
  }
</style>
