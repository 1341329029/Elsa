<template>
	<view class="container">
		<view class='imgCon' @click="chooseLocalImg">
			<image class='iconImg' src="../../static/iconPNG/add.png"></image>
		</view>
		<view v-for='(item,index) in bgImgList' @click="chooseFrame(item)">
			<image class='img' :src="item"></image>			
		</view>
	</view>
</template>

<script>
	export default{
		data(){
			return{
				bgImgList:[
					'../../static/bgImg/bgImg1.jpg',
					'../../static/bgImg/bgImg2.jpg',
					'../../static/bgImg/bgImg3.jpg',
					'../../static/bgImg/bgImg4.jpg'
				],
			}
		},
		methods:{
			chooseLocalImg(){
				uni.chooseImage({
				    count: 1, 
				    sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
				    sourceType: ['album'], //从相册选择
				    success: function (res) {
				        console.log(res,JSON.stringify(res.tempFilePaths));
						uni.navigateTo({
						    url: "/pages/wm-poster/wm-poster?bgImg="+res.tempFilePaths[0],
							animationType: 'slide-in-right',
							animationDuration: 200
						});
				    }
				});
			},
			chooseFrame(item){
				uni.navigateTo({
				    url: "/pages/wm-poster/wm-poster?bgImg="+item,
					animationType: 'slide-in-right',
					animationDuration: 200
				});
			}
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
