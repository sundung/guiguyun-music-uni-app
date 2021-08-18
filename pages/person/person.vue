<template>
  <view class="personalConntainer">
      <!-- 个人信息区域 -->
      <view class="userSection">
          <!-- 背景图片 -->
          <image class="bgImg" src="/static/images/personal/bgImg2.jpg"  />
          <!-- 头像区域 -->
          <view class="avatar" @click="goToLogin">
              <image class="" :src='userInfo.avatarUrl?userInfo.avatarUrl:"/static/images/personal/missing-face.png"'  />
              <text class="name" >
                  {{userInfo.nickname?userInfo.nickname: '请登录'}}
              </text> 
          </view>
          <!-- 退出登录按钮 -->
          <view class="loginOut" v-if="userInfo.nickname" @click="loginOut">
              退出登录
          </view>
          <!-- vip区域 -->
          <view class="vip-card-box">
              <!-- 背景图片 -->
              <image class="" src="/static/images/personal/vip-card-bg.png" />
              <!-- 标题区域 -->
              <view class="title">
                  <view class="left">
                      <text class="iconfont icon-huiyuan-" style="color: #f6e5a3;"></text>
                      <text class="" >硅谷会员</text>    
                  </view>
                  <view class="right">立即开通</view>
              </view>
              <view class="content">
                  <text class="em-a">开通会员听歌, 撸代码</text>
                  <text class="em-b">开通会员听歌, 撸代码</text>
              </view>
          </view>
      </view>
      <!-- 个人具体操作信息 -->
        <!-- 个人具体操作信息 -->
           <view
           class="personalControl" 
           @touchstart="handleTouchStart" 
           @touchmove="handleTouchMove" 
           @touchend="handleTouchEnd"
           :style="{transform: coverTransform,transition: coveTransition}"
           >
               <image class="image" src="/static/images/personal/arc.png" />
               <view class="nav-box">
                   <view class="nav-item">
                       <text class="iconfont icon-xiaoxi" ></text>
                       <text class="text" >我的消息</text>   
                   </view>
                   <view class="nav-item">
                       <text class="iconfont icon-myRecommender" ></text>
                       <text class="text" >我的好友</text>   
                   </view>
                   <view class="nav-item">
                       <text class="iconfont icon-gerenzhuye" ></text>
                       <text class="text" >个人主页</text>   
                   </view>
                   <view class="nav-item">
                       <text class="iconfont icon-gexingzhuangban" ></text>
                       <text class="text" >个性装扮</text>   
                   </view>
               </view>
               <!-- 个人播放记录 -->
           <view class="personalContent">
               <view class="title">
                   <text class="first">最近播放</text>
               </view>
               <scroll-view v-if="userPlayRecord.length" scroll-x class="recentScroll" enable-flex>
                 <view class="recentItem" v-for="(item,index) in userPlayRecord" :key="item.id">
                   <image class="" :src="item.song.al.picUrl"/>             
                 </view>
               </scroll-view>
               <view wx:else>暂无播放记录</view>
               <view class="nav-box">
                   <view class="item">
                       <text>我的音乐</text>
                       <text>></text>
                   </view>
                   <view class="item">
                       <text>我的收藏</text>
                       <text>></text>
                   </view>
                   <view class="item">
                       <text>我的电台</text>
                       <text>></text>
                   </view>
               </view>
           </view>
           </view>
  </view>
</template>

<script>
  import request from "../../api/request.js"
  export default {
    data() {
      return {
         startY : 0,// 手指起始的坐标
         moveY : 0, // 手指移动的坐标
        lmoveDistance: 0, // 手指移动的距离
        coverTransform: 'translateY(200)rpx', // 下拉动画的移动距离
        coveTransition:'', // 动画
        userInfo:{}, // 用户信息
        userPlayRecord:[] // 用户播放记录数据
      };
    },
    created() {
      // 进入页面判断当前用户是否登录
          // 读取用户的基本信息
          let userInfo = wx.getStorageSync('userInfo');
          if(userInfo){ // 用户登录
            // 更新userInfo的状态
              this.userInfo =  JSON.parse(userInfo)
          }
          // this.getUserPlayRecord(this.data.userInfo.userId);
    },
    methods:{
      // 获取用户播放记录
        async getUserPlayRecord(userID){
          let data = await request('/user/record',{uid:userID,type:0})
          let index = 0;
          let userPlayRecord = data.allData && data.allData.slice(0, 10).map(item => {
            item.id = index++;
            return item;
          })
          console.log(userPlayRecord)
          
           this.userPlayRecord = userPlayRecord;
        },
        handleTouchStart(event){
          // 获取起始坐标
           this.startY = event.touches[0].clientY;
        },
        handleTouchMove(event){
          // 获取移动的坐标
           this.moveY = event.touches[0].clientY;
          // 获取手指的移动距离
          var moveDistance = this.moveY - this.startY;
          // 禁止上划
          if(moveDistance <= 0){
            return;
          }
          // 滑动的最大距离为 80 
          if(moveDistance >= 80){
            moveDistance = 80;
          }
          // 动态更新手指的移动距离
            this.coverTransform = `translateY(${moveDistance}rpx)`
        },
        handleTouchEnd(event){
          // 动态更新coverTransform的状态值
            this.coverTransform = `translateY(0rpx)`
            this.coveTransition =  'transform 1s linear'
        },
       
        
    }
  }
