<template>
  <view>
    <view class="user">
      <view class="user_info">
        <view class="user_thumb">
            <image mode="widthFix" :src="users.user.avatar"></image>
        </view>
        <view class="user_title">
          <view class="user_author">{{ users.user.name }}</view>
          <view class="user_time">{{ users.userTime }}</view>
        </view>
      </view>
    </view>
    <view class="middle_img">
      <swiper-action @handleSwiperAction="handleSwiperAction">
        <image mode="widthFix" :src="users.img"></image>
      </swiper-action>
    </view>
    <view class="user_fabulous">
      <view class="fabulous_left">
        <view class="iconfont icondianzan"></view>
        <text>{{users.rank}}</text>
      </view>
      <view class="fabulous_right">
        <view class="iconfont iconhot"></view>
        <text>收藏</text>
      </view>
    </view>
    <!-- 专辑 start -->
    <view class="album">
      <view class="album_relevant">相关</view>
      <view class="album_info">
        <view class="album_img">
          <image mode="aspectFill" :src="users.user.avatar"></image>
        </view>
        <view class="album_title">
          <view class="album_btn">专辑</view>
          <view class="album_text">小熊猫</view>
          <view class="iconfont iconiconfontjiantou4"></view>
        </view>
      </view>
    </view>
    <!-- 专辑 end -->
    <!-- 最热评论 start -->
    <view class="hot" v-if="hotList.length > 0">
      <view class="hot_title">
        <view class="iconfont iconhot1"></view>
        <view class="hot_title_text">最热评论</view>
      </view>
      <view class="hot_list">
        <view class="list_item" v-for="item in hotList" :key="item.id">
          <view class="hot_img">
            <image mode="aspectFill" :src="item.user.avatar"></image>
          </view>
          <view class="hot_user">
            <view class="hot_user_title">
              <text>{{ item.user.name }}</text>
              <view v-for="item1 in item.user.title" :key="item1.icon">
                <image mode="widthFix" :src="item1.icon"></image>
              </view>
            </view>
            <view class="hot_user_time">{{ item.time }}</view>
            <view class="hot_user_desc">
              <text>{{ item.content }}</text>
              <view>
                <view class="iconfont icondianzan"></view>
                <text>{{ item.size }}</text>
              </view>
            </view>
          </view>
        </view>
      </view>
    </view>
    <!-- 最热评论 end -->
    <!-- 最新评论 start -->
    <view class="hot" v-if="commentList.length > 0">
      <view class="hot_title">
        <view class="iconfont iconpinglun"></view>
        <view class="hot_title_text">最新评论</view>
      </view>
      <view class="hot_list">
        <view class="list_item" v-for="item in commentList" :key="item.id">
          <view class="hot_img">
            <image mode="aspectFill" :src="item.user.avatar"></image>
          </view>
          <view class="hot_user">
            <view class="hot_user_title">
              <text>{{ item.user.name }}</text>
              <view v-for="item1 in item.user.title" :key="item1.icon">
                <image mode="widthFix" :src="item1.icon"></image>
              </view>
            </view>
            <view class="hot_user_time">{{ item.time }}</view>
            <view class="hot_user_desc">
              <text>{{ item.content }}</text>
              <view>
                <view class="iconfont icondianzan"></view>
                <text>{{ item.size }}</text>
              </view>
            </view>
          </view>
        </view>
      </view>
    </view>
    <!-- 最新评论 end -->
    <!-- 下载图片 start -->
    <view class="download">
      <view class="download_btn" @click="handleDownload">下载图片</view>
    </view>
    <!-- 下载图片 end -->
  </view>
</template>

<script>

import moment from 'moment'
import swiperAction from 'pages/components/swiperAction'
moment.locale('zh-cn')

