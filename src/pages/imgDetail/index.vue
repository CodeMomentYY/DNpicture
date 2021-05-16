<template>
  <view>
    <!-- 用户信息 -->
    <view class="user_info" v-if="imgDetail.user !== undefined">
      <view class="user_icon">
        <image :src="imgDetail.user.avatar" mode="widthFix" />
      </view>
      <view class="user_desc">
        <view class="user_name">{{ imgDetail.user.name }}</view>
        <view class="user_time">{{ imgDetail.cnTime }}</view>
      </view>
    </view>

    <!-- 高清图 -->
    <view class="high_img">
      <swiper-action @swiperAction="handleSwiperAction">
        <image mode="widthFix" :src="imgDetail.thumb" />
      </swiper-action>
    </view>

    <!-- 点赞 -->
    <view class="user_rank">
      <view class="rank">
        <text class="iconfont icon-dianzan1">{{ imgDetail.rank }}</text>
      </view>
      <view class="user_collect">
        <text class="iconfont icon-shoucang">收藏</text>
      </view>
    </view>

    <!-- 专辑 -->
    <view class="album_wrap" v-if="album.length">
      <view class="album_title">相关</view>
      <view class="albumlist">
        <view class="album_item" v-for="item in album" :key="item.id">
          <view class="album_cover">
            <image mode="aspectFill" :src="item.cover" />
          </view>
          <view class="album_info">
            <view class="album_info_text">专辑</view>
            <view class="album_name">{{ item.name }}</view>
            <text class="iconfont icon-jiantou"></text>
          </view>
        </view>
      </view>
    </view>

    <!-- 最热评论 -->
    <view class="comment hot" v-if="hot.length">
      <view class="comment_title">
        <text class="iconfont icon-huo"></text>
        <text class="comment_text">最热评论</text>
      </view>
      <view class="comment_list">
        <view class="comment_item" v-for="item in hot" :key="item.id">
          <!-- 用户信息 -->
          <view class="comment_user">
            <!-- 用户头像 -->
            <view class="user_icon">
              <image mode="widthFix" :src="item.user.avatar" />
            </view>
            <!-- 用户名 -->
            <view class="user_name">
              <view class="user_nickname">{{ item.user.name }}</view>
              <view class="user_time">{{ item.cnTime }}</view>
            </view>
            <!-- 用户徽章 -->
            <view class="user_badge">
              <image
                v-for="item2 in item.user.title"
                :key="item2.icon"
                :src="item2.icon"
              />
            </view>
          </view>
          <!-- 评论信息 -->
          <view class="comment_desc">
            <view class="comment_content">{{ item.content }}</view>
            <view class="comment_like">
              <text class="iconfont icon-dianzan1">{{ item.size }}</text>
            </view>
          </view>
        </view>
      </view>
    </view>

    <!-- 最新评论 -->
    <view class="comment new" v-if="comment.length">
      <view class="comment_title">
        <text class="iconfont icon-pinglun"></text>
        <text class="comment_text">最新评论</text>
      </view>
      <view class="comment_list">
        <view class="comment_item" v-for="item in comment" :key="item.id">
          <!-- 用户信息 -->
          <view class="comment_user">
            <!-- 用户头像 -->
            <view class="user_icon">
              <image mode="widthFix" :src="item.user.avatar" />
            </view>
            <!-- 用户名 -->
            <view class="user_name">
              <view class="user_nickname">{{ item.user.name }}</view>
              <view class="user_time">{{ item.cnTime }}</view>
            </view>
            <!-- 用户徽章 -->
            <view class="user_badge">
              <image
                v-for="item2 in item.user.title"
                :key="item2.icon"
                :src="item2.icon"
              />
            </view>
          </view>
          <!-- 评论信息 -->
          <view class="comment_desc">
            <view class="comment_content">{{ item.content }}</view>
            <view class="comment_like">
              <text class="iconfont icon-dianzan1">{{ item.size }}</text>
            </view>
          </view>
        </view>
      </view>
    </view>

    <!-- 下载 -->
    <view class="download">
      <view class="download_btn" @click="handleDownload">下载图片</view>
    </view>
  </view>
</template>

<script>
import moment from 'moment'
// 设置语言为中文
moment.locale('zh-cn')

import swiperAction from '@/components/swiperAction'

