<template>
	<view>
		<view>	
			<uni-drawer ref="showLeft" mode="left" :width="320" @change="change($event,'showLeft')">
				<view class="close" v-for="(item,index) in shops" :key='item.id'>
						<view class="word-btn1" 
							hover-class="word-btn--hover1" 
							:hover-start-time="20" 
							:hover-stay-time="70" 
							@click="closeDrawer(index,'showLeft')">
							<text class="word-btn-white">{{item.name}}</text>
						</view>
				</view>
			</uni-drawer>
			<uni-section title="选择二维码" type="line">
				<view class="word-btn draw-cotrol-btn" 
					hover-class="word-btn--hover" 
					:hover-start-time="20" 
					:hover-stay-time="70" 
					@click="showDrawer('showLeft')">
					<text class="word-btn-white">{{shops[shopIndex].name}}</text>
				</view>
			</uni-section>
		</view>
		
		<view class='canvasBox'>
			<canvas style="width: 367px; height: 800px;" canvas-id="firstCanvas"></canvas>
		</view>
		
		<uni-section title="编辑文字" type="line">
			<view>
				<text class='iconfont icon-fontbig func' 
					  :style='{color:colors.increase}'
					  @click="fontIncrease"></text>
				<text class='iconfont icon-fontsmall func' 
					  :style='{color:colors.decrease}'
					  @click='fontDecrease'></text>
			</view>
		</uni-section>
		<view class="uni-form-item uni-column">
		    <textarea class="textArea" auto-height='true' @input="onKeyInput" placeholder="绝无仅有的好旗舰" />
		</view>
		<view class="comBtn"
			  hover-class="word-btn--hover" 
			  @click="posterComplete">生成海报
		</view>
	</view>
</template>

<script>
	import uniDrawer from "@/components/uni-drawer/uni-drawer"
	import UniSection from "@/components/uni-section/uni-section.vue"
	export default {
		data() {
			return {
				showRight: false,
				showLeft: false,
				shops:[ {name:'小米北京1店',id:1,QrSrc:'../../static/shopQr/QrImg1.png'},
						{name:'小米上海2店',id:2,QrSrc:'../../static/shopQr/QrImg2.png'},
						{name:'小米广州3店',id:3,QrSrc:'../../static/shopQr/QrImg3.png'},
						{name:'小米深圳4店',id:4,QrSrc:'../../static/shopQr/QrImg4.jpg'},
						{name:'小米杭州5店',id:5,QrSrc:'../../static/shopQr/QrImg5.jpg'}
						],
				shopIndex:0,
				posterText:'绝无仅有的好旗舰',
				fontSize:18,
				colors:{decrease:'#8a8a8a',increase:'#8a8a8a'},
				}
			},
		components:{
			"uni-drawer":uniDrawer,
			"uni-section":UniSection
		},
		methods: {
		//抽屉
			confirm() {},
			// 打开窗口
			showDrawer(e) {
				this.$refs[e].open()
			},
			// 关闭窗口/选中门店
			closeDrawer(index,e) {
				this.shopIndex = index;
				this.$refs[e].close();
				console.log(index,e,this.shopIndex);
				this.createCanvas();
			},
			// 抽屉状态发生变化触发
			change(e, type) {
				console.log((type === 'showLeft' ? '左窗口' : '右窗口') + (e ? '打开' : '关闭'));
				this[type] = e
			},
		//画布
			createCanvas() {
				var that = this;
				const ctx = uni.createCanvasContext('firstCanvas');
				console.log(ctx);
				ctx.fillStyle="#FFFFFF";//设置背景色
				ctx.fillRect(0,0,367,800);
				//添加海报主体
				var bgImg = new Image();
				bgImg.src = '../../static/bgImg.jpg'
				// uni.downloadFile({
				// 	url:bgImgSrc,
				// 	complete:(res)=>{console.log(res)}
				// })
				bgImg.onload = function(){
					ctx.drawImage(bgImg.src,0,0,367,650);
					//添加门店二维码
					var QrImg = new Image();
					QrImg.src = that.shops[that.shopIndex].QrSrc;
					console.log('that',that.shopIndex);
					QrImg.onload = function(){
						//绘制字符串
						var slogan = that.posterText
						ctx.setFontSize(that.fontSize);
						ctx.setFillStyle('black')
						var lineWidth = 26;//初始x坐标
						var initHeight = 695;//初始y坐标
						var lastSubStrIndex= 0; //每次开始截取的字符串的索引
						var rightRange = (230-lineWidth)%that.fontSize
						var rightLimit = (rightRange >= 0.5*that.fontSize)?(230+lineWidth-2*that.fontSize):(230+lineWidth-3*that.fontSize)
						for(let i=0;i<slogan.length;i++){ 
							if(initHeight>790) break;
							console.log(lineWidth)
						    if(lineWidth >= rightLimit){  
						        ctx.fillText(slogan.substring(lastSubStrIndex,i),26,initHeight);//绘制截取部分
						        initHeight+= 1.5 * that.fontSize;
						        lineWidth= 1.5 * that.fontSize + ctx.measureText(slogan[i]).width;
						        lastSubStrIndex=i;
						    } 
							else{
								lineWidth+=ctx.measureText(slogan[i]).width; 
							}
						    if(i==slogan.length-1){//绘制剩余部分
						        ctx.fillText(slogan.substring(lastSubStrIndex,i+1),26,initHeight);
						    }
						}
						ctx.drawImage(QrImg.src,250,675,100,100);
						ctx.draw();
						};
					};
					// QrImg.onload = function(){
					// 	ctx.drawImage(QrImg.src,25,675,100,100);
					// 	ctx.draw();
					// 	};
				// ctx.setFontSize(20)
				// ctx.setFillStyle('black')
				// ctx.fillText('这是一段自定义文字',25,675)
		//      // Draw coordinates
		//    ctx.draw();
			},
			canvasIdErrorCallback(e) {
			    console.error(e.detail.errMsg)
			},
		//文字输入
			onKeyInput(e){
		        this.posterText = event.target.value;
				this.createCanvas();
				console.log(e);
		    },
		//字体功能
			fontIncrease(){
				if(this.fontSize<=22){
					this.fontSize += 2;
					this.colors.decrease = '#8a8a8a';
				}
				else{
					this.colors.increase = '#cdcdcd';
					uni.showToast({
						title:'文字已达到最大',
						icon:'none'
					})
				}
				console.log(this.fontSize);
				this.createCanvas();
			},
			fontDecrease(){
				if(this.fontSize>=14){
					this.fontSize = this.fontSize - 2;
					this.colors.increase = '#8a8a8a';
				}
				else{
					this.colors.decrease = '#cdcdcd';
					uni.showToast({
						title:'文字已达到最小',
						icon:'none'
					})
				}
				console.log(this.fontSize);
				this.createCanvas();
			},
			posterComplete(){
				uni.canvasToTempFilePath({
				  canvasId: 'firstCanvas',
				  success: function(res) {
				    console.log(res)
					uni.saveImageToPhotosAlbum({
					    filePath: res.tempFilePath,
					    complete: function (e) {
					         console.log('save success',e);
					        }
					    });
				    } 
				})
			}
		},
		onNavigationBarButtonTap(e) {
			if (this.showLeft) {
				this.$refs.showLeft.close()
			} else {
				this.$refs.showLeft.open()
			}
		},
		// app端拦截返回事件 ，仅app端生效
		onBackPress() {
			if (this.showRight || this.showLeft) {
				this.$refs.showLeft.close()
				this.$refs.showRight.close()
				return true
			}
		},
		
		//海报主体
	    onReady: function(){
			this.createCanvas();
		},

	}
