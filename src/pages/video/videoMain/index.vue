<template>
  <view>
    <scroll-view scroll-y enable-flex @scrolltolower="handleScrolltolower" class="video_list">
      <view class="video_list_item" v-for="item in videoList" :key="item.id" @click="handleVideo(item)">
        <image mode="widthFix" :src="item.img"></image>
      </view>
    </scroll-view>
  </view>
</template>

<script>
export default {
  data () {
    return {
      videoList: [],
      hasMore: true
    }
  },
  watch: {
    urlObj () {
      this.videoList = []
      this.getList()
    }
  },
  methods: {
    async getList () {
      const res = await this.request({
        url: this.urlObj.url,
        data: this.urlObj.params
      })
      if (res.res.videowp.length === 0) {
        this.hasMore = false
        uni.showToast({
          title: '没有数据了',
          icon: 'none'
        })
        return
      }
      this.videoList = [...this.videoList, ...res.res.videowp]
    },
    handleScrolltolower () {
      if (this.hasMore) {
        this.urlObj.params.skip += this.urlObj.params.limit
        this.getList()
      } else {
        uni.showToast({
          title: '没有数据了',
          icon: 'none'
        })
      }
    },
    handleVideo (item) {
      getApp().globalData.video = item
      uni.navigateTo({
        url: '/pages/video/videoDetail/index'
      })
    }
  },
  mounted () {
    this.getList()
  },
  props: {
    urlObj: Object
  }
}
</script>

<style lang="scss" scoped>
.video_list {
  display: flex;
  flex-wrap: wrap;
  height: calc(100vh - 36px);
  padding: 0 5rpx;
  .video_list_item {
    width: 33.333%;
    border: 5rpx solid #fff;
  }
}
</style>