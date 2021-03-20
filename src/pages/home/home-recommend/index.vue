<template>
  <scroll-view class="recommend_view" v-if="recommendList.length > 0" scroll-y @scrolltolower="handleScrolltolower">
    <!-- 推荐模块 start -->
    <view class="recommend_wrap">
      
      <navigator class="recommend_wrap_item" v-for="item in recommendList" :key="item.id" :url="`/pages/home/album/index?id=${item.target}`">
        <!-- 图片的适宜 -->
        <image mode="widthFix" :src="item.thumb"></image>
      </navigator>
    </view>
    <!-- 推荐模块 end  -->
    <!-- 月份模块 start -->
    <view class="month_wrap">
      <view class="month_wrap_title">
        <view class="month_info">
          <view class="month">
            <text>{{ months.DD }} </text>
          / {{ months.MM }} 月
          </view>
          <viww class="month_title">{{ months.title }}</viww>
        </view>
        <view class="month_more">更多 ></view>
      </view>
      <view class="month_wrap_content">
        <navigator class="month_img" v-for="item in months.items" :key="item.id" :url="`/pages/home/album/index?id=${item.target}`">
          <image mode="aspectFill" :src="item.thumb + item.rule.replace('$<Height>', 360)"></image>
        </navigator>
      </view>
    </view>
    <!-- 月份模块 end -->
    <!-- 热门模块 start -->
    <view class="hot_wrap">
      <view class="hot_wrap_title">
        <view class="hot_title_info">
          <text>热门</text>
        </view>
      </view>
      <view class="hot_wrap_content">
        <view class="hot_img" v-for="item in hotList" :key="item.id">
          <image mode="widthFix" :src="item.thumb"></image>
        </view>
      </view>
    </view>
    <!-- 热门模块 end -->
  </scroll-view>
</template>

<script>

import moment from 'moment'

export default {
  data () {
    return {
      // 推荐
      recommendList: [],
      // 月份
      months: {},
      // 热门
      hotList: [],
      params: {
        limit: 30,
        order: 'hot',
        skip: 0
      },
      // 判断 热门是否有数据
      hasmore: true
    }
  },
  mounted () {
    this.getList()
  },
  methods: {
    async getList () {
      const res = await this.request({
            url: 'http://157.122.54.189:9088/image/v3/homepage/vertical',
            // url: 'http://service.picasso.adesk.com/v3/homepage/vertical',
            data: this.params
          })
      if (res.res.vertical.length === 0) {
        uni.showToast({
          title: '没有数据了',
          icon: 'none'
        })
        this.hasmore = false
        return
      }
      if (this.recommendList.length === 0) {
        this.recommendList = res.res.homepage[1].items
        this.months = res.res.homepage[2]
        this.months.MM = moment(this.months.stime).format('MM')
        this.months.DD = moment(this.months.stime).format('DD')
      }
      this.hotList = [...this.hotList, ...res.res.vertical]
    },
    handleScrolltolower () {
      if (this.hasmore) {
        console.log(1112)
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
.recommend_view {
  // height: 屏幕的高度 - 头部标题的高度
  height: calc( 100vh - 36px );
}
.recommend_wrap {
  display: flex;
  flex-wrap: wrap;
  .recommend_wrap_item {
    width: 50%;
    border: 3rpx solid #fff;
  }
}
.month_wrap {
  .month_wrap_title {
    display: flex;
    justify-content: space-between;
    padding: 20rpx;
    color: $color;
    .month_info {
      display: flex;
      align-items: flex-end;
      .month {
        text {
          font-size: 38rpx;
        }
      }
      .month_title {
        margin-left: 30rpx;
        font-size: 35rpx;
        color: #666;
      }
    }
  }
  .month_wrap_content {
    display: flex;
    flex-wrap: wrap;
    .month_img {
      width: 33.3333%;
      border: 3rpx solid #fff;
    }
  }
}
.hot_wrap {
    .hot_wrap_title {
      padding: 20rpx;
      .hot_title_info {
        border-left: 10rpx solid $color;
        text {
          margin-left: 10rpx;
        }
      }
    }
    .hot_wrap_content {
    display: flex;
    flex-wrap: wrap;
    .hot_img {
      width: 33.3333%;
      border: 3rpx solid #fff;
    }
  }
}
</style>