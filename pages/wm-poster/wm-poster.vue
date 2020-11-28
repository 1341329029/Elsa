<template>
	<view>
		<view>	
			<uni-drawer ref="showLeft" mode="left" :width="320" @change="change($event,'showLeft')">
				<view class="close" v-for="(item,index) in shopsList" :key='item.id'>
						<view class="word-btn1" 
							hover-class="word-btn--hover1" 
							:hover-start-time="20" 
							:hover-stay-time="70" 
							@tap="closeDrawer(index,'showLeft')">
							<text class="word-btn-white">{{item.name}}</text>
						</view>
				</view>
			</uni-drawer>
			<uni-section title="选择二维码" type="line">
				<view class="word-btn draw-cotrol-btn" 
					hover-class="word-btn--hover" 
					:hover-start-time="20" 
					:hover-stay-time="70" 
					@tap="showDrawer('showLeft')">
					<text class="word-btn-white">{{shopsList[shopIndex].name}}</text>
				</view>
			</uni-section>
		</view>
		
		<view class='canvasBox'>
			<canvas :style="{ width: canvasWidth + 'px', height: canvasHeight + 'px' }" canvas-id="firstCanvas"></canvas>
		</view>
		<uni-section title="编辑贴纸" type="line">
			<view>
				<view class='iconfont icon-arrowup func1'
						hover-class="word-btn--hover1"
						@tap="stickerUp">
				</view>
				<view class='iconfont icon-arrowdown func1'
						hover-class="word-btn--hover1"
						@tap="stickerDown">
				</view>
				<view class='iconfont icon-arrowleft func1' 
					  :style='{color:colors.increase}'
					  hover-class="word-btn--hover1"
					  @tap="stickerLeft">
				</view>
				<view class='iconfont icon-arrowright func1' 
					  :style='{color:colors.decrease}'
					  hover-class="word-btn--hover1" 
					  @tap='stickerRight'>
				</view>
				<view class='iconfont icon-rotate func1' 
						hover-class="word-btn--hover1"
						@tap="stickerRotate">
				</view>
				<view class='iconfont icon-enlarge func1' 
					  :style='{color:colors.increase}'
					  hover-class="word-btn--hover1"
					  @tap="stickerEnlarge">
				</view>
				<view class='iconfont icon-narrow func1' 
					  :style='{color:colors.decrease}'
					  hover-class="word-btn--hover1" 
					  @tap='stickerNarrow'>
				</view>
			</view>
		</uni-section>
