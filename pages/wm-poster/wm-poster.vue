<template>
	<view>
		<view>	
			<uni-drawer ref="showLeft" mode="left" :width="320" @change="change($event,'showLeft')">
				<view class="close" v-for="(item,index) in shopsList" :key='item.id'>
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
					<text class="word-btn-white">{{shopsList[shopIndex].name}}</text>
				</view>
			</uni-section>
		</view>
		
		<view class='canvasBox'>
			<canvas :style="{ width: canvasWidth + 'px', height: canvasHeight + 'px' }" canvas-id="firstCanvas"></canvas>
		</view>
		
		<uni-section title="编辑文字" type="line">
			<view>
				<view class='iconfont icon-color func' 
						:style="{color:fontColor}"
						hover-class="word-btn--hover1"
						@click="showColors">
				</view>
				<view class='iconfont icon-FontIncrease funcSize' 
					  :style='{color:colors.increase}'
					  hover-class="word-btn--hover1"
					  @click="fontIncrease"></view>
				<view class='iconfont icon-FontDecrease funcSize' 
					  :style='{color:colors.decrease}'
					  hover-class="word-btn--hover1" 
					  @click='fontDecrease'></view>
			</view>
		</uni-section>

		<view v-if = 'colorDisplay' class='showColors'>
			<view v-for='(item,index) in fontColorsList' 
				  :key = 'item'>
				<view v-if='item==fontColor' 
					class='iconfont icon-squareSelected-s func'
					:style ='{color:item}'>
				</view>
				<view v-else class='iconfont icon-square func'
					:style ='{color:item}'
					@click="changeColor(item)">
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
				bgImgSrc:'',
				// bgImgList:[
				// 	'../../static/bgImg/bgImg1.jpg',
				// 	'../../static/bgImg/bgImg2.jpg',
				// 	'../../static/bgImg/bgImg3.jpg',
				// 	'../../static/bgImg/bgImg4.jpg'
				// ],
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
				fontColor:'#000000'
				}
			},
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
				this.createCanvas(this.fontSize);
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
			createCanvas(fontSize) {
				var that = this;
				var QrImgSrc = that.shopsList[that.shopIndex].QrSrc;
				var QrX = canvasWidth*25/18 , QrY = canvasWidth*30/19 , QrW = canvasWidth/3, QrH = canvasWidth/3;
				const canvasWidth = window.screen.availWidth - 8;
				var ctx = uni.createCanvasContext('firstCanvas',this);
				console.dir(ctx);
				ctx.setFillStyle("#ffffff");
				ctx.fillRect(0,0,canvasWidth,canvasWidth*16/9);
				var slogan = that.posterText
				ctx.setFontSize(fontSize);
				/* 绘制文字 */
				ctx.setFillStyle(that.fontColor);
				var lineWidth = canvasWidth/30;//文字初始x坐标
				var initHeight = canvasWidth*7/5 + fontSize*3/2;//文字初始y坐标
				// ctx.fillText(slogan,lineWidth,initHeight);
				var lastSubStrIndex= 0; //每次开始截取的字符串的索引
				const rightRange = (canvasWidth*17/30-lineWidth)%fontSize;
				const rightLimit = (rightRange >= 0.5*fontSize)?(canvasWidth*17/30+lineWidth):(canvasWidth*17/30+lineWidth-fontSize);
				const bottomRange = (canvasWidth*5/3)%(fontSize*3/2);
				const bottomLimit = (bottomRange >= 0.5*(fontSize*3/2))?(canvasWidth*5/3):(canvasWidth*5/3 - fontSize);
				for(let i=0;i<slogan.length;i++){ 
					if(initHeight>bottomLimit) break;
					if(lineWidth >= rightLimit){  
						ctx.fillText(slogan.substring(lastSubStrIndex,i),canvasWidth/30,initHeight);//绘制截取部分
						initHeight+= fontSize*3/2;
						lineWidth= fontSize*3/2 + ctx.measureText(slogan[i]).width;
						lastSubStrIndex=i;
					} 
					else{
						lineWidth+=ctx.measureText(slogan[i]).width; 
					}
					if(i==slogan.length-1){//绘制剩余部分
						ctx.fillText(slogan.substring(lastSubStrIndex,i+1),canvasWidth/30,initHeight);
					}
				}
				// var img;
				// new Promise(resolve => {
				// 	uni.downloadFile({
				// 	      url: that.bgImgList[0],
				// 		  success(res){
				// 			  console.log(res)
				// 		  }
				// 	   })
				// 	})
					
				ctx.drawImage(that.bgImgSrc,0,0,canvasWidth,canvasWidth*4/3);//绘制图
				// var bgImg = new Image();
				// bgImg.src = '../../static/bgImg.jpg';
				// ctx.drawImage(bgImg.src,);

				// ctx.drawImage(QrImgSrc,QrX,QrY,QrW,QrH);
				ctx.drawImage(QrImgSrc,250,500,100,100);
				// bgImg.onload = function(){
					
				// 	ctx.draw();
				// };
				ctx.draw()
/* 				        ctx.stroke()
				ctx.setStrokeStyle("#ff0000")
				ctx.setLineWidth(2)
				ctx.moveTo(160, 100)
				ctx.arc(100, 100, 60, 0, 2 * Math.PI, true)
				ctx.moveTo(140, 100)
				ctx.arc(100, 100, 40, 0, Math.PI, false)
				ctx.moveTo(85, 80)
				ctx.arc(80, 80, 5, 0, 2 * Math.PI, true)
				ctx.moveTo(125, 80)
				ctx.arc(120, 80, 5, 0, 2 * Math.PI, true)
				ctx.stroke() */
				        
/* 				var that = this;
				const canvasWidth = window.screen.availWidth - 8;
				const ctx = uni.createCanvasContext('firstCanvas');
				ctx.rect(0,0,canvasWidth,canvasWidth*16/9);
				ctx.setFillStyle('black');//设置背景色
				ctx.fill();
				console.dir(ctx,canvasWidth);
				//添加海报主体
				
				bgImg.onload = function(){
					ctx.drawImage(bgImg.src,0,0,canvasWidth*4/3,canvasWidth);
					//添加门店二维码
					var QrImg = new Image();
					
					// QrImg.src = that.shopsList[that.shopIndex].QrSrc;
					QrImg.onload = function(){
						//绘制字符串
						var slogan = that.posterText
						ctx.setFontSize(that.fontSize);
						ctx.setFillStyle('black')
						var lineWidth = canvasWidth/30;//文字初始x坐标
						var initHeight = canvasWidth*7/5;//文字初始y坐标
						var lastSubStrIndex= 0; //每次开始截取的字符串的索引
						var rightRange = (canvasWidth*17/30-lineWidth)%that.fontSize;
						var rightLimit = (rightRange >= 0.5*that.fontSize)?(canvasWidth*17/30+lineWidth-2*fontSize):(canvasWidth*17/30+lineWidth-3*that.fontSize);
						for(let i=0;i<slogan.length;i++){ 
							if(initHeight>5/3) break;
							console.log(lineWidth)
						    if(lineWidth >= rightLimit){  
						        ctx.fillText(slogan.substring(lastSubStrIndex,i),lineWidth,initHeight);//绘制截取部分
						        initHeight+= that.fontSize*3/2;
						        lineWidth= that.fontSize*3/2 + ctx.measureText(slogan[i]).width;
						        lastSubStrIndex=i;
						    } 
							else{
								lineWidth+=ctx.measureText(slogan[i]).width; 
							}
						    if(i==slogan.length-1){//绘制剩余部分
						        ctx.fillText(slogan.substring(lastSubStrIndex,i+1),lineWidth,initHeight);
						    }
						}
						ctx.drawImage(QrImg.src,canvasWidth*19/30, canvasWidth*25/18, canvasWidth/3, canvasWidth/3);
						ctx.draw();
						};
					}; */
			},
			canvasIdErrorCallback(e) {
			    console.error(e.detail.errMsg);
			},
		/* 文字输入 */
			onKeyInput(e){
		        this.posterText = e.target.value;
				this.createCanvas(this.fontSize*this.fontScale);
				console.log(e);
		    },
		/* 字体功能 */
			showColors(){//显示隐藏颜色选择区域
				this.colorDisplay = !this.colorDisplay;
			},
			changeColor(color){//选择颜色
				this.fontColor = color;
				this.createCanvas(this.fontSize);
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
				this.createCanvas(this.fontSize);
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
				this.createCanvas(this.fontSize);
			},
			/* 生成海报 */
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
			this.createCanvas(18*window.screen.availWidth/375);
		},

	}
</script>
<style>
	@import url("./wm-poster.css");
</style>