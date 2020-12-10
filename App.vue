<script>
	export default {
		globalData: {
			Info:{}
		},
		onLaunch: function() {
			console.log('App Launch')
			var that = this;
			uni.getSystemInfo({
				success: function(res){
					that.globalData.Info.pixelRatio = res.pixelRatio;//屏幕像素比
					that.globalData.Info.screenWidth = res.screenWidth;//屏幕宽度
					that.globalData.Info.screenHeight = res.screenHeight;//屏幕高度
					that.globalData.Info.windowWidth = res.windowWidth;//可用屏幕宽度
					that.globalData.Info.windowHeight = res.windowHeight;//可用屏幕高度
					that.globalData.Info.windowTop = res.windowTop;//可用窗口顶部位置
					that.globalData.Info.windowBottom = res.windowBottom;//可用窗口底部位置
					that.globalData.Info.statusBarHeight = res.statusBarHeight;//状态栏高度
					that.globalData.Info.platform = res.platform;//ios/android
				}
			})
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style lang="scss">
	@import "uview-ui/index.scss";
	@import url("./iconfont.css");
</style>