export default {
  data () {
    return {
      users: {},
      hotList: [],
      commentList: [],
      imgIndex: 0
    }
  },
  onLoad () {
    const { imgIndex } = getApp().globalData
    this.imgIndex = imgIndex
    this.getList()
  },
  methods: {
    async getList () {
      const { imgList } = getApp().globalData
      this.users = imgList[this.imgIndex]
      this.users.userTime = moment(this.users.atime * 1000).fromNow()
      const res = await this.request({
      url: `http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${this.users.id}/comment`
      })
      console.log(res)
      this.hotList = res.res.hot
      this.hotList.forEach(item => item.time = moment(item.atime * 1000).fromNow())
      this.commentList = res.res.comment
      this.hotList.forEach(item => item.time = moment(item.atime * 1000).fromNow())
    },
    handleSwiperAction (direction) {
      const aspect = direction.direction
      const { imgList } = getApp().globalData
      if (aspect === 'left' && this.imgIndex < imgList.length -1 ) {
        this.imgIndex++
        this.getList()
      } else if (aspect === 'right' && this.imgIndex > 0) {
        this.imgIndex --
        this.getList()
      } else {
        uni.showToast({
          title: '没有数据了',
          icon: 'none'
        })
      }
    },
    async handleDownload () {
      uni.showLoading({
        title: '下载中'
      })
      // 下载远程文件到小程序的内存中
      const res = await uni.downloadFile({url: this.users.img})
      const { tempFilePath } = res[1]
      // 将图片从内存中下载到本地
      const result = await uni.saveImageToPhotosAlbum({ filePath: tempFilePath })
      uni.hideLoading()
      uni.showToast({
        title: '下载成功',
        icon: 'none'
      })
    }
  },
  components: {
    swiperAction
  }
}
</script>

<style lang="scss" scoped>
.user {
  padding: 10rpx;
  .user_info {
    display: flex;
    align-items: center;
    .user_thumb {
      padding: 20rpx;
        image {
          width: 88rpx;
          border-radius: 50%;
        }
    }
    .user_title {
      .user_author {
        color: #222;
      }
      .user_time {
        color: #ccc;
        font-size: 20rpx;
      }
    }
  }
}
.user_fabulous {
  display: flex;
  align-items: center;
  justify-content: space-around;
  padding: 20rpx 0;
  border-bottom: 6rpx solid #eee;
  .fabulous_left {
    display: flex;
    view {
      padding-right: 6rpx;
    }
  }
  .fabulous_right {
    display: flex;
    view {
      padding-right: 6rpx;
    }
  }
}
.album {
  border-bottom: 10rpx solid #eee;
  .album_relevant {
    padding: 20rpx 0 10rpx 0;
  }
  .album_info {
    position: relative;
    display: flex;
    padding: 10rpx;
    .album_img {
      padding-right: 20rpx;
      image {
        width: 200rpx;
        height: 200rpx;
      }
    }
    .album_title {
      .album_btn {
        padding: 20rpx;
        background-color: $color;
        color: #fff;
      }
      .album_text {
        color: #000;
      }
      .iconiconfontjiantou4 {
        position: absolute;
        top: 50%;
        right: 5%;
        transform: translateY(-50%);
      }
    }
  }
}
.hot {
  padding: 13rpx 10rpx 0 10rpx;
  .hot_title {
    display: flex;
    .iconhot1 {
      padding: 0 20rpx 0 10rpx;
    }
  }
  .hot_list {
    .list_item {
      display: flex;
      padding-top: 18rpx;
      border-bottom: 10rpx solid #eee;
      .hot_img {
        flex: 1;
        image {
          width: 80rpx;
          height: 80rpx;
        }
      }
      .hot_user {
        padding-left: 18rpx;
        flex: 9;
        font-size: 20rpx;
        .hot_user_title {
          display: flex;
          padding: 6rpx 0;
          color: #ccc;
          view {
            image {
              width: 36rpx;
              height: 36rpx;
            }
          }
        }
        .hot_user_time {
          color: #666;
        }
        .hot_user_desc {
          display: flex;
          justify-content: space-between;
          padding: 20rpx 0;
          color: #000;
          view {
            display: flex;
            align-items: center;
            view {
              padding-right: 6rpx;
            }
            text {
              font-size: 30rpx;
            }
          }
        }
      }
    }
  }
}
.hot_img {
  image {
    width: 80rpx;
    height: 80rpx;
  }
}
.download {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 120rpx;
  .download_btn {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 90%;
    height: 80%;
    background-color: $color;
    font-size: 50rpx;
    font-weight: 600;
    color: #fff;
  }
}
</style>