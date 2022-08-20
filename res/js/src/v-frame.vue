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
    cursor: col-resize;
    background-color: black;
  }

  .frame-separator-grab-area {
    background-color: rgba(255, 0, 0, 0.33);
    width: 18px;
    margin-left: -9px;
    height: 100%;
    cursor: col-resize;
    z-index: 1;
    transform: translateZ(1px);
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

      <div
        v-else
        class="frame-separator-grab-area"
        @mousedown="onMouseDown($event, (i - 1) / 2)"></div>
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

      widths: {
        type: Array,
        required: false,
        default: () => [],
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
        const fixed = self.innerWidths[i] + self.innerWidths[i + 1]

        const left =
          i > 0
            ? self.innerWidths.slice(0, i - 1).reduce((a, b) => a + b, 0)
            : 0

        const right = left + fixed
        const minWidth = 128 / rect.width
        const delta = event.movementX / rect.width

        self.innerWidths[i] = clamp(
          self.innerWidths[i] + delta,
          left + minWidth,
          right - minWidth
        )

        self.innerWidths[i + 1] = fixed - self.innerWidths[i]
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
