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

  .frame-inner {
    width: 100%;
    height: 100%;
    flex-shrink: 999999;
  }

  .frame-panel {
    width: 100%;
    height: 100%;
  }

  .frame-separator {
    height: 100%;
    cursor: col-resize;
    background-color: black;
  }

  .frame-separator-grab-area {
    background-color: rgba(255, 0, 0, 0.33);
    width: 18px;
    margin-left: -8px;
    height: 100%;
    cursor: col-resize;
    z-index: 1;
    transform: translateZ(1px);
  }
</style>

<template>
  <div class="frame">
    <div
      v-for="i in range(0, slots * 2 - 1)"
      :key="i"
      :class="{ 'frame-panel': i % 2 === 0, 'frame-separator': i % 2 !== 0 }"
      :style="`width: ${i % 2 === 0 ? 100 * innerWidths[i / 2] + '%' : '2px'}`">
      <slot v-if="i % 2 === 0" :name="'slot' + i / 2"></slot>
      <div v-else class="frame-separator-grab-area"></div>
    </div>
  </div>
</template>

<script>
  function range(a, b) {
    const out = []
    for (let i = a; i < b; i++) out.push(i)
    return out
  }

  export default {
    name: "v-frame",

    props: {
      slots: {
        type: Number,
        required: true,
        default: 2,
      },

      widths: {
        type: Array,
        required: false,
        default: () => [],
      },
    },

    data() {
      return {
        innerWidths: [],
      }
    },

    watch: {
      widths: {
        deep: true,

        handler() {
          const self = this
          self.onWidthsChange()
        },
      },
    },

    methods: {
      range,

      onWidthsChange() {
        const self = this

        if (self.widths.length > 0) {
          self.innerWidths = self.widths
        } else {
          self.innerWidths = range(0, self.slots).map(() => 1 / self.slots)
        }
      },
    },

    mounted() {
      const self = this
      self.onWidthsChange()
    },
  }
</script>
