<style scoped>
  .app-horizontal {
    width: calc(100vw - 3em);
    height: calc(100vh - 3em);
    border: 2px solid black;
    margin: 1.5em;
  }

  p {
    margin: 0;
    padding: 1.5em;
    width: calc(100% - 3em);
    height: auto;
  }

  .custom-frame-panel {
    background-color: red;
  }
</style>

<template>
  <div class="app-horizontal">
    <v-frame
      orientation="horizontal"
      :slots="4"
      :sizes-as-percents="[0.1, 0.1, 0.1, 0.7]"
      :min-size-percent="0.05"
      :grab-size-pixels="24"
      :panel-styles="
        ['ff0000', 'ffa500', 'ffff00', '008000'].map(
          v => `background-color: #${v}50;`
        )
      ">
      <template v-slot:slot0>
        <p>
          <b>Lorem ipsum</b>
          dolor sit amet, consectetur adipiscing elit. Fusce ac efficitur
          ligula, eu laoreet metus. In eu dolor eu ex iaculis lobortis. Praesent
          iaculis porta eros, quis dictum dolor finibus id. Quisque blandit non
          enim non ultrices. Nulla in massa id diam molestie sodales. Praesent
          imperdiet semper sapien, sit amet rhoncus massa. Aenean ultrices
          bibendum purus nec dictum.
        </p>
      </template>

      <template v-slot:slot1>
        <p>
          <b>Morbi purus</b>
          augue, lacinia vitae elementum vitae, commodo quis nibh. Cras sed
          lacinia sem. Etiam est sem, pretium ac rhoncus et, dignissim non
          ipsum. Duis tempus malesuada purus nec dapibus. Aenean elementum
          commodo lacinia. Pellentesque id tempus libero. Curabitur arcu velit,
          venenatis nec metus aliquet, ultricies rutrum lacus. Vivamus sed est
          non ligula luctus consectetur. Proin fringilla viverra purus ac
          tristique. Donec in ultricies nisi, nec imperdiet tellus. Nulla
          euismod nulla dolor, in ullamcorper nibh venenatis a. Ut tincidunt
          lectus varius dolor ullamcorper ullamcorper. Vestibulum quis mattis
          sapien, ac auctor ante.
        </p>
      </template>

      <template v-slot:slot2>
        <p>
          <b>Mauris et</b>
          aliquam ligula. Cras varius ut nunc sed mattis. Nullam tempus at arcu
          a porttitor. Sed lobortis massa sit amet purus egestas vestibulum.
          Integer rutrum nunc sed tortor dictum facilisis gravida eget orci.
          Maecenas egestas ligula augue, et mollis est tristique vel. In hac
          habitasse platea dictumst. Cras auctor accumsan leo, nec semper tortor
          efficitur eu. Proin ut tempus tortor. Vestibulum euismod imperdiet
          neque sit amet tincidunt. Duis dictum mauris sem, quis ultricies dolor
          posuere eu. Vestibulum ante ipsum primis in faucibus orci luctus et
          ultrices posuere cubilia curae;
        </p>
      </template>

      <template v-slot:slot3>
        <p>
          <b>Ut vitae</b>
          lorem pulvinar, pharetra nisl in, condimentum lectus. Sed eu justo
          dapibus nisl malesuada congue. Nullam ultricies nisi nunc, a egestas
          ligula faucibus ut. Mauris sed lacus sed purus pulvinar lacinia. Nunc
          eget tellus ac enim faucibus mollis at in arcu. Donec placerat, diam
          ac posuere porttitor, sapien sem cursus purus, at sagittis urna nulla
          id lacus. Sed id congue mi, a molestie ipsum. Aliquam erat volutpat.
          Donec imperdiet sem eros, nec varius lacus pharetra eget. Sed ac
          viverra purus. Etiam gravida quis ligula non congue.
        </p>
      </template>
    </v-frame>
  </div>
</template>

<script>
  import VFrameComponent from "./v-frame.vue"

  export default {
    components: {
      "v-frame": VFrameComponent,
    },
  }
</script>
