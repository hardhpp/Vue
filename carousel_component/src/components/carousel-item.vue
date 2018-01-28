<template>
  <div>
    <slot></slot>
  </div>
</template>

<script>
export default{
  name: 'carousel-item',
  mounted () {
    this.$parent && this.$parent.carouselItemCreatedOrDestroyed(this)
  },
  destroyed () {
    this.$parent && this.$parent.carouselItemCreatedOrDestroyed(this)
  }
}
</script>
