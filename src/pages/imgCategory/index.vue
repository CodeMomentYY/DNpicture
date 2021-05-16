<template>
  <view>
    <view class="category_tab">
      <view class="category_tab_title">
        <view class="title_inner">
          <uni-segmented-control
            :current="current"
            :values="itemsMap"
            @clickItem="onClickItem"
            styleType="text"
            activeColor="#52d4dd"
          ></uni-segmented-control>
        </view>
        <view class="iconfont icon-ziyuan"></view>
      </view>
      <scroll-view
        @scrolltolower="handleScrollToLower"
        scroll-y
        enable-flex
        class="category_tab_content"
      >
        <view class="cate_item" v-for="item in vertical" :key="item.id">
          <go-detail :list="vertical" :index="index">
            <image mode="widthFix" :src="item.thumb" />
          </go-detail>
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script>
import { uniSegmentedControl } from '@dcloudio/uni-ui'

import goDetail from '@/components/goDetail'

export default {
  components: {
    uniSegmentedControl,
    goDetail
  },
  data() {
    return {
      items: [
        { title: '最新', order: 'new' },
        { title: '热门', order: 'hot' }
      ],
      current: 0,
      params: {
        limit: 30,
        skip: 0,
        order: 'new'
      },
      id: 0,
      // 页面显示数据数组
      vertical: [],
      hasMore: true
    }
  },
  onLoad(options) {
    this.id = options.id
    this.getList()
  },
  computed: {
    itemsMap() {
      return this.items.map(i => i.title)
    }
  },
  methods: {
    onClickItem(e) {
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex
      } else {
        // 点击的是相同的标题
        return
      }
      this.params.order = this.items[e.currentIndex].order
      this.params.skip = 0
      this.vertical = []
      this.getList()
    },
    getList() {
      this.request({
        url: `http://157.122.54.189:9088/image/v1/vertical/category/${this.id}/vertical`,
        data: this.params
      }).then(res => {
        if (res.res.vertical.length === 0) {
          this.hasMore = false
          uni.showToast({
            title: '已经拉到底了哦',
            icon: 'none'
          })
          return
        }

        this.vertical = [...this.vertical, ...res.res.vertical]
      })
    },
    handleScrollToLower() {
      if (this.hasMore) {
        this.params.skip += this.params.limit
        this.getList()
      } else {
        uni.showToast({
          title: '已经拉到底了哦',
          icon: 'none'
        })
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.category_tab {
  .category_tab_title {
    position: relative;

    .title_inner {
      margin: 0 auto;
      width: 60%;
    }

    .icon-ziyuan {
      position: absolute;
      top: 50%;
      right: 5%;
      transform: translateY(-50%);
    }
  }

  .category_tab_content {
    display: flex;
    flex-wrap: wrap;
    height: calc(100vh - 36px);

    .cate_item {
      width: 33.33%;
      border: 5rpx solid #fff;
    }
  }
}
</style>