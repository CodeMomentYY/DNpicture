<template>
  <scroll-view
    @scrolltolower="handleToLower"
    class="recommend_view"
    scroll-y
    v-if="recommends.length > 0"
  >
    <!-- 推荐 -->
    <view class="recommend_wrap">
      <navigator
        :url="`/pages/album/index?id=${item.target}`"
        class="recommend_item"
        v-for="item in recommends"
        :key="item.id"
      >
        <image mode="widthFix" :src="item.thumb" />
      </navigator>
    </view>

    <!-- 月份 -->
    <view class="month_warp">
      <view class="month_title">
        <view class="month_title_info">
          <view class="month_info">
            <text>{{ monthes.DD }}</text> / {{ monthes.MM }} 月
          </view>
          <view class="month_text">{{ monthes.title }}</view>
        </view>
        <view class="month_title_more">更多 ></view>
      </view>
      <view class="month_content">
        <view class="month_item" v-for="(item, index) in monthes.items" :key="item.id">
          <go-detail :list="monthes.items" :index="index">
            <image
              mode="aspectFill"
              :src="item.thumb + item.rule.replace('$<Height>', 360)"
            />
          </go-detail>
        </view>
      </view>
    </view>

    <!-- 热门 -->
    <view class="hots_wrap">
      <view class="hots_title">
        <text>热门</text>
      </view>
      <view class="hots_content">
        <view class="hots_item" v-for="(item, index) in hots" :key="item.id">
          <go-detail :list="hots" :index="index">
            <image mode="widthFix" :src="item.thumb" />
          </go-detail>
        </view>
      </view>
    </view>
  </scroll-view>
</template>

<script>
import moment from 'moment'
import goDetail from '@/components/goDetail'

export default {
  components: {
    goDetail
  },
  data() {
    return {
      // 推荐列表
      recommends: [],
      // 月份
      monthes: {},
      // 热门列表
      hots: [],
      // 请求的参数
      params: {
        limit: 30,
        order: 'hot',
        skip: 0
      },
      // 是否还有下一页
      hasMore: true
    }
  },
  mounted() {
    this.getList()
  },
  methods: {
    // 获取接口数据
    getList() {
      this.request({
        url: 'http://157.122.54.189:9088/image/v3/homepage/vertical',
        data: this.params
      }).then(res => {
        // 判断是否还有数据
        if (res.res.vertical.length === 0) {
          this.hasMore = false
          uni.showToast({
            title: '已经拉到底了哦',
            icon: 'none'
          })
          return
        }

        if (this.recommends.length === 0) {
          // 第一次请求
          this.recommends = res.res.homepage[1].items
          this.monthes = res.res.homepage[2]
          // 将时间戳格式化
          this.monthes.MM = moment(this.monthes.stime).format('MM')
          this.monthes.DD = moment(this.monthes.stime).format('DD')
        }

        // 数组拼接
        this.hots = [...this.hots, ...res.res.vertical]
      })
    },
    // 滚动触底事件
    handleToLower() {
      // 1.修改请求参数skip
      // 2.重新发送请求
      // 3.请求成功，hots数据叠加
      if (this.hasMore) {
        this.params.skip += this.params.limit
        this.getList()
      } else {
        // 弹窗提示
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
.recommend_view {
  // heigth=屏幕高度-头部标题高度
  height: calc(100vh - 36px);
}

.recommend_wrap {
  display: flex;
  flex-wrap: wrap;

  .recommend_item {
    width: 50%;
    border: 5rpx solid #fff;
    border-radius: 10rpx;
    overflow: hidden;
  }
}

.month_warp {
  .month_title {
    display: flex;
    justify-content: space-between;
    padding: 20rpx;

    .month_title_info {
      display: flex;
      font-size: 30rpx;
      color: $color;
      font-weight: 600;

      .month_info {
        text {
          font-size: 36rpx;
        }
      }

      .month_text {
        margin-left: 24rpx;
        font-size: 34rpx;
        color: #666;
      }
    }

    .month_title_more {
      font-size: 24rpx;
      color: $color;
    }
  }

  .month_content {
    display: flex;
    flex-wrap: wrap;

    .month_item {
      width: 33.33%;
      border: 5rpx solid #fff;
      border-radius: 15rpx;
      overflow: hidden;
    }
  }
}

.hots_wrap {
  .hots_title {
    padding: 20rpx;

    text {
      padding-left: 20rpx;
      border-left: 15rpx solid $color;
      font-size: 34rpx;
      font-weight: 600;
    }
  }

  .hots_content {
    display: flex;
    flex-wrap: wrap;

    .hots_item {
      width: 33.33%;
      border: 1rpx solid #fff;
    }
  }
}
</style>