<template>
	<view>
		<u-navbar title="" >
			<view class="navbar-right" slot="right">
				<view class="message-box right-item">
					<button size="mini" class='sub-button'> 生成海报 </button>
				</view>
			</view>
		</u-navbar>
		
		<view class='canvasBox'>
			<canvas :style="{ width: canvasWidth + 'px', height: canvasHeight + 'px' }" canvas-id="firstCanvas"></canvas>
		</view >
		<view class='test'>这是占位测试</view>
	<!--底部工具栏-->
		<view class="box-2">
			<view v-if='Number(texts)!=0' class='text-history'>
				<text v-for='(item,index) of texts' class='text' @click='modify(item,index)'>
					{{item.slice(0,2)}}..
					<text class='iconfont icon-multiply' :data-index='index' @click.stop="deleteText"></text>
				</text>
			</view>
			<view class='func-area'>
				<view class='more iconfont icon-add funcbar' @click="showMore"></view>
				<view class="input-area funcbar">
					<textarea auto-height="true" class="u-search-text" :value="inputValue" placeholder="添加文字" @input="onKeyInput"/>
				</view>
				<view>
					<button v-if='modifyIndex == -1' size="mini" class='add-button' @click="confirm">添加</button>
					<button v-else size="mini" class='modify-button' @click="modifyConfirm">修改</button>
				</view>
			</view>
			<swiper v-if="isMoreVisible" class='detail-func-area' 
				:current="editIndex" 
				indicator-dots='true' 
				indicator-color='rgb(160,207,255,0.3)' 
				indicator-active-color='rgb(43,133,228,0.7)'>
				<swiper-item item-id="0" class='Qr-area'>
					<view v-for="(item,index) in editList" class = 'detail-func'>
						<view :class="item.isSelected?item.iconClassSelected:item.iconClass"
						hover-class="edit-item"
							@click="editTap(index,item.isSelected)">
						</view>
						<text class="edit-name">{{item.name}}</text>
					</view>	
				</swiper-item>
				<swiper-item item-id='1'>
					<view class='iconfont icon-multiply' @click="returnBtn"></view>
					icons
				</swiper-item>
				
				<swiper-item item-id='2' class='flex-col'>
					<view class='iconfont icon-multiply' @click="returnBtn"></view>
					bold
				</swiper-item>
				<swiper-item item-id='3' class='showColors'>
					<view class='iconfont icon-multiply' @click="returnBtn"></view>
					<view v-for='(item,index) in fontColorsList' 
						  :key = 'item'>
						<text :class="item==fontColor?'iconfont icon-squareSelected-s func':'iconfont icon-square func'"
							:style ='{color:item}'
							@click="changeColor(item)">
						</text>
					</view>
				</swiper-item>
				<swiper-item item-id='4' class='flex-col'>
					<view class='iconfont icon-multiply' @click="returnBtn"></view>
					size
				</swiper-item>
			</swiper>
		</view>
	</view>
	
</template>

