<template>
  <view>
    <view class="uni-tab-bar">
      <scroll-view
        scroll-x
        class="uni-swiper-tab"
      >
        <block
          v-for="(tabBar,index) in tabBars"
          :key="index"
        >
          <view
            class="swiper-tab-list"
            :class="{'active': tabIndex === index}"
            @tap="toggleTab(tabBar.id)"
          >
            {{tabBar.name}}
            <view class="swiper-tab-line">
            </view>
          </view>
        </block>
      </scroll-view>
    </view>
    <view>
      <m-find v-if="selectedTab.id === 1" ref="find" :index="tabIndex" @getData="getData"></m-find>
    </view>
  </view>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import uniNavBar from '@/components/common/uni-nav-bar/uni-nav-bar/uni-nav-bar.vue';
import MFind from '@/pages/index/components/find.vue';

@Component({
  components: {
    MFind,
  }
})

export default class Index extends Vue {
  protected tabIndex = 1;
  protected tabBars = [
    { name: '我的', id: 0 },
    { name: '发现', id: 1 },
    { name: '云村', id: 2 },
    { name: '视频', id: 3 }
  ];

  protected contentList = [
    "我的",
    "发现",
    "云村",
    "视频"
  ];

  protected selectedTab =  {name: '发现', id: 1 };

  onLoad(): void{
    this.$nextTick(() => {
      // if(this.selectedTab.id == 1)(this.$refs.find as MFind).getData();
    })
  }

  toggleTab (index: number): void {
    this.tabIndex = index
    this.selectedTab = this.tabBars[index];
  }

  //滑动切换swiper
  tabChange (e: { detail: any }): void {
    const tabIndex = e.detail.current;
    this.tabIndex = tabIndex;
  }

  onPullDownRefresh () {
    //监听下拉刷新动作的执行方法，每次手动下拉刷新都会执行一次
    console.log('refresh');
    setTimeout(() => {
      (this.$refs.find as MFind).getData();
      this.getData();
      uni.stopPullDownRefresh();  //停止下拉刷新动画
    }, 1000);
  }

  getData(): void{}
}

</script>

<style>
.uni-tab-bar {
  width: 100%;
}

.content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.swiper-tab-list {
  color: #969696;
  font-weight: bold;
}
.active {
  color: #343434;
}

.active .swiper-tab-line {
  border-bottom: 6upx solid #fede33;
  width: 70upx;
  margin: auto;
  border-top: 6upx solid #fede33;
  border-radius: 20upx;
}
.uni-swiper-tab {
  border-bottom: 1upx solid #eeeeee;
}
</style>
