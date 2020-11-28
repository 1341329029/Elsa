<template>
	<view>
		<view class="container">
<!-- 			<view>
				<picker mode = 'selector' @change='bindPickerChange' :range="array" :value='index'> -->
					<view class='imgCon' @tap="chooseLocalImg()" >
						<image class='iconImg' src="../../static/iconPNG/add.png"></image>
					</view>
<!-- 				</picker>
			</view> -->
			<view v-for='(item,index) in bgImgList' @tap="navigateToPoster(item)">
				<image class='img' :src="item"></image>			
			</view>
			<!-- <image class="image" :src="url"></image> -->
		</view>
		<kps-image-cutter @ok="onok"
			@cancel="oncancle" 
			@original='onoriginal'
			:url="url" 
			:fixed="false" 
			:blob="false" 
			:maxWidth="500" 
			:maxHeight="500">
		</kps-image-cutter>

	</view>
</template>

<script>
	import kpsImageCutter from "@/components/ksp-image-cutter/ksp-image-cutter.vue";
	export default{
		components:{
			"kps-image-cutter":kpsImageCutter
		},
		data(){
			return{
				url:'',//待裁剪本地图片
				path:'',//已裁剪或提供模板图片
				bgImgList:[
					'../../static/bgImg/bgImg1.jpg',
					'../../static/bgImg/bgImg2.jpg',
					'../../static/bgImg/bgImg3.jpg',
					'../../static/bgImg/bgImg4.jpg'
				],
			}
		},
		methods:{
			    bindPickerChange(e){
			            console.log('picker发送选择改变，携带值为', e.target.value)
			            // this.index = e.target.value
			        },
			/* 选择本地图片 */
			chooseLocalImg(){
				var that=this;
				uni.chooseImage({
				    count: 1, 
				    sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
				    sourceType: ['album'], //从相册选择
				    success: function (res) {
						that.url = res.tempFilePaths[0];
				        console.log(res,JSON.stringify(res.tempFilePaths));
				    }
				});
			},
			/* 确定裁剪 */
			onok(ev) {
				// this.url = '';
				this.navigateToPoster(ev.path);
			},
			/* 取消裁剪 */
			oncancle() {
				// url设置为空，隐藏控件
				this.url = "";
			},
			onoriginal(){
				this.navigateToPoster(this.url)
			},
			/* 跳转至海报编辑页面 */
			navigateToPoster(bgPath) {
				console.log(bgPath)
				uni.navigateTo({
					url: "/pages/wm-poster/wm-poster?bgImg="+bgPath,
					animationType: 'slide-in-right',
					animationDuration: 200
				})
			}
		},
		onHide() {
			this.url=''
		}
	} 
</script>

<style>
	@import url("./frameChoose.css");
	.container{
		display: grid;
		grid-template-columns:50% 50%;
	}
	.imgCon{
		display: flex;
		align-item: center;
		position:relative;
		background-color: #EEEEEE;
	}
	.iconImg{
		width: 200rpx;
		height: 200rpx;
		margin: auto;
		position: absolute;
		top: 0; left: 0; bottom: 0; right: 0;
	}
	.img{
		width: 96%;
	}
</style>
