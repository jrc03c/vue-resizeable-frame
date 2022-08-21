<style scoped>
  .user-select-none,
  .user-select-none * {
    user-select: none !important;
  }

  .frame {
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
    overflow-x: hidden;
  }

  .frame-separator {
    height: 100%;
    width: 0px;
    z-index: 1;
    transform: translateZ(1px);
  }

  .frame-separator-inner {
    height: 100%;
    width: 0;
    z-index: 2;
    transform: translateZ(2px);
    position: relative;
  }

  .frame-separator-visible-area {
    width: 2px;
    height: 100%;
    background-color: black;
    position: absolute;
    top: 0;
    left: -1px;
    z-index: 3;
    transform: translateZ(3px);
  }

  .frame-separator-grab-area {
    opacity: 0;
    position: absolute;
    top: 0;
    height: 100%;
    cursor: col-resize;
    z-index: 4;
    transform: translateZ(4px);
  }
</style>

<template>
  <div ref="frame" class="frame">
    <div
      v-for="i in range(0, slots * 2 - 1)"
      :key="i"
      :class="{ 'frame-panel': i % 2 === 0, 'frame-separator': i % 2 !== 0 }"
      :style="`width: ${i % 2 === 0 ? 100 * innerWidths[i / 2] + '%' : '0px'}`">
      <slot v-if="i % 2 === 0" :name="'slot' + i / 2"></slot>

      <div v-else class="frame-separator-inner">
        <div
          class="frame-separator-visible-area"
          :style="separatorStyles"></div>

        <div
          class="frame-separator-grab-area"
          :style="`width: ${grabWidthPixels}px; left: ${
            -grabWidthPixels / 2
          }px;`"
          @mousedown="onMouseDown($event, (i - 1) / 2)"></div>
      </div>
    </div>
  </div>
</template>

<script>
  function range(a, b) {
    const out = []
    for (let i = a; i < b; i++) out.push(i)
    return out
  }

  function clamp(x, a, b) {
    if (x < a) return a
    if (x > b) return b
    return x
  }

  export default {
    name: "v-frame",

    props: {
      slots: {
        type: Number,
        required: true,
        default: 2,
      },

      "widths-as-percents": {
        type: Array,
        required: false,
        default: () => [],
      },

      "widths-as-pixels": {
        type: Array,
        required: false,
        default: () => [],
      },

      "min-width-percent": {
        type: Number,
        required: false,
        default: () => undefined,
      },

      "min-width-pixels": {
        type: Number,
        required: false,
        default: () => undefined,
      },

      "separator-styles": {
        type: String,
        required: false,
        default: () => "",
      },

      "grab-width-pixels": {
        type: Number,
        required: false,
        default: () => 24,
      },
    },

    data() {
      return {
        innerWidths: [],
        isResizing: false,
        resizingIndex: 0,
      }
    },

    watch: {
      "widths-as-percents": {
        deep: true,

        handler() {
          const self = this
          self.onWidthsChange()
        },
      },

      "widths-as-pixels": {
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

        if (self.widthsAsPercents.length > 0) {
          self.innerWidths = self.widthsAsPercents
        } else if (self.widthsAsPixels.length > 0) {
          const rect = self.$refs.frame.getBoundingClientRect()
          self.innerWidths = self.widthsAsPixels.map(p => p / rect.width)
        } else {
          self.innerWidths = range(0, self.slots).map(() => 1 / self.slots)
        }
      },

      onMouseDown(event, i) {
        const self = this
        self.isResizing = true
        self.resizingIndex = i
        window.addEventListener("mousemove", self.onMouseMove)
        window.addEventListener("mouseup", self.onMouseUp)
        self.$refs.frame.classList.add("user-select-none")
      },

      onMouseMove(event) {
        const self = this
        if (!self.isResizing) return

        const i = self.resizingIndex
        const rect = self.$refs.frame.getBoundingClientRect()

        const fixed = self.innerWidths
          .slice(i, i + 2)
          .reduce((a, b) => a + b, 0)

        const delta = event.movementX / rect.width

        const minWidth =
          typeof self.minWidthPercent !== "undefined"
            ? parseFloat(self.minWidthPercent)
            : typeof self.minWidthPixels !== "undefined"
            ? parseFloat(self.minWidthPixels) / rect.width
            : 0.1

        self.innerWidths[i] = clamp(
          self.innerWidths[i] + delta,
          minWidth,
          fixed - minWidth
        )

        if (i < self.innerWidths.length - 1) {
          self.innerWidths[i + 1] = fixed - self.innerWidths[i]
        }
      },

      onMouseUp() {
        const self = this
        self.isResizing = false
        self.resizingIndex = 0
        window.removeEventListener("mousemove", self.onMouseMove)
        window.removeEventListener("mouseup", self.onMouseUp)
        self.$refs.frame.classList.remove("user-select-none")
      },
    },

    mounted() {
      const self = this
      self.onWidthsChange()
    },
  }
</script>
