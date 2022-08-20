<style scoped>
  .frame {
    border: 2px solid black;
    display: flex;
    flex-direction: row;
    flex-wrap: nowrap;
    justify-content: flex-start;
    align-content: flex-start;
    align-items: flex-start;
    width: 100%;
    height: 100%;
    box-sizing: border-box;
  }
</style>

<template>
  <div class="frame">
    <slot></slot>
  </div>
</template>

<script>
  export default {
    name: "v-frame",
  }
</script>
