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
		
		<view class = 'edit'>
			<view 	v-for="(item,index) in editList">
				<view :class="editIndex==index?item.iconClassSelected:item.iconClass"
					hover-class="word-btn--hover1"
					@tap="editTap(index)">
				</view>
				<text class="editName">{{item.name}}</text>
			</view>				
		</view>
		
		<view v-if = 'editIndex==2'>
			<uni-section title="编辑贴纸" type="line">
				<view>
					<view class='iconfont icon-reset func1 reset'
							hover-class="word-btn--hover1"
							@tap="stickerReset">
					</view>				
					<view class='iconfont icon-uparrow func1'
							hover-class="word-btn--hover1"
							@tap="stickerUp">
					</view>
					<view class='iconfont icon-downarrow func1'
							hover-class="word-btn--hover1"
							@tap="stickerDown">
					</view>
					<view class='iconfont icon-leftarrow func1' 
						  :style='{color:colors.increase}'
						  hover-class="word-btn--hover1"
						  @tap="stickerLeft">
					</view>
					<view class='iconfont icon-rightarrow func1' 
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
			<view class='scrollX'>
				<scroll-view class="showStickers" scroll-x="true">
					<image v-for='(item,index) in stickerList' :class="index==stickerIndex?'stickerSelected':'stickerImg'"
								  :key = 'item.id' :src="item.Src" mode='aspectFit' @tap='chooseSticker(index)'></image>
				</scroll-view>
			</view>
		</view>
		
		<view v-if = 'editIndex==0'>
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
					<view :class="item==fontColor?'iconfont icon-squareSelected-s func':'iconfont icon-square func'"
						:style ='{color:item}'
						@tap="changeColor(item)">
					</view>
				</view> 
			</view>
			<view class="uni-form-item uni-column">
				<textarea class="textArea" auto-height='true' @input="onKeyInput" placeholder="绝无仅有的好旗舰" />
			</view>
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
				bgImgSrc:'',
				showRight: false,
				showLeft: false,
				shopsList:[ {name:'小米北京1店',id:1,QrSrc:'../../static/shopQr/QrImg1.png'},
						{name:'小米上海2店',id:2,QrSrc:'../../static/shopQr/QrImg2.png'},
						{name:'小米广州3店',id:3,QrSrc:'../../static/shopQr/QrImg3.png'},
						{name:'小米深圳4店',id:4,QrSrc:'../../static/shopQr/QrImg4.jpg'},
						{name:'小米杭州5店',id:5,QrSrc:'../../static/shopQr/QrImg5.jpg'}
						],
				shopIndex:0,
				editList:[{name:'文字',id:1,iconClass:'iconfont icon-edit-line edit-item',iconClassSelected:'iconfont icon-edit-fill edit-item-Selected'},
						{name:'图片',id:2,iconClass:'iconfont icon-tupiancopy edit-item',iconClassSelected:'iconfont icon-tupian-filled edit-item-Selected'},		
						{name:'素材',id:2,iconClass:'iconfont icon-magic-line edit-item',iconClassSelected:'iconfont icon-magic-fill edit-item-Selected'},		
						{name:'图片',id:2,iconClass:'iconfont icon-price-tag--line edit-item',iconClassSelected:'iconfont icon-price-tag--fill edit-item-Selected'}		
						],
				editIndex:0,
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
				stickerList:[ {name:'Redmi Note 7',id:1,Src:'../../static/phoneStickers/phone1.png'},
						{name:'Redmi Note 8',id:2,Src:'../../static/phoneStickers/phone2.png'},
						{name:'Redmi Note 9',id:3,Src:'../../static/phoneStickers/phone3.png'},
						{name:'xiaomi K30',id:4,Src:'../../static/phoneStickers/phone4.png'},
						{name:'xiaomi K30 Pro',id:5,Src:'../../static/phoneStickers/phone5.png'}
						],
				stickerIndex:0,
				direction:{
					angle:0,
					scale:1,
					longitude:0,
					latitude:0				
				}
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
				var ctx = uni.createCanvasContext('firstCanvas',this);
				const canvasW = this.canvasWidth;
				const canvasH = this.canvasHeight;
				const bgH = this.bgHeight;
				console.log(ctx);
				ctx.setFillStyle("#ffffff");
				ctx.fillRect(0,0,canvasW,canvasH);//填充白色背景
				/* 二维码位置参数 */
				let QrW = Math.min(canvasW/4,canvasH*2/9);
				let paddingW = canvasW/6-QrW/3;
				let QrH = QrW,					
					QrX = canvasW*5/6-QrW*2/3,
					QrY = canvasH*5/6-QrW/2;
				/* 绘制文字 */
				let slogan = that.posterText
				ctx.setFontSize(fontSize);
				ctx.setFillStyle(that.fontColor);
				let initTextX = paddingW, limitTextX = paddingW + canvasW/2;//文字初始x坐标,结束x坐标
				let textMid = paddingW + canvasW/4;
				let initTextY = canvasH*3/4, limitText = canvasH*11/12;//文字初始y坐标,结束y坐标
				let lastSubStrIndex= 0; //每次开始截取的字符串的索引
				for(let i=0;i<slogan.length;i++){ 
					if(initTextY > limitText) break;
					if(initTextX >= limitTextX){ 
						ctx.setTextAlign('center');
						ctx.setTextBaseline('middle');
						ctx.fillText(slogan.substring(lastSubStrIndex,i),textMid,initTextY);//绘制截取部分
						initTextY += fontSize*3/2;
						initTextX = paddingW;
						lastSubStrIndex=i;
					} 
					else{
						initTextX += ctx.measureText(slogan[i]).width; 
					}
					if(i==slogan.length-1){//绘制剩余部分
						ctx.setTextAlign('center');
						ctx.setTextBaseline('middle');
						ctx.fillText(slogan.substring(lastSubStrIndex,i+1),textMid,initTextY);
					}
				}
				/* 绘制二维码 */
				var QrImg = new Image();
				QrImg.src = that.shopsList[that.shopIndex].QrSrc;
				QrImg.onload = function(){//绘制二维码
					ctx.drawImage(QrImg.src,QrX,QrY,QrW,QrH);
				};
				/* 绘制贴纸和背景图 */
				var phone = new Image();
				phone.src = that.stickerList[that.stickerIndex].Src;
				var bgImg = new Image();
				bgImg.src = that.bgImgSrc;
				bgImg.onload = function(){//绘制背景图
					ctx.drawImage(bgImg.src,0,0,canvasW,bgH);//绘制图
					ctx.rotate(Direction.angle);
					ctx.drawImage(phone.src,Direction.longitude,Direction.latitude,200*Direction.scale,400*Direction.scale);//绘制贴纸
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
			/* 功能选择 */
			editTap(index){
				this.editIndex = index;
			},
			/* 操作贴纸 */
			stickerReset(){
				this.direction = {
					angle:0,
					scale:1,
					longitude:0,
					latitude:0	
				};
				this.createCanvas(this.fontSize,this.direction);
			},
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
			/* 选择贴纸 */
			chooseSticker(index){
				this.stickerIndex = index;
				console.log(this.stickerIndex);
				this.createCanvas(this.fontSize,this.direction);
			},
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
			let bg = new Image()
			bg.src = options.bgImg;
			this.bgImgSrc = options.bgImg;
			this.screenWidth = window.screen.availWidth;
			this.canvasWidth = window.screen.availWidth-8;
			this.bgHeight = bg.height * this.canvasWidth / bg.width;
			this.canvasHeight = this.bgHeight *3/2;
			
			console.log(this.canvasWidth,this.bgHeight,this.canvasHeight)
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