<!-- 		<view class='showColors'>
			<view v-for='(item,index) in stickerList' 
				  :key = 'item'>
				<image class='func'
				:src='item.Src'>
				</image>
			</view> 
		</view> -->
		<!-- <view @touchstart="touchStart($event, 'plank')"
			@touchmove="touchMove" 
			@touchend="touchEnd" 
			@touchcancel="touchCancel"  
			class="plank">
			<view class="frame" @touchstart="touchStart($event, 'frame')" @touchstart.stop.prevent="touchHandle" 
				:style="{left: sticker.left + 'px', top: sticker.top + 'px', width: sticker.width + 'px', height: sticker.height + 'px'}">
	
				<view class="rect"></view>
				<view @touchstart="touchStart($event, 'left')" 
					  @touchstart.stop.prevent="touchHandle" 
					  class="frame-left">
				</view>
				<view @touchstart="touchStart($event, 'right')" @touchstart.stop.prevent="touchHandle" class="frame-right"></view>
				<view @touchstart="touchStart($event, 'top')" @touchstart.stop.prevent="touchHandle" class="frame-top"></view>
				<view @touchstart="touchStart($event, 'bottom')" @touchstart.stop.prevent="touchHandle" class="frame-bottom"></view>
				<view @touchstart="touchStart($event, 'left-top')" @touchstart.stop.prevent="touchHandle" class="frame-left-top"></view>
				<view @touchstart="touchStart($event, 'left-bottom')" @touchstart.stop.prevent="touchHandle" class="frame-left-bottom"></view>
				<view @touchstart="touchStart($event, 'right-top')" @touchstart.stop.prevent="touchHandle" class="frame-right-top"></view>
				<view @touchstart="touchStart($event, 'right-bottom')" @touchstart.stop.prevent="touchHandle" class="frame-right-bottom"></view>
			</view>
		</view> -->
		
		<uni-section title="编辑文字" type="line">
			<view>
				<view class='iconfont icon-color func' 
						:style="{color:fontColor}"
						hover-class="word-btn--hover1"
						@tap="showColors">
				</view>
				<view class='iconfont icon-FontIncrease funcSize' 
					  :style='{color:colors.increase}'
					  hover-class="word-btn--hover1"
					  @tap="fontIncrease"></view>
				<view class='iconfont icon-FontDecrease funcSize' 
					  :style='{color:colors.decrease}'
					  hover-class="word-btn--hover1" 
					  @tap='fontDecrease'></view>
			</view>
		</uni-section>
		<view v-if='colorDisplay' class='showColors'>
			<view v-for='(item,index) in fontColorsList' 
				  :key = 'item'>
				<view v-if='item==fontColor' 
					class='iconfont icon-squareSelected-s func'
					:style ='{color:item}'>
				</view>
				<view v-else class='iconfont icon-square func'
					:style ='{color:item}'
					@tap="changeColor(item)">
				</view>
			</view> 
		</view>

		<view class="uni-form-item uni-column">
		    <textarea class="textArea" auto-height='true' @input="onKeyInput" placeholder="绝无仅有的好旗舰" />
		</view>
		<view class="comBtn"
			  hover-class="word-btn--hover"
			  :hover-start-time="20" 
			  :hover-stay-time="70" 
			  @tap="posterComplete">生成海报
		</view>
		<view v-show='poster'>
			<image src='this.posterSrc'></image>
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
				bgImgSrc:'',
				shopsList:[ {name:'小米北京1店',id:1,QrSrc:'../../static/shopQr/QrImg1.png'},
						{name:'小米上海2店',id:2,QrSrc:'../../static/shopQr/QrImg2.png'},
						{name:'小米广州3店',id:3,QrSrc:'../../static/shopQr/QrImg3.png'},
						{name:'小米深圳4店',id:4,QrSrc:'../../static/shopQr/QrImg4.jpg'},
						{name:'小米杭州5店',id:5,QrSrc:'../../static/shopQr/QrImg5.jpg'}
						],
				shopIndex:0,
				// screenWidth:375,
				// fontSize:18*screenWidth/375,
				posterText:'绝无仅有的好旗舰',
				colors:{decrease:'#8a8a8a',increase:'#8a8a8a'},
				fontColorsList:['#f58f98','#f15b6c','#ef5b9c','#d71345','#7a1723',
							'#faa755','#f15a22','#c85d44','#b7704f','#5f3c23',
							'#ffe600','#ffc20e','#fcaf17','#dea32c','#c37e00',
							'#abc88b','#bed742','#7fb80e','#007947','#005831',
							'#90d7ec','#33a3dc','#009ad6','#426ab3','#102b6a',
							'#9b95c9','#694d9f','#77787b','#464547','#000000'
							],
				colorDisplay:false,
				fontColor:'#000000',
				blob:true,
				poster:false,
				posterSrc:'',
				sticker: {
					left: 50,
					top: 50,
					width: this.width,
					height: this.height
				},
				stickerList:[ {id:1,Src:'../../static/phoneStickers/phone1.png'},
						{id:2,Src:'../../static/phoneStickers/phone2.png'},
						{id:3,Src:'../../static/phoneStickers/phone3.png'},
						{id:4,Src:'../../static/phoneStickers/phone4.png'},
						{id:5,Src:'../../static/phoneStickers/phone5.png'}
						],
				direction:{
					angle:0,
					scale:1,
					longitude:0,
					latitude:0				
				}
