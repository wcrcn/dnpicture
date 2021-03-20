<template>
  <view @click="handleImgDetail">
    <slot></slot>
  </view>
</template>

<script>
export default {
  props: {
    list: Array,
    index: Number
  },
  methods: {
    handleImgDetail () {
      console.log(this.list)
      getApp().globalData.imgList = this.list
      getApp().globalData.imgIndex = this.index
      uni.navigateTo({
        url: '/pages/img-detail/index'
      })
    }
  }
}
</script>

<style>

</style>