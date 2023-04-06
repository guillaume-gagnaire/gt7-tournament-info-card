<template>
  <div class="image-container" :style="style"></div>
</template>

<script>
export default {
  props: {
    url: String,
    size: {
      type: String,
      default: 'contain'
    },
    height: String,
    width: { type: String, default: '100%' }
  },
  computed: {
    style () {
      return {
        background: `transparent url(${this.url}) no-repeat center center`,
        backgroundSize: this.size,
        height: this.height,
        width: this.width
      }
    }
  }
}
</script>
