<template>
  <scroll-view
    class="album_scroll_view"
    scroll-y
    @scrolltolower="handleToLower"
  >
    <!-- 轮播图 -->
    <view class="album_swiper">
      <swiper autoplay indicator-dots circular>
        <swiper-item v-for="item in banner" :key="item.id">
          <image :src="item.thumb" />
        </swiper-item>
      </swiper>
    </view>

    <!-- 列表 -->
    <view class="album_list">
      <navigator
        :url="`/pages/album/index?id=${item.id}`"
        class="album_item"
        v-for="item in album"
        :key="item.id"
      >
        <view class="album_img">
          <image mode="aspectFill" :src="item.cover" />
        </view>
        <view class="album_info">
          <view class="album_name">{{ item.name }}</view>
          <view class="album_desc">{{ item.desc }}</view>
          <view class="album_btn">
            <view class="album_attention">+关注</view>
          </view>
        </view>
      </navigator>
    </view>
  </scroll-view>
</template>

<script>
export default {
  data() {
    return {
      params: {
        limit: 30,
        order: 'new',
        skip: 0
      },
      // 轮播图数组
      banner: [],
      // 列表数组
      album: [],
      hasMore: true
    }
  },
  mounted() {
    // 修改页面标题
    uni.setNavigationBarTitle({ title: '专辑' })
    this.getList()
  },
  methods: {
    getList() {
      this.request({
        url: 'http://157.122.54.189:9088/image/v1/wallpaper/album',
        data: this.params
      }).then(res => {
        if (this.banner.length === 0) {
          this.banner = res.res.banner
        }

        if (res.res.album.length === 0) {
          this.hasMore = false
          uni.showToast({
            title: '已经拉到底了哦',
            icon: 'none'
          })
          return
        }

        this.album = [...this.album, ...res.res.album]
      })
    },
    handleToLower() {
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
.album_scroll_view {
  height: calc(100vh - 36px);
}

.album_swiper {
  swiper {
    height: calc(750rpx / 2.3);

    image {
      height: 100%;
    }
  }
}

.album_list {
  padding: 20rpx;

  .album_item {
    padding: 10rpx;
    display: flex;
    border-bottom: 1rpx solid #eee;

    .album_img {
      flex: 1;
      padding: 10rpx;

      image {
        width: 200rpx;
        height: 200rpx;
      }
    }

    .album_info {
      flex: 2;
      padding: 0 10rpx;
      overflow: hidden;

      .album_name {
        padding: 10rpx 0;
        font-size: 30rpx;
        color: #000;
      }

      .album_desc {
        padding: 10rpx 0;
        font-size: 24rpx;
        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
      }

      .album_btn {
        padding: 10rpx;
        display: flex;
        justify-content: flex-end;

        .album_attention {
          padding: 10rpx;
          color: $color;
          border: 1rpx solid $color;
          border-radius: 10rpx;
        }
      }
    }
  }
}
</style>