/* 				startSticker: {
					left: 0,
					top: 0,
					width: 0,
					height: 0
				},
				touches:[] */
			}},
		components:{
			"uni-drawer":uniDrawer,
			"uni-section":UniSection
		},
		methods: {
		/* 抽屉 选择二维码*/
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
				this.createCanvas(this.fontSize,this.direction);
			},
			// 抽屉状态发生变化触发
			change(e, type) {
				console.log((type === 'showLeft' ? '左窗口' : '右窗口') + (e ? '打开' : '关闭'));
				this[type] = e;
			},
			onNavigationBarButtonTap(e) {
				if (this.showLeft) {
					this.$refs.showLeft.close()
				} else {
					this.$refs.showLeft.open()
				}
			},
			//app端拦截返回事件 ，仅app端生效
			onBackPress() {
				if (this.showRight || this.showLeft) {
					this.$refs.showLeft.close()
					this.$refs.showRight.close()
					return true
				}
			},
		/* 画布 */
			createCanvas(fontSize,Direction) {
				var that = this;
				const canvasWidth = window.screen.availWidth - 8;
				var ctx = uni.createCanvasContext('firstCanvas',this);
				console.log(ctx);
				ctx.setFillStyle("#ffffff");
				ctx.fillRect(0,0,canvasWidth,canvasWidth*16/9);
				var slogan = that.posterText
				ctx.setFontSize(fontSize);
				/* 绘制文字 */
				ctx.setFillStyle(that.fontColor);
				var lineWidth = canvasWidth/30;//文字初始x坐标
				var initHeight = canvasWidth*7/5 + fontSize*3/2;//文字初始y坐标
				// ctx.fillText(slogan,lineWidth,initHeight);
				let lastSubStrIndex= 0; //每次开始截取的字符串的索引
				const rightRange = (canvasWidth*17/30-lineWidth)%fontSize;
				const rightLimit = (rightRange >= 0.5*fontSize)?(canvasWidth*17/30+lineWidth):(canvasWidth*17/30+lineWidth-fontSize);
				const bottomRange = (canvasWidth*5/3)%(fontSize*3/2);
				const bottomLimit = (bottomRange >= 0.5*(fontSize*3/2))?(canvasWidth*5/3):(canvasWidth*5/3 - fontSize);
				for(let i=0;i<slogan.length;i++){ 
					if(initHeight > bottomLimit) break;
					if(lineWidth >= rightLimit){ 
						ctx.setTextAlign('center');
						ctx.fillText(slogan.substring(lastSubStrIndex,i),canvasWidth/60+rightLimit/2,initHeight);//绘制截取部分
						initHeight += fontSize*3/2;
						lineWidth= fontSize*3/2 + ctx.measureText(slogan[i]).width;
						lastSubStrIndex=i;
					} 
					else{
						lineWidth += ctx.measureText(slogan[i]).width; 
					}
					if(i==slogan.length-1){//绘制剩余部分
						ctx.setTextAlign('center');
						ctx.fillText(slogan.substring(lastSubStrIndex,i+1),canvasWidth/60+rightLimit/2,initHeight);
					}
				}
				var QrX = canvasWidth*19/30, QrY = canvasWidth*25/18 , QrW = canvasWidth/3, QrH = canvasWidth/3;
				var QrImg = new Image();
				QrImg.src = that.shopsList[that.shopIndex].QrSrc;
				QrImg.onload = function(){//绘制二维码
					ctx.drawImage(QrImg.src,QrX,QrY,QrW,QrH);
				};
				
				var phone = new Image();
				phone.src = '../../static/phoneStickers/phone1.png';
				var bgImg = new Image();
				bgImg.src = that.bgImgSrc;
				bgImg.onload = function(){//绘制背景图
					ctx.drawImage(bgImg.src,0,0,canvasWidth,canvasWidth*4/3);//绘制图
					// ctx.translate(100,100)
					ctx.rotate(Direction.angle);
					ctx.drawImage(phone.src,Direction.longitude,Direction.latitude,200*Direction.scale,400*Direction.scale);
					Direction.scale
/* 					ctx.beginPath();
					ctx.setLineDash([40, 20]);//设置虚线间隔
					ctx.setLineWidth(3);//设置虚线宽度
					ctx.setStrokeStyle("#ffffff")//设置虚线颜色
					ctx.setLineCap('round');//设置虚线端点样式
					ctx.rect(0,0,200,400);
					ctx.stroke(); */
				};
				setTimeout(function() {
					ctx.draw()}, 200);
			},
			/* 操作贴纸 */
			stickerUp(){
				this.direction.latitude += 50;
				this.createCanvas(this.fontSize,this.direction);
			},
			stickerDown(){
				this.direction.latitude = this.direction.latitude - 50;
				this.createCanvas(this.fontSize,this.direction);
			},
			stickerLeft(){
				this.direction.longitude = this.direction.longitude - 50;
				this.createCanvas(this.fontSize,this.direction);
			},
			stickerRight(){
				this.direction.longitude += 50;
				this.createCanvas(this.fontSize,this.direction);
			},
			stickerRotate(){
				this.direction.angle += 15 * Math.PI / 180;
				this.createCanvas(this.fontSize,this.direction);
			},
			stickerEnlarge(){
				this.direction.scale += 0.2;
				this.createCanvas(this.fontSize,this.direction);
			},
			stickerNarrow(){
				this.direction.scale = this.direction.scale - 0.2;
				this.createCanvas(this.fontSize,this.direction);
			},
			/* touchHandle() {},
			touchStart(ev, type) {
				console.log(ev)
				// this.stopTime();
				// this.mask.show = false;
				if (this.touches.length == 0) {
					this.type = type;
					this.startSticker.left = this.sticker.left;
					this.startSticker.top = this.sticker.top;
					this.startSticker.width = this.sticker.width;
					this.startSticker.height = this.sticker.height;
					// this.start.image.left = this.image.left;
					// this.start.image.top = this.image.top;
					// this.start.image.width = this.image.width;
					// this.start.image.height = this.image.height;
				}
				var touches = ev.changedTouches;
				for(var i = 0; i < touches.length; i++) {
					var touch = touches[i];
					// this.touches[touch.identifier] = touch;
					this.touches.push(touch);
				}
			},
			touchMove(ev) {
				console.log(ev)
				// this.stopTime();
				ev.preventDefault();//阻止触摸的默认行为
				var touches = ev.touches;
				if (this.touches.length == 1) {
					if (this.type == "plank" || this.type == "frame" || this.fixed) {
						// this.moveImage(this.touches[0], touches[0]);
					} else {
						this.scaleFrame(this.touches[0], touches[0], this.type);
					}
				} else if (this.touches.length == 2 && touches.length == 2) {
					var ta = this.touches[0];
					var tb = this.touches[1];
					var tc = touches[0];
					var td = touches[1];
					if (ta.identifier != tc.identifier) {
						var temp = tc;
						tc = td;
						td = temp;
					}
					this.scaleImage(ta, tb, tc, td);
				}
			},
			touchEnd(ev) {
				this.type = "";
				this.touches = [];
				// this.startTime();
			},
			touchCancel(ev) {
				this.type = "";
				this.touches = [];
				// this.startTime();
			},
			
			scaleFrame(ta, tb, type) {
				var ax = tb.clientX - ta.clientX;
				var ay = tb.clientY - ta.clientY;
				var x1 = this.startSticker.left;
				var y1 = this.startSticker.top;
				var x2 = this.startSticker.left + this.startSticker.width;
				var y2 = this.startSticker.top + this.startSticker.height;
				if (type == "left") {
					x1 += ax;
				} else if (type == "right") {
					x2 += ax;
				} else if (type == "top") {
					y1 += ay;
				} else if (type == "bottom") {
					y2 += ay;
				} else if (type == "left-top") {
					x1 += ax;
					y1 += ay;
				} else if (type == "left-bottom") {
					x1 += ax;
					y2 += ay;
				} else if (type == "right-top") {
					x2 += ax;
					y1 += ay;
				} else if (type == "right-bottom") {
					x2 += ax;
					y2 += ay;
				}
				this.sticker.left = x1;
				this.sticker.top = y1;
				this.sticker.width = x2 - x1;
				this.sticker.height = y2 - y1;
			}, */
			/* 生成海报 */
			posterComplete(){//无法获取到画布，点击事件超时
				uni.canvasToTempFilePath({
				  canvasId: 'firstCanvas',
				  success: function(res) {
				    console.log(res)
					this.posterSrc = res.tempFilePath
					// #ifdef H5
					if (this.blob) {
						var path = this.parseBlob(path);
						console.log(path)
					}
					this.poster = true;
					// #endif
					// #ifndef H5
					uni.showToast({
						title:'已生成海报'
					})
					uni.saveImageToPhotosAlbum({
					    filePath: res.tempFilePath,
					    complete: function (e) {
					         console.log('save success',e);
					        }
					    });
					// #endif

				    } 
				})
			},
			parseBlob(base64) {
				var arr = base64.split(',');
				var mime = arr[0].match(/:(.*?);/)[1];
				var bstr = atob(arr[1]);
				var n = bstr.length;
				var u8arr = new Uint8Array(n);
				for(var i = 0; i < n; i++) {
					u8arr[i] = bstr.charCodeAt(i);
				}
				var url = URL || webkitURL;
				// console.log(url.createObjectURL(new Blob([u8arr], {type: mime})));
				return url.createObjectURL(new Blob([u8arr], {type: mime}));
			},
			canvasIdErrorCallback(e) {
			    console.error(e.detail.errMsg);
			},
		/* 文字输入 */
			onKeyInput(e){
		        this.posterText = e.target.value;
				this.createCanvas(this.fontSize*this.fontScale,this.direction);
				console.log(e);
		    },
		/* 字体功能 */
			showColors(){//显示隐藏颜色选择区域
				this.colorDisplay = !this.colorDisplay;
			},
			changeColor(color){//选择颜色
				this.fontColor = color;
				this.createCanvas(this.fontSize,this.direction);
			},
			fontIncrease(){//字体增大
				if(this.fontSize<=22*this.fontScale){
					this.fontSize += 2*this.fontScale;
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
				this.createCanvas(this.fontSize,this.direction);
			},
			fontDecrease(){//字体减小
				if(this.fontSize>=14*this.fontScale){
					this.fontSize = this.fontSize - 2*this.fontScale;
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
				this.createCanvas(this.fontSize,this.direction);
			},
			mounted() {
				//#ifdef H5
				this.$el.addEventListener("touchmove", (ev) => {
					ev.preventDefault();
				});
				// #endif
			}
		},
		onLoad: function(options){
			console.log(options.bgImg)
			this.bgImgSrc = options.bgImg;
			this.canvasWidth = window.screen.availWidth-8;
			this.canvasHeight = (window.screen.availWidth-8)*16/9;
			this.fontSize = 18*window.screen.availWidth/375;
			this.fontScale = window.screen.availWidth/375
		},
		/* 海报主体 */
	    onReady: function(){
			this.createCanvas(18*window.screen.availWidth/375,this.direction);
		},

	}
