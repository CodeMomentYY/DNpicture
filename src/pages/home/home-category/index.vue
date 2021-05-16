<template>
  <view class="home_category">
    <navigator
      :url="`/pages/imgCategory/index?id=${item.id}`"
      class="cate_item"
      v-for="item in category"
      :key="item.id"
    >
      <image mode="aspectFill" :src="item.cover" />
      <view class="cate_name">{{ item.name }}</view>
    </navigator>
  </view>
</template>

<script>
export default {
  data() {
    return {
      category: []
    }
  },
  mounted() {
    // 修改页面标题
    uni.setNavigationBarTitle({ title: '分类' })
    this.getList()
  },
  methods: {
    getList() {
      this.request({
        url: 'http://157.122.54.189:9088/image/v1/vertical/category'
      }).then(res => {
        this.category = res.res.category
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.home_category {
  display: flex;
  flex-wrap: wrap;

  .cate_item {
    position: relative;
    width: 33.33%;
    border: 5rpx solid #fff;

    image {
      height: 240rpx;
    }

    .cate_name {
      position: absolute;
      left: 0;
      bottom: 0;
      padding-left: 20rpx;
      width: 100%;
      height: 50rpx;
      background-image: linear-gradient(
        to right top,
        rgba(0, 0, 0, 0.2),
        rgba(0, 0, 0, 0)
      );
      color: #fff;
      font-size: 40rpx;
      display: flex;
      align-items: center;
    }
  }
}
</style>