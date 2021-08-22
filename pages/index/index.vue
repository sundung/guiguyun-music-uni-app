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
      <view class="navItem" @click="goTorecommendSong">
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
    <!-- 推荐歌曲区域 -->
    <view class="recommendSongContainer">
      <view class="header">
        <view class="title">
          推荐歌曲
        </view>
          <view class="text">
            <text>为你精心推荐</text>
            <text class="btn">查看更多</text>
          </view>
      </view>
      <!-- 内容区域 -->
      <scroll-view class="recommendSongScroll" scroll-x='true'  enable-flex='true'>
         <block   v-for="(item,index) in recommendSong" :key="index">
             <view class="scrollItem">
               <image class="image" :src="item.picUrl" mode=""></image>
               <text>{{item.name}}</text>
             </view>
         </block>
       </scroll-view>
    </view>
     <!-- 排行榜区域 -->
     <view class="topSongList">
       <view class="header">
         <view class="title">
           排行榜
         </view>
           <view class="text">
             <text>热歌风向标</text>
             <text class="btn">查看更多</text>
           </view>
       </view>
       <!-- 内容区域 -->
       <swiper class="topSongListContainer" circular style="height:450rpx" next-margin="50rpx" previous-margin="50rpx">
         <swiper-item v-for="(item,index) in topSongList" :key="index">
           <view class="swiper-item" >
             <!-- 头部区域 -->
             <view class="title">
               {{item.name}}
             </view>
             <view class="content" v-for="(item1,index1) in item.tracks" :key="index1">
               <image :src="item1.al.picUrl" mode="" ></image>
               <text class="count">{{index1 + 1}}</text>
               <text class="musicName">{{item1.name}}</text>
             </view>
           </view>
         </swiper-item>
       </swiper>
     </view>
     
     
     
     
  </view>
</template>

<script>
  import request from "../../api/request.js"
  export default {
    data() {
      return {
        banners:[], // 轮播图数据
        recommendSong:[], // 推荐歌曲列表数据
        topSongList:[], // 排行榜数据
      };
    },
    created(){
      this.getBannerList();
      this.getRecommendSong();
      this.getTopSongList();
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
      },
      // 获取推荐歌曲列表数据
      async getRecommendSong(){
        let data = await request('/personalized',{limit:10});
        console.log(data);
        this.recommendSong = data.result;
      },
      // 获取排行榜数据
      async getTopSongList(){
        let index = 0;
        while(index < 5){
          let data = await request('/top/list',{idx:index ++});
           let topListItem = {name: data.playlist.name, tracks: data.playlist.tracks.slice(0, 3)};
           this.topSongList.push(topListItem);
          console.log(this.topSongList);
        }
      },
      // 点击每日推荐跳转到推荐页面
      goTorecommendSong(){
        uni.navigateTo({
          url:'/pages/recommendSong/recommendSong'
        })
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
    // 推荐歌曲区域
    .recommendSongContainer {
      padding: 20rpx;
      .header {
        .title {
          font-size: 32rpx;
          color: #666;
        }
        .text {
          display: flex;
          justify-content: space-between;
          align-items: center;
          font-size: 26rpx;
          margin-top: 20rpx;
          .btn {
            border: 1rpx solid #333;
            padding: 10rpx 20rpx;
            font-size: 24rpx;
            border-radius: 30rpx;
          }
        }
      }
      // 内容区域
      .recommendSongScroll {
        margin-top: 20rpx;
        height: 300rpx;
        display: flex;
        white-space: nowrap;
        .scrollItem {
          width: 200rpx;
          display: inline-block;
          margin-right: 20rpx;
          .image {
            width: 200rpx;
            height: 200rpx;
            border-radius: 10rpx;
          }
          text {
            font-size: 26rpx;
            /* 多行文本溢出隐藏 省略号代替*/
              overflow: hidden;
              text-overflow: ellipsis;
              display: -webkit-box;
              -webkit-box-orient: vertical; /*设置对其模式*/
              -webkit-line-clamp: 2; /*设置多行的行数*/
          }
        }
      }
    }
    // 排行榜区域
    .topSongList {
      padding: 20rpx;
      .header {
        .title {
          font-size: 32rpx;
          color: #666;
        }
        .text {
          display: flex;
          justify-content: space-between;
          align-items: center;
          font-size: 26rpx;
          margin-top: 20rpx;
          .btn {
            border: 1rpx solid #333;
            padding: 10rpx 20rpx;
            font-size: 24rpx;
            border-radius: 30rpx;
          }
        }
      }
      // 排行榜内容区域
      .topSongListContainer {
        .swiper-item {
          margin-top: 20rpx;
           width: 96%;
           background: #fbfbfb;
          .title {
            font-size: 32rpx;
          }
         .content {
           display: flex;
           align-items: center;
           margin-top: 20rpx;
           image {
             width: 100rpx;
             height: 100rpx;
             border-radius: 10rpx;
           }
           .count {
               width: 100rpx;
               height: 100rpx;
               text-align: center;
               line-height: 100rpx;
           }
           .musicName {
               height: 100rpx;
               line-height: 100rpx;
               max-width: 400rpx;
               white-space: nowrap;
               overflow: hidden;
               text-overflow: ellipsis;
           }
         }
        }
      }
    }
  }
</style>
