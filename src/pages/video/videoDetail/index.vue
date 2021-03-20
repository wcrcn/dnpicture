<template>
  <view class="video">
    <image :src="video.img"></image>
    <view class="video_tool">
      <view class="iconfont iconjingyin" @click="handleMuted"></view>
      <view class="iconfont iconzhuanfa">
        <button open-type="share"></button>
      </view>
    </view>
    <view class="video_wrap">
      <video :src="video.video" :muted="muted" objectFit="fill"></video>
    </view>
    <view class="video_download">
      <view class="download_btn" @click="handleDownload">下载高清</view>
    </view>
  </view>
</template>

<script>
export default {
  data () {
    return {
      video: {},
      muted: false
    }
  },
  methods: {
    handleMuted () {
      this.muted = !this.muted
    },
    async handleDownload () {
      uni.showLoading({
        title: '下载中'
      })
      // 将远程文件 下载到小程序内存中
      const { tempFilePath} = (await uni.downloadFile({url: this.video.video}))[1]
      // 将内存中的文件 下载到本地上
      await uni.saveVideoToPhotosAlbum({filePath: tempFilePath})
      uni.hideLoading()
      uni.showToast({
        title: '下载成功',
        icon: 'none'
      })
    }
  },
  onLoad () {
    this.video = getApp().globalData.video
  }
}
</script>

<style lang="scss" scoped>
.video {
  position: relative;
  image {
    position: absolute;
    width: 100%;
    height: 100%;
    z-index: -1;
    filter: blur(20px);
  }
  .video_tool {
    position: absolute;
    top: 0;
    right: 0;
    display: flex;
    justify-content: space-around;
    height: 80rpx;
    .iconfont{
      display: flex;
      justify-content: center;
      align-items: center;
      margin-right: 20rpx;
      width: 80rpx;
      color: #fff;
      border-radius: 40rpx;
      font-size: 40rpx;
      background-color: rgba(0, 0, 0, .2);
    }
  }

  .video_wrap {
    display: flex;
    justify-content: center;
    padding-top: 80rpx;
    video {
      width: 50%;
      height: 600rpx;
    }
  }

  .video_download {
    margin-top: 20rpx;
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 80rpx;
    .download_btn {
      display: flex;
      justify-content: center;
      align-items: center;
      width: 50%;
      padding: 20rpx;
      color: #fff;
      font-size: 25rpx;
      border-radius: 40rpx;
      background-color: rgba(0, 0, 0, .4);
    }
  }
}
</style>