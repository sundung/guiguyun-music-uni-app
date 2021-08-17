<template>
  <view class="videoContainer">
    <!-- 搜索头部区域 -->
    <view class="header">
      <image src="/static/images/video/video.jpg"></image>
      <view class="search" bindtap="goToSearch">
        搜索音乐
      </view>
      <image src="/static/images/logo.png"></image>
    </view>
    <!-- 推荐导航区域 -->
      <scroll-view
          scroll-x
          class="scrollContainer"
          enable-flex
          scroll-with-animation
      >
        <view  class="scrollItem" v-for="(item) in videoGroupList" :key="item.id">
          <view class="scrollContent" 
          :class="navId == item.id ? 'active' : ''" 
          @click="changeNav" :id="item.id" 
          :data-id="item.id">{{item.name}}</view>
        </view>
      </scroll-view>
  </view>
</template>

<script>
  import request from "../../api/request.js"
  export default {
    data() {
      return {
        videoGroupList:[], // 获取视频标签列表数组
        navId:'', // 控制激活样式的标识
      };
    },
    created() {
      this.getVideoGroupList();
    },
    methods:{
      // 获取推荐导航 /video/group/list
        async getVideoGroupList(){
          let data = await request('/video/group/list');
          console.log(data)
            this.videoGroupList = data.data.slice(0,13);
            this.navId = data.data[0].id;
        },
        // 点击推荐导航事件
          changeNav(event){
            console.log(event)
            let navId = event.currentTarget.id; // 通过id向event传参的时候如果传的是number会自动转换成string
            // let navId = event.currentTarget.dataset.id;
            // 加载loading状态
            // wx.showLoading({
            //   title:'正在加载'
            // })
              // 获取当前导航
              this.navId = navId;
              // 清空当前视频列表
              this.videoList = [];
            // 点击切换时加载数据
            // this.getVideoList(this.data.navId)
          }
    }
  }
</script>

<style lang="scss" scoped>
.videoContainer {
  padding: 0 20rpx;
  // 搜索头部区域
  .header {
    height: 80rpx;
    line-height: 80rpx;
    display: flex;
    justify-content: space-between;
    margin-top: 10rpx;
    image {
      width: 60rpx;
      height: 60rpx;
    }
    .search {
      flex: 1;
      margin: 0 20rpx;
      height: 60rpx;
      border: 1px solid red;
      border-radius: 30rpx;
      text-align: center;
      line-height: 60rpx;
    }
    
  }
  // 推荐导航区域
  .scrollContainer {
    display: flex;
    white-space: nowrap;
    height: 60rpx;
    .scrollItem {
      padding: 0 30rpx;
      font-size: 28rpx;
      height: 60rpx;
      line-height: 60rpx;
        .active {
           border-bottom: 1rpx solid #d43c33;
        }
        // 防止 激活样式不在
        .scrollContent {
            height: 60rpx;
            box-sizing: border-box;
        }
      }
  }
}
</style>
