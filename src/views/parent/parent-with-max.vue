<template>
  <div class="view-box">
    <div id="toolbar">Size: {{ width }} x {{ height }}</div>
    <div class="container">
      <vue-drag-resize-rotate :parent="true" :max-width="300" :max-height="300" @resizing="onResizing">
        <p>
          Component costrained in parent with
          <b>maxWidth</b> and
          <b>maxHeight</b> equal to 300.
        </p>
      </vue-drag-resize-rotate>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      width: 200,
      height: 200
    }
  },
  methods: {
    onResizing(x, y, width, height) {
      this.width = width
      this.height = height
    }
  }
}
</script>

<style>
</style>