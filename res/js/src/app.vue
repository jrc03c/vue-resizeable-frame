<style scoped>
  .app {
    width: 1024px;
    height: 512px;
  }
</style>

<template>
  <div class="app">
    <v-frame
      :slots="4"
      :widths-as-percents="[0.25, 0.25, 0.25, 0.25]"
      :min-width-percent="0.1"
      separator-styles="background-color: gray; width: 8px; left: -4px; border-radius: 4px;"
      :grab-width-pixels="24">
      <template v-slot:slot0>
        <x-a></x-a>
      </template>

      <template v-slot:slot1>
        <x-b></x-b>
      </template>

      <template v-slot:slot2>
        <x-a></x-a>
      </template>

      <template v-slot:slot3>
        <x-b></x-b>
      </template>
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
