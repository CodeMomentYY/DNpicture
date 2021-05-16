<template>
  <view @touchstart="handleTouchStart" @touchend="handleTouchEnd"></view>
</template>

<script>
/* 
  1.给容器绑定两个触屏事件 touchstart 和 touchend
  2.用户按下屏幕事件
    2.1.记录用户按下屏幕的事件 Date.now() 时间戳，返回 1970.1.1 到现在的毫秒数
    2.2.记录用户按下屏幕的坐标 x 和 y
  3.用户离开屏幕事件
    3.1.记录用户离开屏幕的事件 Date.now()
    3.2.记录用户离开屏幕的坐标 x 和 y
    3.3.根据两个时间运算，判断用户按下屏幕的时长是否合法
    3.4.根据两对坐标，判断距离是否合法，和判断滑动的方向
*/

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
      if (Math.abs(endX - this.startX) > 10) {
        direction = endX - this.startX > 0 ? 'right' : 'left'
      } else {
        return
      }

      // 用户做了合法滑动操作
      console.log(direction);
    }
  }
}
</script>

<style lang="scss" scoped>
view {
  width: 100%;
  height: 500rpx;
  background-color: pink;
}
</style>