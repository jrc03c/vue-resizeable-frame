<style scoped>
  .app {
    width: 1024px;
    height: 512px;
  }
</style>

<template>
  <div class="app">
    <v-frame>
      <x-a></x-a>
      <x-b></x-b>
    </v-frame>
  </div>
</template>

<script>
  import VFrameComponent from "./v-frame.vue"
  import XAComponent from "./a.vue"
  import XBComponent from "./b.vue"

  export default {
    components: {
      "v-frame": VFrameComponent,
      "x-a": XAComponent,
      "x-b": XBComponent,
    },
  }
</script>
