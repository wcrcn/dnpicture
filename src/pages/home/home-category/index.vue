<template>
  <view>
    <view class="home_category_tab">
      <view class="category_item" v-for="item in categoryList" :key="item.id" @click="handleCategoryDetail(item.id)">
        <view class="category_thumb">
          <image mode="aspectFill" :src="item.cover"></image>
        </view>
        <view class="category_name">{{ item.name }}</view>
      </view>
    </view>
  </view>
</template>

<script>

import { uniSegmentedControl } from '@dcloudio/uni-ui'

export default {
  data () {
    return {
      categoryList: []
    }
  },
  mounted () {
    this.getCategoryList()
  },
  methods: {
    async getCategoryList () {
      const res = await this.request({
        url: 'http://157.122.54.189:9088/image/v1/vertical/category'
      })
      this.categoryList = res.res.category
      console.log(this.categoryList)
    },
    handleCategoryDetail (id) {
      uni.navigateTo({
        url: `/pages/home/categoryDetail/index?id=${id}`
      })
    }
  },
  comments: {
    uniSegmentedControl
  }
}
</script>

<style lang="scss" scoped>
.home_category_tab {
  display: flex;
  flex-wrap: wrap;
  padding: 0 5rpx;
  .category_item {
    position: relative;
    width: 33.3333%;
    border: 5rpx solid #fff;
    .category_thumb {
      image {
        height: 240rpx;
      }
    }
    .category_name {
      position: absolute;
      left: 8%;
      bottom: 6%;
      width: 100%;
      height: 40rpx;
      font-size: 40rpx;
      color: #fff;
      background-image: linear-gradient(to right top, rgba(0,0,0,.2), rgba(0, 0, 0, 0));
    }
  }
}
</style>