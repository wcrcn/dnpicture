<template>
  <scroll-view class="album_view" scroll-y @scrolltolower="handleScrolltolower">
    <!-- 轮播图 start -->
    <view class="album_swiper">
      <swiper
      autoplay
      indicator-dots
      circular
      >
        <swiper-item class="album_swiper_item" v-for="item in bannerList" :key="item.id">
          <image mode="widthFix" :src="item.thumb"></image>
        </swiper-item>
      </swiper>
    </view>
    <!-- 轮播图 end -->
    <!-- 专辑 start -->
    <view class="album_list">
      <navigator :url="`/pages/home/album/index?id=${item.id}`" class="album_list_item" v-for="item in albumList" :key="item.id">
        <view class="album_img">
          <image mode="widthFix" :src="item.cover"></image>
        </view>
        <view class="album_info">
          <view class="album_title">{{ item.name }}</view>
          <view class="album_desc">{{ item.desc }}</view>
          <view class="album_btn">
            <text>+ 关注</text>
          </view>
        </view>
      </navigator>
    </view>
    <!-- 专辑 end -->
  </scroll-view>
</template>

<script>
export default {
  data () {
    return {
      bannerList: [],
      albumList: [],
      params: {
        limit: 30,
        order: 'new',
        skip: 0
      },
      hasMore: true
    }
  },
  mounted () {
    uni.setNavigationBarTitle({title: '专辑'})
    this.getList()
  },
  methods: {
    async getList () {
      const res = await this.request({
        url: 'http://157.122.54.189:9088/image/v1/wallpaper/album',
        data: this.params
      })
      if (this.bannerList.length === 0) {
        this.bannerList = res.res.banner
      }
      if (res.res.album.length === 0) {
        uni.showToast({
          title: '没有数据了',
          icon: 'none'
        })
        this.hasMore = false;
        return
      }
      this.albumList = [...this.albumList, ...res.res.album]
    },
    handleScrolltolower () {
      if (this.hasMore) {
        this.params.skip += this.params.limit
        this.getList()
      } else {
        uni.showToast({
          title: '没有数据了',
          icon: 'none'
        })
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.album_view {
  height: calc( 100vh - 36px );
}
.album_swiper {
  .album_swiper_item {
    height: calc(326.1);
    image {
      width: 100%;
    }
  }
}
.album_list {
  padding: 10rpx;
  .album_list_item {
    display: flex;
    .album_img {
      flex: 1;
      padding: 10rpx;
      width: 200rpx;
      height: 200rpx;
      image {
        width: 100%;
        height: 100%;
      }
    }
    .album_info {
      flex: 2;
      padding-left: 20rpx;
      overflow: hidden;
      .album_title {
        padding: 10rpx 20rpx;
        font-size: 30rpx;
        font-weight: 600;
      }
      .album_desc {
        padding: 10rpx 15rpx;
        white-space: nowrap;
        text-overflow: ellipsis;
        overflow: hidden;
      }
      .album_btn {
        display: flex;
        justify-content: flex-end;
        padding: 10rpx;
        text {
          padding: 10rpx;
          border: 1px solid $color;
          color: $color;
          border-radius: 6rpx;
        }
      }
    }
  }
}
</style>