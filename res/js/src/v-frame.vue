<style scoped>
  /* common styles */
  .user-select-none,
  .user-select-none * {
    user-select: none !important;
  }

  .frame {
    display: flex;
    flex-wrap: nowrap;
    justify-content: flex-start;
    align-content: flex-start;
    align-items: flex-start;
    width: 100%;
    height: 100%;
    box-sizing: border-box;
  }

  .frame-panel {
    width: 100%;
    height: 100%;
  }

  .frame-inner {
    width: 100%;
    height: 100%;
    flex-shrink: 999999;
  }

  /* horizontal styles */
  .frame-horizontal {
    flex-direction: row;
  }

  .frame-horizontal .frame-panel {
    overflow-x: hidden;
  }

  .frame-horizontal .frame-separator {
    height: 100%;
    width: 0px;
    z-index: 1;
    transform: translateZ(1px);
  }

  .frame-horizontal .frame-separator-inner {
    height: 100%;
    width: 0;
    z-index: 2;
    transform: translateZ(2px);
    position: relative;
  }

  .frame-horizontal .frame-separator-visible-area {
    width: 2px;
    height: 100%;
    background-color: black;
    position: absolute;
    top: 0;
    left: -1px;
    z-index: 3;
    transform: translateZ(3px);
  }

  .frame-horizontal .frame-separator-grab-area {
    opacity: 0;
    position: absolute;
    top: 0;
    height: 100%;
    cursor: col-resize;
    z-index: 4;
    transform: translateZ(4px);
  }

  /* vertical styles */
  .frame-vertical {
    flex-direction: column;
  }

  .frame-horizontal .frame-panel {
    overflow-y: hidden;
  }

  .frame-vertical .frame-separator {
    height: 0;
    width: 100%;
    z-index: 1;
    transform: translateZ(1px);
  }

  .frame-vertical .frame-separator-inner {
    height: 0;
    width: 100%;
    z-index: 2;
    transform: translateZ(2px);
    position: relative;
  }

  .frame-vertical .frame-separator-visible-area {
    width: 100%;
    height: 2px;
    background-color: black;
    position: absolute;
    top: -1px;
    left: 0;
    z-index: 3;
    transform: translateZ(3px);
  }

  .frame-vertical .frame-separator-grab-area {
    opacity: 0;
    position: absolute;
    left: 0;
    width: 100%;
    cursor: row-resize;
    z-index: 4;
    transform: translateZ(4px);
  }
</style>

<template>
  <div
    ref="frame"
    class="frame"
    :class="{
      'frame-horizontal': orientation === 'horizontal',
      'frame-vertical': orientation === 'vertical',
    }">
    <div
      v-for="i in range(0, slots * 2 - 1)"
      :key="i"
      :class="{ 'frame-panel': i % 2 === 0, 'frame-separator': i % 2 !== 0 }"
      :style="`${orientation === 'vertical' ? 'height' : 'width'}: ${
        i % 2 === 0 ? 100 * innerWidths[i / 2] + '%' : '0px'
      }`">
      <slot v-if="i % 2 === 0" :name="'slot' + i / 2"></slot>

      <div v-else class="frame-separator-inner">
        <div
          class="frame-separator-visible-area"
          :style="separatorStyles"></div>

        <div
          class="frame-separator-grab-area"
          :style="`${
            orientation === 'vertical' ? 'height' : 'width'
          }: ${grabWidthPixels}px; ${
            orientation === 'vertical' ? 'top' : 'left'
          }: ${-grabWidthPixels / 2}px;`"
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
      orientation: {
        type: String,
        required: false,
        default: () => "horizontal",
      },

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
          const dim = self.orientation === "vertical" ? rect.height : rect.width
          self.innerWidths = self.widthsAsPixels.map(p => p / dim)
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

        const movement =
          self.orientation === "vertical" ? event.movementY : event.movementX
        const dim = self.orientation === "vertical" ? rect.height : rect.width
        const delta = movement / dim

        const minWidth =
          typeof self.minWidthPercent !== "undefined"
            ? parseFloat(self.minWidthPercent)
            : typeof self.minWidthPixels !== "undefined"
            ? parseFloat(self.minWidthPixels) / dim
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
