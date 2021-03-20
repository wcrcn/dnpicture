<template>
  <view>
    <view class="category_detail_tab">
      <view class="category_detail_tab_title">
        <view class="title_inner">
          <uni-segmented-control
            :current="current"
            :values="items.map(item => item.title)"
            @clickItem="onClickItem"
            style-type="text"
            active-color="#d4237a"></uni-segmented-control>
        </view>
        <view class="iconfont iconsearch"></view>
      </view>
      <view class="content">
        <scroll-view scroll-y enable-flex class="img_list" @scrolltolower="handleCateList">
          <view class="list_item" v-for="item in categoryList" :key="item.id">
            <image :src="item.thumb" mode="aspectFill"></image>
          </view>
        </scroll-view>
      </view>
    </view>
  </view>
</template>

<script>


import { uniSegmentedControl } from '@dcloudio/uni-ui'

export default {
  data () {
    return {
            // items: ['推荐', '分类', '最新', '专辑'],
            items: [
              {
                title: '最新',
                order: 'new'
              },
              {
                title: '热门',
                order: 'hot'
              }
            ],
            current: 0,
            id: 0,
            params: {limit: 30, skip: 0, order: 'new'},
            categoryList: [],
            hasMore: true
        }
  },
  onLoad (option) {
    this.id = option.id
    this.getList()
  },
  methods: {
    onClickItem(e) {
      if (this.current !== e.currentIndex) {
          this.current = e.currentIndex;
      }
      this.params.order = this.items[e.currentIndex].order
      this.params.skip = 0
      this.categoryList = []
      this.getList()
    },
    async getList () {
      const res = await this.request({
        url: `http://157.122.54.189:9088/image/v1/vertical/category/${this.id}/vertical`,
        data: this.params
      })
      if (res.res.vertical.length === 0) {
        this.hasMore = false
        uni.showToast({
          title: '没有数据了',
          icon: 'none'
        })
        return
      }
      this.categoryList = [...this.categoryList, ...res.res.vertical]
    },
    handleCateList () {
      console.log(111)
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
  },
  components: {
    uniSegmentedControl
  }
}
</script>

<style lang="scss" scoped>
.category_detail_tab {
  .category_detail_tab_title {
    position: relative;
    .title_inner {
      width: 60%;
      margin: 0 auto;
    }
    .iconsearch {
      position: absolute;
      top: 50%;
      right: 5%;
      transform: translate(0, -50%);
    }
  }
}
.img_list {
  display: flex;
  flex-wrap: wrap;
  height: calc(100vh - 36px);
  padding: 0 5rpx;
  .list_item {
    width: 33.333%;
    border: 5rpx solid #fff;
    image {
      height: 240rpx;
    }
  }
}
</style>