<style scoped>
  .app-horizontal {
    width: 1024px;
    height: 64px;
  }

  .app-vertical {
    width: 256px;
    height: 512px;
  }
</style>

<template>
  <div class="app-horizontal">
    <v-frame
      :slots="4"
      :sizes-as-percents="[0.25, 0.25, 0.25, 0.25]"
      :min-size-percent="0.1"
      separator-styles="background-color: gray; width: 8px; left: -4px; border-radius: 4px;"
      :grab-size-pixels="24">
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

  <br />

  <div class="app-vertical">
    <v-frame
      orientation="vertical"
      :slots="4"
      :sizes-as-percents="[0.25, 0.25, 0.25, 0.25]"
      :min-size-percent="0.1"
      separator-styles="background-color: gray; height: 8px; top: -4px; border-radius: 4px;"
      :grab-size-pixels="24">
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
