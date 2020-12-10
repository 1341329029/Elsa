<template>
	<view>
		<view class="container">
			<view v-for='(item,index) in bgImgList' @tap="navigateToPoster(item)">
				<image class='img' :src="item.path"></image>			
			</view>
		</view>
		<view class='btn-add' hover-class="btn-add-active" @tap='chooseLocalImg'>
			<text class='iconfont icon-add btn-add-icon'></text>
		</view>
	</view>
</template>

<script>
	import posterBg from "@/common/posterBg.data.js";
	export default{
		data(){
			return{
				bgImgList: posterBg,
				goodStr: null
			}
		},
		methods: {
			bindPickerChange(e){
			    console.log('picker发送选择改变，携带值为', e.target.value)
			            // this.index = e.target.value
			},
			/* 选择本地图片 */
			chooseLocalImg(){
				var that=this;
				uni.chooseImage({
				    count:  1, 
				    sizeType:  ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
				    sourceType:  ['album'], //从相册选择
				    success:  function (res) {
						let uploadImg = { name: 'uploadImg', path: res.tempFilePaths[0] } 
						that.navigateToPoster(uploadImg)
				    }
				});
			},
			/* 跳转至海报编辑页面 */
			navigateToPoster(item) {
				console.log(item)
				uni.navigateTo({
					url:  `/pages/wm-poster/wm-poster?good=${this.goodStr}&posterBg=${JSON.stringify(item)}`,
					animationType:  'slide-in-right',
					animationDuration:  200
				})
			}
		},
		onLoad(options){
			this.goodStr = options.good
			console.log(JSON.parse(options.good));
		},
		onHide() {
			this.url=''
		}
	} 
</script>

<style>
	@import url("./frameChoose.css");
</style>