<script>
	import UniSection from "@/components/uni-section/uni-section.vue"
	import colors from "@/common/fontColor.data.js";
	const app = getApp()

	export default {
		data() {
			return {
				/* 传入参数默认值，方便调试 */
				fontColorsList:colors,
				posterBg: {
					name: 'NOTE 9',
					path: '../../static/bgImg/bgImg1.jpg',
					width: 960,
					height: 1422
				},
				/* 传入参数默认值，方便调试 */
				good: {
					name: "小米10",
					key: "小米10",
					icon: "../../static/goods/xiaomiPhone/xiaomi10.jpg",
					cat: 10
				  },
				Qr: "../../static/QrImg1.png",
				editList:[{name:'二维码',id:1,iconClass:'iconfont icon-erweima edit-item',iconClassSelected:'iconfont icon-erweima edit-item-Selected',isSelected:false},
						{name:'图标',id:2,iconClass:'iconfont icon-magic-line edit-item',iconClassSelected:'iconfont icon-magic-line edit-item-Selected',isSelected:false},		
						{name:'文字',id:3,iconClass:'iconfont icon-FontIncrease edit-item',iconClassSelected:'iconfont icon-FontIncrease edit-item-Selected',isSelected:false},		
						{name:'颜色',id:4,iconClass:'iconfont icon-color edit-item',iconClassSelected:'iconfont icon-color edit-item-Selected',isSelected:false},
						{name:'更多',id:5,iconClass:'iconfont icon-edit-line edit-item',iconClassSelected:'iconfont icon-edit-line edit-item-Selected',isSelected:false}
						],
				editIndex:0,
				posterText:'绝无仅有的好旗舰',
				colors:{decrease:'#8a8a8a',increase:'#8a8a8a'},
				
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
				direction:{
					angle:0,
					scale:1,
					longitude:0,
					latitude:0				
				},
				isMoreVisible: false,
				// isColorVisible: false,
				// isQrVisible: false,
				modifyIndex: -1,
				inputValue:'',
				texts: []
			}},
		components:{
			"uni-section":UniSection
		},
		methods: {
		/* 画布 */
			createCanvas(fontSize,Direction) {
				var that = this;
				const ctx = uni.createCanvasContext('firstCanvas');
				const canvasW = this.canvasWidth;
				const canvasH = this.canvasHeight;
				const bgH = this.bgHeight;
				
				console.log(canvasW,bgH,ctx)
				ctx.setFillStyle("#ffffff");
				ctx.fillRect(0,0,canvasW,canvasH);//填充白色背景
				/* 二维码位置参数 */
				let QrW = Math.min(canvasW/4,canvasH*2/9);
				let paddingW = canvasW/6-QrW/3;
				let QrH = QrW,					
					QrX = canvasW*5/6-QrW*2/3,
					QrY = canvasH*5/6-QrW/2;
				// /* 绘制文字 */
				// let slogan = that.posterText
				// ctx.setFontSize(fontSize);
				// ctx.setFillStyle(that.fontColor);
				// let initTextX = paddingW, limitTextX = paddingW + canvasW/2;//文字初始x坐标,结束x坐标
				// let textMid = paddingW + canvasW/4;
				// let initTextY = canvasH*3/4, limitText = canvasH*11/12;//文字初始y坐标,结束y坐标
				// let lastSubStrIndex= 0; //每次开始截取的字符串的索引
				// for(let i=0;i<slogan.length;i++){ 
				// 	if(initTextY > limitText) break;
				// 	if(initTextX >= limitTextX){ 
				// 		ctx.setTextAlign('center');
				// 		ctx.setTextBaseline('middle');
				// 		ctx.fillText(slogan.substring(lastSubStrIndex,i),textMid,initTextY);//绘制截取部分
				// 		initTextY += fontSize*3/2;
				// 		initTextX = paddingW;
				// 		lastSubStrIndex=i;
				// 	} 
				// 	else{
				// 		initTextX += ctx.measureText(slogan[i]).width; 
				// 	}
				// 	if(i==slogan.length-1){//绘制剩余部分
				// 		ctx.setTextAlign('center');
				// 		ctx.setTextBaseline('middle');
				// 		ctx.fillText(slogan.substring(lastSubStrIndex,i+1),textMid,initTextY);
				// 	}
				// }
				/* 绘制二维码 */
				if(this.editList[0].isSelected){
					var QrImg = new Image();
					QrImg.src = that.Qr
					QrImg.onload = function(){//绘制二维码
						ctx.drawImage(QrImg.src,QrX,QrY,QrW,QrH);
					};
				}

				/* 绘制背景图 */
				var bgImg = new Image();
				bgImg.src = that.posterBg.path;
				bgImg.onload = function(){//绘制背景图
					ctx.drawImage(bgImg.src,0,0,canvasW,bgH);//绘制图
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
			addTextToCanvas(){
				ctx.setFontSize('18px');
				// ctx.setFillStyle(that.fontColor);
				let initTextX = 10
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
			},
			/* 功能选择 */

			editTap(index){
				this.editIndex = index;
				switch(index){
					case 0:{
						this.editList[0].isSelected = !this.editList[0].isSelected
						this.createCanvas();
						break
					}
					case 1:{
						this.isIcon()
						break
					}
					case 2:{
						break
					}
					case 3:{
						this.isBold()
						break
					}
					case 4:{
						this.isSize()
						break
					}
				}
			},
			confirm() {
				this.texts.push(this.inputValue)
				this.inputValue = ''
				this.addTextToCanvas()
			},
			onKeyInput(event) {				
			    this.inputValue = event.target.value
			},
			modify(item,index) {
				this.inputValue = item
				this.modifyIndex = index
			},
			modifyConfirm() {
				this.texts[this.modifyIndex] = this.inputValue
				this.modifyIndex = -1
				this.inputValue = ''
			},
			deleteText(e){
				this.texts.splice(e.currentTarget.dataset.index,1)
				this.modifyIndex = -1
				this.inputValue = ''
			},
			returnBtn(){
				this.editIndex = 0
				console.log(this.editIndex)
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
			showMore(){
				this.isMoreVisible = !this.isMoreVisible
			},
		/* 文字输入 */
			// onKeyInput(e){
		 //        this.posterText = e.target.value;
			// 	this.createCanvas(this.fontSize*this.fontScale,this.direction);
			// 	console.log(e);
		 //    },
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
			console.log(options)
			this.posterBg = JSON.parse(options.posterBg)
			let bgImg = new Image()
			bgImg.src = this.posterBg.path
			this.posterBg.width = bgImg.width
			this.posterBg.height = bgImg.height
			
			this.canvasWidth = app.globalData.Info.screenWidth - 8 
			let scale = this.canvasWidth / this.posterBg.width
			this.bgHeight = this.posterBg.height *scale
			this.canvasHeight = this.bgHeight + this.canvasWidth / 2;
			console.log(this.canvasWidth,this.canvasHeight)
			
			this.good = JSON.parse(options.good)
			
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
	@import "../../lib/global.scss";
	
</style>