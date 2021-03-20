<template>
<!-- @touchstart：手指按下屏幕事件  @touchend：手指离开屏幕事件 -->
  <view @touchstart="handleTouchStart" @touchend="handleTouchEnd">
    <slot></slot>
  </view>
</template>

<script>
export default {
  data () {
    return {
      startTime: 0,
      startX: 0,
      startY: 0
    }
  },
  methods: {
    handleTouchStart (e) {
      this.startTime = Date.now()
      // 手指在屏幕上的坐标
      this.startX = e.changedTouches[0].clientX
      this.startY = e.changedTouches[0].clientY
    },
    handleTouchEnd (e) {
      const endTime = Date.now()
      const endX = e.changedTouches[0].clientX
      const endY = e.changedTouches[0].clientY
      let direction = ''
      if (endTime - this.startTime > 2000) return
      if (Math.abs(endX - this.startX) > 10) {
        direction = endX - this.startX > 0 ? 'right' : 'left'
        this.$emit('handleSwiperAction', {direction})
      } else {
        return
      }
    }
  }
}
</script>

<style>

</style>