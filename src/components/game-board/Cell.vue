<script setup lang="ts">

  interface ICellProps {
    key: number; 
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

<style scoped>
.cell {
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid #ccc;
  height: 100px;
  font-size: 5rem;
  cursor: pointer;
}
</style>
