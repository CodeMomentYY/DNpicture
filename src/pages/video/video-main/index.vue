<template>
  <scroll-view
    @scrolltolower="handleScrollToLower"
    scroll-y
    enable-flex
    class="video_main"
  >
    <view class="video_item" v-for="item in videowp" :key="item.id">
      <image mode="widthFix" :src="item.img" @click="handleGoVideo(item)" />
    </view>
  </scroll-view>
</template>

<script>
export default {
  props: {
    urlobj: Object
  },
  data() {
    return {
      videowp: [],
      hasMore: true
    }
  },
  mounted() {
    this.getList()
  },
  watch: {
    urlobj() {
      this.videowp = []
      this.getList()
    }
  },
  methods: {
    getList() {
      this.request({
        url: this.urlobj.url,
        data: this.urlobj.params
      }).then(res => {
        if (res.res.videowp === 0) {
          this.hasMore = false
          uni.showToast({
            title: '已经拉到底了哦',
            icon: 'none'
          })
          return
        }

        this.videowp = [...this.videowp, ...res.res.videowp]
      })
    },
    handleScrollToLower() {
      if (this.hasMore) {
        this.urlobj.params.skip += this.urlobj.params.limit
        this.getList()
      } else {
        uni.showToast({
          title: '已经拉到底了哦',
          icon: 'none'
        })
      }
    },
    handleGoVideo(item) {
      // 1.将数据存到全局共享中
      getApp().globalData.video = item
      // 2.页面跳转
      uni.navigateTo({
        url: '/pages/videoPlay/index'
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.video_main {
  display: flex;
  flex-wrap: wrap;
  height: calc(100vh - 36px);

  .video_item {
    width: 33.33%;
    border: 5rpx solid #fff;
  }
}
</style>