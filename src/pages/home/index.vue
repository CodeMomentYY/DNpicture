<template>
  <view>
    <view class="home_tab">
      <view class="home_tab_title">
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
      <view class="home_tab_content">
        <view v-if="current === 0">
          <home-recommend></home-recommend>
        </view>
        <view v-if="current === 1">
          <home-category></home-category>
        </view>
        <view v-if="current === 2">
          <home-new></home-new>
        </view>
        <view v-if="current === 3">
          <home-album></home-album>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
import homeAlbum from './home-album'
import homeCategory from './home-category'
import homeNew from './home-new'
import homeRecommend from './home-recommend'

import { uniSegmentedControl } from '@dcloudio/uni-ui'

export default {
  components: {
    homeAlbum,
    homeCategory,
    homeNew,
    homeRecommend,
    uniSegmentedControl
  },
  data() {
    return {
      items: [
        { title: '推荐' },
        { title: '分类' },
        { title: '最新' },
        { title: '专辑' }
      ],
      current: 0
    }
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
      }
    }
  }
}
</script>

<style lang="scss" scope>
.home_tab {
  .home_tab_title {
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
}
</style>
