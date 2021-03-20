<template>
  <view>
    <view class="video_category_list">
      <view class="video_category_item">
        <view class="video_img">
          <image mode="widthFix" src=""></image>
        </view>
        <view class="video_title"></view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data () {
    cateList: []
  },
  mounted () {
    this.getList()
  },
  methods: {
    async getList () {
      const res = await this.request({
        url: this.urlObj.url
      })
      console.log(res.res)
    }
  },
  props: {
    urlObj: Object
  }
}
</script>

<style lang="scss" scoped>
.video_category_list {
  display: flex;
  flex-wrap: wrap;
  .video_category_item {
    position: relative;
    width: 50%;
    border: 5rpx solid #fff;
    .video_title {
      position: absolute;
      left: 10%;
      bottom: 8%;
      width: 100%;
      height: 40rpx;
      font-size: 40rpx;
      color: #fff;
      background-image: linear-gradient(to right top, rgba(0,0,0,.2), rgba(0, 0, 0, 0));
    }
  }
}
</style>