</script>
<style>
	@import url("./wm-poster.css");
/* 	.plank {
		position: absolute;
		left: 0upx;
		right: 0upx;
		top: 0upx;
		bottom: 0upx;
	}
	.image {
		position: absolute;
	}
	.frame {
		position: absolute;
	}
	.canvas {
		position: absolute;
		display: block;
		left: 0px;
		top: 0px;
	}
	.rect {
		position: absolute;
		left: -2px;
		top: -2px;
		width: 100%;
		height: 100%;
		border: 2px solid white;
	}
	.frame-left {
		position: absolute;
		height: 100%;
		width: 8px;
		left: -4px;
		top: 0;
	}
	.frame-right {
		position: absolute;
		height: 100%;
		width: 8px;
		right: -4px;
		top: 0;
	}
	.frame-top {
		position: absolute;
		width: 100%;
		height: 8px;
		top: -4px;
		left: 0;
	}
	.frame-bottom {
		position: absolute;
		width: 100%;
		height: 8px;
		bottom: -4px;
		left: 0;
	}
	.frame-left-top {
		position: absolute;
		width: 20px;
		height: 20px;
		left: -6px;
		top: -6px;
		border-left: 4px solid #007AFF;
		border-top: 4px solid #007AFF;
	}
	.frame-left-bottom {
		position: absolute;
		width: 20px;
		height: 20px;
		left: -6px;
		bottom: -6px;
		border-left: 4px solid #007AFF;
		border-bottom: 4px solid #007AFF;
	}
	.frame-right-top {
		position: absolute;
		width: 20px;
		height: 20px;
		right: -6px;
		top: -6px;
		border-right: 4px solid #007AFF;
		border-top: 4px solid #007AFF;
	}
	.frame-right-bottom {
		position: absolute;
		width: 20px;
		height: 20px;
		right: -6px;
		bottom: -6px;
		border-right: 4px solid #007AFF;
		border-bottom: 4px solid #007AFF;
	} */
</style>