export default {
  components: {
    swiperAction
  },
  data() {
    return {
      imgDetail: {},
      // 专辑数组
      ablum: [],
      // 最热评论
      hot: [],
      // 最新评论
      comment: [],
      // 图片的索引
      imgIndex: 0
    }
  },
  onLoad() {
    // console.log(getApp().globalData);
    const { imgIndex } = getApp().globalData
    this.imgIndex = imgIndex

    this.getData()
  },
  methods: {
    getData() {
      const { imgList } = getApp().globalData
      this.imgDetail = imgList[this.imgIndex]
      this.imgDetail.cnTime = moment(this.imgDetail.atime * 1000).fromNow()

      // 获取图片详情id
      this.getComments(this.imgDetail.id)
    },
    getComments(id) {
      this.request({
        url: `http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${id}/comment`
      }).then(res => {
        this.alum = res.res.ablum

        // 给hot数组中的对象添加时间属性
        res.res.hot.forEach(i => (i.cnTime = moment(i.atime * 1000).fromNow()))
        res.res.comment.forEach(
          i => (i.cnTime = moment(i.atime * 1000).fromNow())
        )

        this.hot = res.res.hot
        this.comment = res.res.comment
      })
    },
    handleSwiperAction(e) {
      // 用户左滑，imgIndex++
      // 用户右滑，imgIndex--
      // 判断越界问题
      const { imgList } = getApp().globalData

      if (e.direction === 'left' && this.imgIndex < imgList.length - 1) {
        this.imgIndex++
        this.getData()
      } else if (e.direction === 'right' && this.imgIndex > 0) {
        this.imgIndex--
        this.getData()
      } else {
        uni.showToast({
          title: '哎呀，划不动了',
          icon: 'none'
        })
      }
    },
    // 点击下载
    async handleDownload() {
      await uni.showLoading({
        title: '下载中...'
      })

      // 1.将远程文件下载到小程序内存中
      const result1 = await uni.downloadFile({
        url: this.imgDetail.img
      })
      const tempFilePath = result1[1].tempFilePath

      // 2.将小程序内存中的临时文件下载到本地
      const result2 = await uni.saveImageToPhotosAlbum({
        filePath: tempFilePath
      })

      // 3.提示用户下载成功
      // console.log('下载成功');
      uni.hideLoading()

      await uni.showToast({
        title: '下载成功'
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.user_info {
  display: flex;
  padding: 20rpx;

  .user_icon {
    padding: 0 20rpx;

    image {
      width: 88rpx;
      border-radius: 50%;
    }
  }

  .user_desc {
    .user_name {
      color: #000;
      font-weight: 600;
    }

    .user_time {
      padding: 10rpx 0;
      font-size: 24rpx;
      color: #ccc;
    }
  }
}

.user_rank {
  display: flex;
  height: 80rpx;
  border-bottom: 1rpx solid #ccc;

  .rank {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .user_collect {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
  }
}

.album_wrap {
  padding: 20rpx;

  .album_title {
    padding: 10rpx 0;
  }

  .albumlist {
    .album_item {
      display: flex;
      padding: 10rpx 0;
      border-bottom: 1rpx solid #eee;

      .album_cover {
        flex: 1;

        image {
          width: 180rpx;
          height: 180rpx;
        }
      }

      .album_info {
        position: relative;
        flex: 3;
        padding-left: 20rpx;

        .album_info_text {
          width: 100rpx;
          height: 50rpx;
          background-color: $color;
          color: #fff;
          display: flex;
          justify-content: center;
          align-items: center;
        }

        .album_name {
          padding: 10rpx 0;
          color: #888;
        }

        .iconfont {
          position: absolute;
          top: 50%;
          right: 10rpx;
          transform: translateY(-50%);
          font-size: 40rpx;
          color: #000;
        }
      }
    }
  }
}

.comment {
  .comment_title {
    padding: 15rpx;

    .iconfont {
      color: #ff0000;
      font-size: 40rpx;
    }

    .comment_text {
      margin-left: 10rpx;
      font-style: 28rpx;
      font-weight: 600;
      color: #666;
    }
  }

  .comment_list {
    .comment_item {
      border-bottom: 5rpx solid #eee;

      // 用户信息
      .comment_user {
        display: flex;
        padding: 20rpx 0;

        .user_icon {
          width: 15%;
          display: flex;
          justify-content: center;
          align-items: center;

          image {
            width: 90%;
          }
        }

        .user_name {
          flex: 1;

          .user_nickname {
            color: #777;
          }

          .user_time {
            padding: 5rpx;
            font-style: 24px;
            color: #ccc;
          }
        }

        .user_badge {
          image {
            width: 40rpx;
            height: 40rpx;
            display: inline-block;
          }
        }
      }

      .comment_desc {
        display: flex;
        padding: 30rpx 0;

        .comment_content {
          flex: 1;
          padding-left: 15%;
          color: #000;
        }

        .comment_like {
          text-align: right;
        }
      }
    }
  }
}

.new {
  .icon-pinglun {
    color: $color !important;
  }
}

.download {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100rpx;

  .download_btn {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 90%;
    height: 75%;
    background-color: $color;
    color: #fff;
    font-size: 45rpx;
    font-weight: 600;
  }
}
</style>