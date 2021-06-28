<template>
  <view class="m-find module">
    <view class="m-view">
      <swiper
        class="swiper"
        :indicator-dots="true"
        autoplay="true"
        indicator-active-color="red"
        indicator-color="rgb(211, 211, 211, 0.385)"
      >
        <swiper-item
          v-for="(item, index) in banners"
          :key="index"
          class="m-swiper"
        >
          <image
            :src="item.pic"
            class="m-swiper__img"
          />
          <text
            class="m-swiper__text"
            :style="{background:item.titleColor}"
          >{{item.typeTitle}}</text>
        </swiper-item>
      </swiper>
    </view>
    <view>
      <scroll-view
        class="m-scroll-view"
        scroll-x="true"
      >
        <div class="m-scroll-view__items-container">
          <view
            class="m-scroll-view__items"
            v-for="(item, index) in balls"
            :key="index"
          >
            <image :src="item.iconUrl"></image>
            <text class="m-scroll-view__text">{{item.name}}</text>
          </view>
        </div>
      </scroll-view>
    </view>
    <view class="m-recommend-container">
      <view class="m-recommend-head">
        <text class="m-recommend-head__text">推荐歌单</text>
        <button
          type="default"
          plain="true"
          class="m-recommend-head__more"
          @click="jumpPage('/pages/index/components/songSquare')"
        >查看更多
        </button>
      </view>
      <scroll-view
        class="m-scroll-recommend"
        scroll-x="true"
      >
        <div class="m-scroll-container">
          <view
            class="m-recommend-scroll__items"
            v-for="(item, index) in recommends"
            :key="index"
            @tap="jumpPage('/pages/index/components/songList?id='+ item.creativeId)"
          >
            <image :src="item.uiElement.image.imageUrl"></image>
            <text class="m-recommend-scroll__text">{{item.uiElement.mainTitle.title}}</text>
          </view>
        </div>
      </scroll-view>
    </view>
  </view>
</template>

<script lang="ts">
import { Vue, Prop, Watch, Component } from 'vue-property-decorator';
import axios from "axios";

@Component({
})

export default class content extends Vue {
  @Prop({ default: 1 }) protected index!: number;

  protected banners: any[] | null = null;

  protected balls: any[] | null = null;

  protected recommends: any[] | null = null;

  mounted () {
    setTimeout(() => {
      console.log('start pulldown');
      uni.startPullDownRefresh({
        success: ((res) => {
          console.log(res);  //success 返回参数说明
          this.getData();
        })
      });  //这里表示当进入页面的时候就开始执行下拉刷新动画
    }, 1000);
  }

  getData () {
    axios.get("/homepage/block/page").then((res) => {
      console.log(res);
      this.banners = res.data.data.blocks[0].extInfo.banners;
      this.recommends = res.data.data.blocks[1].creatives;
    });

    axios.get("/homepage/dragon/ball").then((res) => {
      console.log(res);
      this.balls = res.data.data;
      console.log(this.balls);
    });
    this.$emit('getData');
  }

  jumpPage (myUrl: string) {
    uni.navigateTo({
      url: myUrl,
    })
  }
}
</script>

<style lang="scss" scoped>
.m-find.module {
  .m {
    &-view {
      display: flex;
      width: 100%;
      justify-content: center;
      margin: 20rpx 0;
      swiper {
        width: 100%;
      }
    }

    &-swiper {
      width: 90%;
      height: 100%;
      display: flex;
      justify-content: center;
      &__img {
        width: 90%;
        height: 100%;
        border-radius: $uni-border-radius-lg;
        // border: 0 20rpx 0 20rpx;
      }

      &__text {
        // background-color: red;
        font-size: 26rpx;
        padding: 8rpx 14rpx;
        color: white;
        // color: rgba(211, 211, 211, 0.385);
        position: absolute;
        right: 39rpx;
        bottom: 0;
        border-radius: $uni-border-radius-lg 0 $uni-border-radius-lg 0;
      }
    }

    &-scroll-view {
      ::-webkit-scrollbar {
        display: none;
      }
      display: flex;
      flex-direction: column;
      &__items-container {
        display: flex;
      }

      &__items {
        display: flex;
        flex-direction: column;
        align-items: center;
        padding: 0 30rpx;
        image {
          width: 90rpx;
          height: 90rpx;
          background: red;
          border-radius: $uni-border-radius-circle;
        }
      }

      &__text {
        font-size: $uni-font-size-sm;
        white-space: nowrap;
      }
    }

    &-recommend-container {
      display: flex;
      align-items: center;
      flex-direction: column;
      padding: 50rpx 0;
    }

    &-recommend-head {
      width: 90%;
      display: flex;
      flex-direction: row;
      align-items: center;
      justify-content: space-between;

      &__text {
        font-weight: bold;
      }

      &__more {
        display: flex;
        align-items: center;
        border-radius: 40rpx;
        font-size: $uni-font-size-sm;
        height: 50rpx;
        line-height: 50rpx;
        text-align: center;
        margin: 0;
        border-color: rgb(174, 174, 174);
      }
    }

    &-scroll-recommend {
      ::-webkit-scrollbar {
        display: none;
      }
      padding: 30rpx 0;
    }

    &-scroll-container {
      display: flex;
      flex-direction: row;
    }

    &-recommend-scroll {
      display: flex;
      justify-content: center;
      flex-direction: row;

      &__items {
        width: 200rpx;
        display: flex;
        flex-direction: column;
        align-items: center;
        padding: 0 12rpx;

        image {
          width: 200rpx;
          height: 200rpx;
          border-radius: $uni-border-radius-lg;
          overflow: hidden; /*超出的文本隐藏*/
          text-overflow: ellipsis; /* 溢出用省略号*/
        }
      }

      &__text {
        font-size: $uni-font-size-sm;
      }
    }
  }
}
</style>
