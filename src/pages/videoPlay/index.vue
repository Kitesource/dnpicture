<template>
  <view class="video_play">
    <image :src="videoObj.img" mode="" />
    <!-- 工具栏 -->
    <view class="video_tool">
      <view @click="handleMuted" :class="['iconfont', muted?'icon-jingyin':'icon-shengyin']"></view>
      <view class="iconfont icon-zhuanfa">
        <button open-type="share"></button>
      </view>
    </view>
    <!-- 视频 -->
    <view class="video_wrap">
      <video :muted="muted" :src="videoObj.video" objectFit="fill"></video>
    </view>
    <!-- 下载 -->
    <view class="download">
      <view class="download_btn" @click="handleDownloadVideo">下载高清</view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      videoObj: {},
      //是否静音, false为不要静音
      muted:false,
    };
  },
  onLoad(args) {
    // console.log(getApp().globalData);
    this.videoObj = getApp().globalData.video;
  },
  methods: {
    // 开关声音
    handleMuted() {
      this.muted = !this.muted;
    },
    // 下载视频
    async handleDownloadVideo() {

      await uni.showLoading({
        title: '下载中...',
        mask: true
      })

      //1 将远程文件下载到小程序内存中,返回的是一个数组，取数组第2个元素
      const {tempFilePath} = (await uni.downloadFile({url: this.videoObj.video}))[1];
      //2 将小程序内存下载到本地
      await uni.saveVideoToPhotosAlbum({
        filePath: tempFilePath,
        success: function () {
          uni.hideLoading();
        }
      });
      await uni.showToast({
        title: '下载成功',
        icon: 'success'
      })
    }
  }
};
</script>

<style lang="scss" scoped>
.video_play {
  position: relative;
  image {
    position: absolute;
    width: 100vw;
    height: 100vh;
    z-index: -1;
    //css 滤镜
    filter: blur(15px);
  }

  .video_tool {
    height: 80rpx;
    display: flex;
    justify-content: flex-end;
    .iconfont {
      width: 80rpx;
      color: #fff;
      font-size: 40rpx;
      border-radius: 50%;
      background-color: rgba(0, 0, 0, 0.2);
      display: flex;
      justify-content: center;
      align-items: center;
      margin-right: 20rpx;
    }
    .icon-zhuanfa{
      position: relative;
      button{
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
      color: #fff;
      width: 360rpx;
      height: 80rpx;
      border-radius: 40rpx;
      display: flex;
      justify-content: center;
      align-items: center;
      border: 2rpx solid #fff;
      background-color: rgba(0, 0, 0, 0.6);
    }
  }
}
</style>