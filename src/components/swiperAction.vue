<template>
  <view>
    <view @touchstart="handleTouchStart" @touchend="handleTouchEnd">
      <slot></slot>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      // 按下的时间
      startTime: 0,
      // 按下的坐标
      startX: 0,
      startY: 0
    }
  },
  methods: {
    // 用户按下屏幕
    handleTouchStart(e) {
      // console.log('按下：' + e.changedTouches[0].clientX)
      // console.log('按下：' + e.changedTouches[0].clientY)

      this.startTime = Date.now()
      this.startX = e.changedTouches[0].clientX
      this.startY = e.changedTouches[0].clientY
    },
    // 用户离开屏幕
    handleTouchEnd(e) {
      // console.log('松开：' + e.changedTouches[0].clientX)
      // console.log('松开：' + e.changedTouches[0].clientY)

      const endTime = Date.now()
      const endX = e.changedTouches[0].clientX
      const endY = e.changedTouches[0].clientY

      // 判断按下时长
      if (endTime - this.startTime > 2000) {
        return
      }

      // 滑动方向
      let direction = ''

      // 先判断用户滑动距离是否合法，再判断滑动方向
      // 注意：距离要加上绝对值
      if (Math.abs(endX - this.startX) > 10 && Math.abs(endY - this.startY < 10)) {
        direction = endX - this.startX > 0 ? 'right' : 'left'
      } else {
        return
      }

      // 用户做了合法滑动操作
      // console.log(direction)
      this.$emit('swiperAction', { direction })
    }
  }
}
</script>

<style lang="scss" scoped>
</style>