</script>

<style lang="scss" scoped>
  .personalConntainer {
      width: 100%;
      height: 100%;
      position: relative;
      // 个人信息区域
      .userSection {
          position: relative;
          // 背景图片区域
          .bgImg{
              width: 100%;
              height: 620rpx;
          }
          // 头像区域
          .avatar {
              position: absolute;
              top: 140rpx;
              left: 40rpx;
              display: flex;
              image {
                  width: 140rpx;
                  height: 140rpx;
                  border: 4px solid #fff;
                  border-radius: 50%;
              }
              .name {
                  margin-left: 20rpx;
                  line-height: 140rpx;
              }
          }
          .loginOut {
              position: absolute;
              top: 180rpx;
              right: 40rpx;
              background-color: #f5f5f5;
              padding: 15rpx;
              border: 1px solid #000;
              font-size: 30rpx;
              border-radius: 60rpx;
          }
          // vip 区域
          .vip-card-box {
              position: absolute;
              top: 340rpx;
              left: 40rpx;
              background: rgba(0, 0, 0, .7);
              height: 240rpx;
              width: 90%;
              border-radius: 20rpx;
              padding: 20rpx;
              box-sizing: border-box;
              image {
                  width: 380rpx;
                  height: 260rpx;
                  float: right;
              }
              // 标题区域
              .title {
                  position: absolute;
                  width: 95%;
                  height: 60rpx;
                  font-size: 22rpx;
                  color: #f7d680;
                  display: flex;
                  justify-content: space-between;
                  .left{
                      .icon-huiyuan- {
                          margin-right: 20rpx;
                      }
                  }
                  .right {
                      width: 132rpx;
                      height: 40rpx;
                      text-align: center;
                      line-height: 40rpx;
                      font-size: 22rpx;
                      color: #36343c;
                      border-radius: 20px;
                      background: #f9e6af;
                  }
              }
              // 内容区域
              .content {
                  position: absolute;
                  display: flex;
                  flex-direction: column;
                  margin-top: 80rpx;
                  
                  .em-a {
                      font-size: 34rpx;
                      color: #f9e6af;;
                  }
                  .em-b{
                      margin-top: 20rpx;
                      font-size: 24rpx;
                      color: #d8cba9;
                  }
              }
          }
      }
      // 个人具体操作信息
      .personalControl {
          // position: absolute;
          // left: 0;
          // top: 580rpx;
          height: 740rpx;
          width: 100%;
          background-color: #f5f5f5;
          margin-top: -40rpx;
          padding: 0 40rpx;
          box-sizing: border-box;
          .image {
              position: absolute;
              left: 0;
              top: -30rpx;
              width: 100%;
              height: 36rpx;
          }
          .nav-box {
              background: #fff;
              padding: 20rpx 0;
              border-radius: 15rpx;
              display: flex;
              justify-content: space-evenly;
              align-items: center;
              .nav-item {
                  display: flex;
                  flex-direction: column;
                  justify-content: space-between;
                  align-items: center;
                  .iconfont {
                      font-size: 50rpx;
                      color: #d43c33;
                      line-height: 70rpx;
                  }
                  .text {
                      font-size: 22rpx;
                  }
              }
          }
          .personalContent {
              margin-top: 20rpx;
              background-color: #fff;
              width: 100%;
              .title {
                 display: flex;
                 flex-direction: column;
                 .first {
                  padding-left: 20rpx;
                  font-size: 26rpx;
                  color: #333;
                  line-height: 80rpx;
                 }
              }
              // 播放记录
              .recentScroll {
                  display: flex;
                  height: 200rpx;
                  .recentItem {
                      image {
                          width: 200rpx;
                          height: 200rpx;
                          margin-right:20rpx;
                          border-radius: 10rpx;
                      }
                  }
              }
              .nav-box {
                  display: flex;
                  flex-direction: column;
                  align-items: space-between;
                  // padding: 20rpx;
                  .item {
                      width: 100%;
                      padding: 20rpx;
                      border-top: 1rpx solid #eee;
                      height: 80rpx;
                      line-height: 80rpx;
                      font-size: 26rpx;
                      color: #333;
                      box-sizing: border-box;
                      display: flex;
                      justify-content: space-between;
                      align-items: center;
                  }
              }
          }
      }
  }
</style>
