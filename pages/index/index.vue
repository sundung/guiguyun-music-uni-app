<template>
  <view class="homeContainer">
    <!-- 轮播图区域 -->
    <swiper 
    :indicator-dots="true" 
    :autoplay="true" 
    :interval="3000" 
    :duration="1000"
    class="swiperContainer"
    >
      <swiper-item v-for="(item,index) in banners"
    :key="index">
        <view class="swiperItem">
          <image :src="item.imageUrl" mode=""></image>
        </view>
      </swiper-item>
    </swiper>
    <!-- 五个轮播图区域 -->
    <view class="navContainer">
      <view class="navItem">
        <text class="iconfont icon-meirituijian"></text>
         <text>每日推荐</text>
      </view>
      <view class="navItem">
        <text class="iconfont icon-gedan1"></text>
        <text>Other页面</text>
      </view>
      <view class="navItem">
        <text class="iconfont icon-icon-ranking"></text>
        <text>排行榜</text>
      </view>
      <view class="navItem">
        <text class="iconfont icon-diantai"></text>
        <text>电台</text>
      </view>
      <view class="navItem">
        <text class="iconfont icon-zhiboguankanliangbofangsheyingshexiangjixianxing"></text>
        <text>直播</text>
      </view>
    </view>
  </view>
</template>

<script>
  import request from "../../api/request.js"
  export default {
    data() {
      return {
        banners:[], // 轮播图数据
      };
    },
    created(){
      this.getBannerList();
    },
    methods:{
      // 获取轮播图数据
      async getBannerList(){
        let data = await request('/banner');
        console.log(data);
        if(data.code !== 200) {
          return false;
        }
        this.banners = data.banners;
      }
    }
  }
</script>

<style lang="scss" scoped>
  .homeContainer {
    // 轮播图区域
    .swiperContainer {
      .swiperItem {
        height: 300rpx;
        image {
          height: 100%;
          width: 100%;
        }
      }
    }
    // 五个导航区域
    .navContainer {
      display: flex;
      justify-content: space-evenly;
      .navItem {
        display: flex;
        flex-direction: column;
        align-items: center;
        width: 20%;
        .iconfont {
          width: 100rpx;
          height: 100rpx;
          border-radius: 50%;
          text-align: center;
          line-height: 100rpx;
          background: rgb(240, 19, 19);
          font-size: 50rpx;
          color: #fff;
          margin: 20rpx 0;
        }
        text {
          font-size: 26rpx;
        }
      }
       
    }
  }
</style>
