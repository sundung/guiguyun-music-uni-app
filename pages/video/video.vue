<template>
  <view class="videoContainer">
    <!-- 搜索头部区域 -->
    <view class="header">
      <image src="/static/images/video/video.jpg"></image>
      <view class="search" @click="goToSearch">
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
    <!-- 视频区域 -->
        <scroll-view 
        class="scrollVideo"  
        scroll-y 
        bindrefresherrefresh="handleRefresher"
        refresher-enabled
        :refresher-triggered="isTriggered"
        >
            <view class="videoItem" v-for="item in videoList" :key="item.id">
                <video 
                class="common"
                poster="item.data.coverUrl" 
                :data-id="item.data.vid" 
                @play='handlePlay' 
                object-fit="cover"
                :src="item.data.urlInfo.url"
                v-if="videoId == item.data.vid"
                @timeupdate="handleTimeUpdate"
                @ended="handleEnded"
                ></video>
                <image v-else :data-id="item.data.vid" class="common" :src="item.data.coverUrl" @tap='handlePlay'  />
                  
                <view class="scrollVideoContent">
                    <text class="text">{{item.data.title}}</text>
                    <view class="footer">
                        <view class="left">
                            <image class="avatar" :src="item.data.creator.avatarUrl" /> 
                            <text class="nickname">{{item.data.creator.nickname}}</text>
                        </view>
                        <view class="right">
                            <text class="item">
                                <text class="iconfont icon-buoumaotubiao15"></text>
                                <text class="count">{{item.data.praisedCount}}</text>
                            </text>
                            <text class="item">
                                <text class="iconfont icon-pinglun1"></text>
                                <text class="count">{{item.data.commentCount}}</text>
                            </text>
                            <button open-type="share" class="item btn">
                                <text class="iconfont icon-gengduo"></text>
                            </button>
                        </view>
                    </view>
                </view>
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
        videoList:[], // 视频数组
        videoId:'',// 视频播放的id
        videoUpdateTime: [], // 记录video播放的时长
        isTriggered:false ,// 控制下拉刷新的标识
        videoContext:null, // 视频播放实例对象
      };
    },
    created() {
      this.getVideoGroupList();
    },
    methods:{
      // 获取推荐导航 /video/group/list
        async getVideoGroupList(){
          let data = await request('/video/group/list');
            this.videoGroupList = data.data.slice(0,13);
            this.navId = data.data[0].id;
            // 此处调用导航对应下的视频数组,因为 能获取 navId
           this.getVideoList(this.navId);
        },
        // 点击推荐导航事件
          changeNav(event){
            console.log(event)
            let navId = event.currentTarget.id; // 通过id向event传参的时候如果传的是number会自动转换成string
            // let navId = event.currentTarget.dataset.id;
            // 加载loading状态
            wx.showLoading({
              title:'正在加载'
            })
              // 获取当前导航
              this.navId = navId;
              // 清空当前视频列表
              this.videoList = [];
            // 点击切换时加载数据
            this.getVideoList(this.navId);
          },
          // 获取推荐导航下对应的视频信息,注意该接口需要用户登录
            async getVideoList(navId){
              let data = await request('/video/group',{id:this.navId});
               // 关闭loading
              if(data) {
                // 关闭消息提示框
                wx.hideLoading();
              }
              // 设置一个id用作遍历的 key
              let index = 0;
              let videoList = data.datas.map(item => {
                item.id = index++;
                return item;
              })
             
                this.videoList = videoList
                this.isTriggered = false // 控制下拉刷新的标识
            },
            // 处理多个视频播放的问题
              handlePlay(event){
                // 1.获取上一个视频的id
                let vid = event.currentTarget.dataset.id;
                // 更新视频id
                  this.videoId = vid
                // 创建控制video标签的实例对象
                this.videoContext = wx.createVideoContext(vid);
                // 判断当前的视频之前是否播放过，是否有播放记录, 如果有，跳转至指定的播放位置
                let videoItem = this.videoUpdateTime.find(item => item.vid === vid);
                if(videoItem){
                  this.videoContext.seek(videoItem.currentTime);
                }
                // 自动播放
                this.videoContext.play();
              },
              // 跳转到播放过视频的对应时长
                handleTimeUpdate(event){
                  let videoTimeObj = {vid: event.currentTarget.dataset.id, currentTime: event.detail.currentTime};
                  /*
                  * 思路： 判断记录播放时长的videoUpdateTime数组中是否有当前视频的播放记录
                  *   1. 如果有，在原有的播放记录中修改播放时间为当前的播放时间
                  *   2. 如果没有，需要在数组中添加当前视频的播放对象
                  *
                  * */
                  let videoItem = this.videoUpdateTime.find(item => item.vid === videoTimeObj.vid);
                  if(videoItem){ // 之前有
                    videoItem.currentTime = event.detail.currentTime;
                  }else { // 之前没有
                    this.videoUpdateTime.push(videoTimeObj);
                  }
                  // 更新videoUpdateTime的状态
                
                },
                // 视频播放结束调用的回调
                handleEnded(event){
                  // 移除记录播放时长数组中当前视频的对象
                  this.videoUpdateTime.splice(this.videoUpdateTime.findIndex(item => item.vid === event.currentTarget.dataset.id), 1);  
                },
                // 跳转到搜索页面
                goToSearch(){
                  uni.navigateTo({
                    url:"/pages/search/search"
                  })
                }
    }
  }
</script>

<style lang="scss" scoped>
.videoContainer {
    // 头部区域
    .header {
        display: flex;
        padding: 10rpx;
        image {
            width: 60rpx;
            height: 60rpx;
        }
        .search {
            flex:1;
            border: 1px solid #eee;
            border-radius: 10rpx;
            font-size: 24rpx;
            text-align: center;
            line-height: 60rpx;
            margin: 0 20rpx;
            color: #d43c33;
        }
    }
    // 推荐列表区域
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
        }
        // 防止 激活样式不在
        .scrollContent {
            height: 60rpx;
            box-sizing: border-box;
        }
    }
    // 视频区域
    .scrollVideo{
        // 固定导航区域,需要给 scroll-view 的高度,需要动态计算
        height: calc(100vh - 152rpx);
        margin-top: 10rpx;
        .videoItem {
            padding: 0 3%;
            .common{
                width: 100%;
                height: 320rpx;
                border-radius: 10rpx;
            }
            .scrollVideoContent{
                
                .text {
                    font-size: 28rpx;
                    width: 440rpx;
                    height: 80rpx;
                    line-height: 80rpx;
                    // 单行文本溢出
                    display: block;
                    white-space: nowrap;
                    overflow: hidden;
                    text-overflow: ellipsis;
                }
                .footer {
                    height: 100rpx;
                    line-height: 100rpx;
                    border-top: 1px solid #eee;
                    display: flex;
                    justify-content: space-between;
                    .left {
                        .avatar {
                            width: 60rpx;
                            height: 60rpx;
                            vertical-align: middle;
                            border-radius: 50%;
                        }
                        .nickname {
                            margin-left: 20rpx;
                            font-size: 28rpx;
                        }
                    }
                    .right {
                        display: flex;
                        justify-content: space-between;
                        align-items: center;
                        .item {
                            position: relative;
                            width: 70rpx ;
                            .count {
                                position:absolute;
                                top: -30rpx;
                                font-size: 20rpx;
                            }
                        }
                        button{
                            width: 20rpx;
                            display: inline;
                            padding: 0;
                            background-color: transparent;
                            border-color: transparent;
                            &:after {
                                border: none;
                              } 
                        }
                    }
                }
            }
        }
    }
}
</style>
