<template>
  <view class="video_play">
    <image :src="videoObj.img" />

    <!-- 工具栏 -->
    <view class="video_tool">
      <view
        :class="['iconfont', muted ? 'icon-shengyinguan' : 'icon-volume']"
        @click="handleMuted"
      ></view>
      <view class="iconfont icon-zhuanfa">
        <button open-type="share"></button>
      </view>
    </view>

    <!-- 视频 -->
    <view class="video_wrap">
      <video :src="videoObj.video" objectFit="fill" :muted="muted"></video>
    </view>

    <!-- 按钮 -->
    <view class="download">
      <view class="download_btn" @click="handleDownload">下载高清</view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      videoObj: {},
      // 是否静音
      muted: false
    }
  },
  onLoad() {
    // console.log(getApp().globalData);
    this.videoObj = getApp().globalData.video
  },
  methods: {
    handleMuted() {
      this.muted = !this.muted
    },
    // 下载视频
    async handleDownload() {
      await uni.showLoading({
        title: '下载中...'
      })

      // 1.将远程文件下载到小程序内存中
      const { tempFilePath } = (
        await uni.downloadFile({
          url: this.videoObj.video
        })
      )[1]
      // 2.将内存中的文件下载到本地
      await uni.saveVideoToPhotosAlbum({
        filePath: tempFilePath
      })

      await uni.showToast({
        title: '下载成功'
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.video_play {
  position: relative;

  image {
    position: absolute;
    width: 100vw;
    height: 100vh;
    z-index: -1;
    filter: blur(20px);
  }

  .video_tool {
    margin-top: 10rpx;
    height: 80rpx;
    display: flex;
    justify-content: flex-end;

    .iconfont {
      margin-right: 20rpx;
      width: 80rpx;
      height: 80rpx;
      background-color: rgba(0, 0, 0, 0.2);
      border-radius: 50%;
      color: #fff;
      font-size: 50rpx;
      display: flex;
      justify-content: center;
      align-items: center;
    }

    .icon-zhuanfa {
      position: relative;

      button {
        position: absolute;
        width: 100%;
        height: 100%;
        opacity: 0;
      }
    }
  }

  .video_wrap {
    display: flex;
    justify-content: center;

    video {
      width: 360rpx;
      height: 600rpx;
    }
  }

  .download {
    display: flex;
    justify-content: center;
    margin-top: 30rpx;

    .download_btn {
      width: 360rpx;
      height: 80rpx;
      background-color: rgba(0, 0, 0, 0.2);
      border: 3rpx solid #fff;
      border-radius: 40rpx;
      display: flex;
      justify-content: center;
      align-items: center;
      color: #fff;
    }
  }
}
</style>