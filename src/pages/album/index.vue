<template>
  <view>
    <!-- 专辑背景 -->
    <view class="album_bg">
      <image mode="widthFix" :src="album.cover" />
      <view class="album_info">
        <view class="album_name">{{ album.name }}</view>
        <view class="album_btn">关注专辑</view>
      </view>
    </view>

    <!-- 专辑作者 -->
    <view class="album_author">
      <view class="album_author_info">
        <image mode="widthFix" :src="album.user.avatar" />
        <view class="author_name">{{ album.user.name }}</view>
      </view>
      <view class="album_author_desc">
        <text>{{ album.desc }}</text>
      </view>
    </view>

    <!-- 列表 -->
    <view class="album_list">
      <view class="album_item" v-for="(item, index) in wallpaper" :key="item.id">
        <go-detail :list="wallpaper" :index="index">
          <image
            mode="aspepectFill"
            :src="item.thumb + item.rule.replace('$<Height>', 360)"
          />
        </go-detail>
      </view>
    </view>
  </view>
</template>

<script>
import goDetail from '@/components/goDetail'

export default {
  components: {
    goDetail
  },
  data() {
    return {
      params: {
        limit: 30,
        order: 'new',
        skip: 0,
        // first为1有album对象（第一次请求），为0只有wallpaper（不是第一次请求）
        first: 1
      },
      id: -1,
      album: {},
      wallpaper: [],
      hasMore: true
    }
  },
  onLoad(options) {
    this.id = options.id
    // this.id = "5d5f8e45e7bce75ae7fb8278"
    this.getList()
  },
  // 页面触底
  onReachBottom() {
    if (this.hasMore) {
      this.params.first = 0
      this.params.skip += this.params.limit
      this.getList()
    } else {
      uni.showToast({
        title: '已经拉到底了哦',
        icon: 'none'
      })
    }
  },
  methods: {
    getList() {
      this.request({
        url: `http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
        data: this.params
      }).then(res => {
        if (Object.keys(this.album).length === 0) {
          this.album = res.res.album
        }

        if (res.res.wallpaper.length === 0) {
          this.hasMore = false
          uni.showToast({
            title: '已经拉到底了哦',
            icon: 'none'
          })
          return
        }

        this.wallpaper = [...this.wallpaper, ...res.res.wallpaper]
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.album_bg {
  position: relative;

  .album_info {
    position: absolute;
    left: 0;
    bottom: 0;
    padding: 0 15rpx;
    width: 100%;
    height: 80rpx;
    display: flex;
    justify-content: space-between;
    align-items: center;
    color: #fff;

    .album_name {
      font-size: 40rpx;
    }

    .album_btn {
      width: 152rpx;
      height: 60rpx;
      background-color: $color;
      display: flex;
      justify-content: center;
      align-items: center;
      border-radius: 10rpx;
    }
  }
}

.album_author {
  padding: 10rpx;

  .album_author_info {
    padding: 10rpx 0;
    display: flex;

    image {
      width: 50rpx;
    }

    .author_name {
      margin-left: 15rpx;
      color: #000;
    }
  }
}

.album_list {
  display: flex;
  flex-wrap: wrap;

  .album_item {
    width: 33.33%;
    border: 3rpx solid #fff;

    image {
      height: 160rpx;
    }
  }
}
</style>