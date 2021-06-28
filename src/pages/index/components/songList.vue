<template>
  <view class="p-song-list page">
    <view class="p-head-container">
      <image
        class="p-head-container__img"
        :src="listDetail.coverImgUrl"
      />
      <view class="p-head-detail">
        <text>{{listDetail.name}}</text>
        <text class="p-head-detail__description">{{listDetail.description}}</text>
      </view>
    </view>
    <view class="p-middle">
      <view>
        <text>评论：</text>
        <text>{{listDetail.commentCount}}</text>
      </view>
      <view>
        <text>分享：</text>
        <text>{{listDetail.shareCount}}</text>
      </view>
      <view>
        <text>下载</text>
      </view>
      <view>
        <text>多选</text>
      </view>
    </view>
    <view class="p-list">
      <view class="p-list-head">
        <text>播放全部</text>
      </view>
      <view class="p-list-body">
        <scroll-view scroll-x="true">
          <view
            class="p-item-container"
            v-for="(item, index) in listDetail.tracks"
            :key="index"
            @click="playerMusic(item)"
          >
            <text>{{index + 1}}</text>
            <view>
              <view class="p-item-container__text">
                <text>{{item.name}}</text>
                <text class="p-item-container__text--author">{{item.ar[0].name}}</text>
              </view>
            </view>
          </view>
        </scroll-view>
      </view>
      <!-- <view>
        <audio
          style="text-align: left"
          :src="music.url"
          :id="music.id"
          :poster="music.poster"
          :name="music.name"
          :author="music.author"
          :action="audioAction"
          controls
        ></audio>
      </view> -->
    </view>
  </view>
</template>

<script lang='ts'>
import { Vue, Prop, Watch, Component } from 'vue-property-decorator';
import axios from "axios";

@Component({

})

export default class songList extends Vue {
  protected songList = [];
  protected listDetail: any | null = null;
  protected innerPlayer = uni.createInnerAudioContext();
  protected music = {
    id: 0,
    name: '',
    poster: '',
    url: '',
    author: '',
  }

  protected audioAction = {
    method: 'pause',
  }

  onLoad (options: any) {
    axios.get("/playlist/detail?id=" + options.id).then((res) => {
      console.log(res);
      this.listDetail = res.data.playlist;
      console.log(this.listDetail);
    })
    if(this.innerPlayer.src) this.innerPlayer.autoplay = true;
  }

  onPullDownRefresh () {
    setTimeout(function () {
      uni.stopPullDownRefresh();
    }, 1000);
  }

  playerMusic (item: {id: number, name: string, al: {picUrl: string}, ar: {name: string}[]}) {
    // axios.get("/song/url?id=" + item.id).then((res) => {
    //   if(res.data){
    //     this.music.id = item.id;
    //     this.music.url = res.data.data[0].url;
    //     this.music.poster = item.al.picUrl;
    //     this.music.name = item.name;
    //     this.music.author = item.ar[0].name;
    //   }
    // })
    this.innerPlayer.src = "/song/url?id=" + item.id;
    this.innerPlayer.onPlay(() => {
      console.log('开始播放');
    });
  }

}
</script>

<style lang="scss" scoped>
.p-song-list {
  .p {
    ::-webkit-scrollbar {
      display: none;
    }
    &-head-container {
      margin-top: 26rpx;
      display: flex;
      flex-direction: row;
      &__img {
        width: 280rpx;
        height: 280rpx;
        flex-shrink: 0;
        margin: 0 30rpx 0 20rpx;
        border-radius: $uni-border-radius-lg;
      }
    }

    &-head-detail {
      flex-shrink: 1;
      display: flex;
      flex-direction: column;
      &__title {
        overflow: hidden; /* 多于两行的内容将会被隐藏 */
        -webkit-line-clamp: 2;
        text-overflow: ellipsis; /* 文本溢出用省略号代替 */
        display: -webkit-box; /* 定义为盒子显示 */
        -webkit-box-orient: vertical; /*定义为竖排显示 */
      }

      &__description {
        margin: 30rpx 0;
        line-height: 40rpx;
        font-size: $uni-font-size-sm;
        overflow: hidden; /* 多于两行的内容将会被隐藏 */
        -webkit-line-clamp: 2;
        text-overflow: ellipsis; /* 文本溢出用省略号代替 */
        display: -webkit-box; /* 定义为盒子显示 */
        -webkit-box-orient: vertical; /*定义为竖排显示 */
      }
    }

    &-middle {
      display: flex;
      flex-direction: row;
      justify-content: space-around;
      margin: 30rpx 0;
    }

    &-item-container {
      display: flex;
      flex-direction: row;
      align-items: center;
      margin: 20rpx 0;
      &__text {
        display: flex;
        flex-direction: column;
        margin-left: 40rpx;
        &--author {
          font-size: $uni-font-size-sm;
        }
      }
    }
  }
}
</style>
