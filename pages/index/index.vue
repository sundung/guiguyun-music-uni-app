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
  }
</style>