</script>

<style>
	@charset "UTF-8";
	/* 头条小程序组件内不能引入字体 */
	/* #ifdef MP-TOUTIAO */
	@font-face {
		font-family: uniicons;
		font-weight: normal;
		font-style: normal;
		/* src: url("~@/static/uni.ttf") format("truetype"); */
	}

	/* #endif */
	page {
		display: flex;
		flex-direction: column;
		box-sizing: border-box;
		background-color: #eeeeee;
		min-height: 100%;
		height: auto;
		padding-left: 4px;
		padding-right: 4px;
	}
	
	
	.word-btn {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		align-items: center;
		justify-content: center;
		border-radius: 6px;
		height: 36px;
		/* margin: 15px; */
		background-color: #eeeeee;
	}
	
	.word-btn--hover {
		background-color: #bbbbbb;
	}
	
	.word-btn1 {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		align-items: center;
		justify-content: center;
		border-radius: 6px;
		height: 36px;
		/* margin: 15px; */
		background-color: #f3f3f3;
	}
	
	.word-btn--hover1 {
		background-color: #cccccc;
	}
	
	.header {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		padding: 10px 15px;
		align-items: center;
		border-top-width: 1px;
		border-top-color: #f5f5f5;
		border-top-style: solid;
		background-color: #ffffff;
	}
	
	.input-view {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		align-items: center;
		flex-direction: row;
		background-color: #e7e7e7;
		height: 30px;
		border-radius: 15px;
		padding: 0 10px;
		flex: 1;
		background-color: #f5f5f5;
	}
	
	.uni-drawer-info {
		background-color: #ffffff;
		padding: 15px;
		padding-top: 5px;
		color: #3b4144;
	}
	
	.uni-padding-wrap {
		padding: 0 15px;
		line-height: 1.8;
	}
	
	.input {
		flex: 1;
		padding: 0 5px;
		height: 24px;
		line-height: 24px;
		font-size: 14px;
		background-color: transparent;
	}
	
	.close {
		padding: 8px;
	}
	
	.draw-cotrol-btn {
		flex: 1;
	}
	.scroll-view {
		/* #ifndef APP-NVUE */
		width: 100%;
		height: 100%;
		/* #endif */
		flex: 1;
	}
	
	.scroll-view-box {
		flex: 1;
		position: absolute;
		top: 0;
		right: 0;
		bottom: 0;
		left: 0;
	}
	
	.info-content {
		padding: 5px 15px;
	}
	
	.canvasBox{
		/* margin:4px; */
	}
	
	.textArea{
		padding:10px;
		height:36px;
	}
	
	.func{
		font-size:20px;
		margin-right:8px;
		/* color: #8a8a8a; */
	}
	
	.comBtn{
		background-color: #ffffff;
		margin:8px 0;
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		align-items: center;
		justify-content: center;
		height: 36px;
			/* margin: 15px; */
		font-size: 18px;
		letter-spacing:4px;
	}
</style>