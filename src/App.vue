<script setup>
  import Todos from '@/components/Todos.vue'
  import "./assets/styles/libs/reset.scss"
  
</script>

<template>
  <div>
    <Todos />
  </div>
</template>


<style lang="scss">
@import "./assets/styles/libs/allColors.scss";
body {
  background-color: $color-primary;
}
</style>
