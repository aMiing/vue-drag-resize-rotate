<template>
  <div class="view-box">
    <div id="toolbar">拖动组件触发事件dragging和停止拖拽dragstop</div>
    <div class="container">
      <vue-drag-resize-rotate :w="200" :h="200" :r="0" @dragging="onDrag" @dragstop="onDragStop">
        <p>
          {{ dragging ? '你正在拖动' : '我就待在这里' }}
          <br />
          X: {{ x }} / Y: {{ y }}
        </p>
      </vue-drag-resize-rotate>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      dragging: false,
      x: 0,
      y: 0
    }
  },
  beforeDestroy: function () {
    clearTimeout(this.timer)
  },
  methods: {
    onDrag: function (x, y) {
      this.dragging = true
      this.x = x
      this.y = y
    },
    onDragStop: function (x, y) {
      this.dragging = false
    }
  }
}
</script>

<style>
</style>