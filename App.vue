<script>
	export default {
    globalData: {
        isMusicPlay: false, // 是否有音乐在播放
        musicId: '' // 音乐id
      },
		onLaunch: function() {
			console.warn('当前组件仅支持 uni_modules 目录结构 ，请升级 HBuilderX 到 3.1.0 版本以上！')
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
  /* 导入 iconfont 字体图标 */
  @import  'static/iconfont/iconfont.css';
	/*每个页面公共css */
  page {
    height: 100%;
  }
</style>
