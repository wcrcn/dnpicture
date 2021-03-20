<template>
  <scroll-view scroll-y>
    <!-- 专辑详情背景 start -->
    <view class="album_bg">
      <image mode="widthFix" :src="albums.cover"></image>
      <view class="album_bg_content">
        <view class="album_bg_title">{{ albums.name }}</view>
        <view class="album_bg_btn">
          <text>关注专辑</text>
        </view>
      </view>
    </view>
    <!-- 专辑详情背景 end -->
    <!-- 专辑详情内容 start -->
    <view class="album_content">
      <view class="album_content_title">
        <view class="album_content_img">
          <image mode="widthFix" :src="albums.user.avatar"></image>
        </view>
        <text class="album_content_title_text">{{ albums.user.name }}</text>
      </view>
      <view class="album_content_desc">
        <text>{{ albums.desc }}</text>
      </view>
    </view>
    <!-- 专辑详情内容 end -->
    <view class="album_img_list">
      <view class="album_img_list_item" v-for="(item, index) in wallpaperList" :key="item.id">
        <go-detail :list="wallpaperList" :index="index">
          <image mode="widthFix" :src="item.thumb + item.rule.replace('$<Height>', 360)"></image>
        </go-detail>
      </view>
    </view>
  </scroll-view>
</template>

<script>

import goDetail from '../../components/goDetail'

export default {
  data () {
    return {
      id: '',
      params: {
        limit: 30,
        order: 'new',
        skip: 0,
        first: 1
      },
      albums: {},
      wallpaperList: [],
      hasMore: true
    }
  },
  onLoad (option) {
    this.id = option.id
    this.getList()
  },
  methods: {
    async getList () {
      const res = await this.request({
        url: `http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
        data: this.params
      })
    // this.params.first = 1
    this.albums = res.res.album
    if (res.res.wallpaper.length === 0) {
      uni.showToast({
        title: '没有数据了',
        icon: 'none'
      })
      this.hasMore = false
      return
    }
    this.wallpaperList = [...this.wallpaperList, ...res.res.wallpaper]
    },
  },
  onReachBottom () {
    if (this.hasMore) {
      this.params.skip = this.params.limit
      this.getList()
    }
  },
  components: {
    goDetail
  }
}
</script>

<style lang="scss" scoped>
.album_view {
  height: calc( 100vh - 36px );
}
.album_bg {
  position: relative;
  .album_bg_content {
    position: absolute;
    bottom: 10rpx;
    left: 0;
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 20rpx;
    color: #fff;
    .album_bg_title {
      padding-left: 20rpx;
    }
    .album_bg_btn {
      padding: 20rpx;
      background-color: $color;
      border-radius: 10rpx;
    }
  }
}
.album_content {
  padding: 10rpx;
  .album_content_title {
    display: flex;
    .album_content_img {
      width: 60rpx;
      height: 60rpx;
      padding-right: 10rpx;
    }
    .album_content_title_text {
      font-weight: 600;
    }
  }
}
.album_img_list {
  display: flex;
  flex-wrap: wrap;
  .album_img_list_item {
    width: 33.333%;
    border: 6rpx solid #fff;
    &:nth-child(3n-1) {
      border: 0;
    }
  }
}
</style>