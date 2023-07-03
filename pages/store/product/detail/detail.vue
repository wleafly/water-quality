<template>
	<view style="height: 100%;">
		<view style="height: 95%;">
			<web-view  style="height: 95%;" :src="prefixUrl+(typeId+1)+'/'+urlArr[typeId][productId]+'.html'"></web-view>
		</view>
		<navigator url="taobao/taobao" style="height: 5%;display: flex;align-items: center;justify-content:center;background-color: #FE792D;color: white;font-weight: bold;">
			进入店铺
		</navigator>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				typeId:null,
				productId:null,
				urlArr:[
					[57,41,56,42,55,48,58,33,54,349,46,51,43,49,353,352,351,350,310,309,53,52,50,47,45,44,40,32,31],
					[82,81,80,79,78,77,76],
					[74,73],
					[68,70,69,304,305,302,293,292,72,71],
					[354,66,65,64,63,62],
					[298,297,59],
					[319]
				],
				prefixUrl:'http://www.chemins-tech.com/cp'
			}
		},
		onLoad(option){
			console.log('执行了onload')
			console.log(option)
			this.typeId = option.typeId
			this.productId = option.productId
			
			// #ifdef APP-PLUS
			var height = 0; //定义动态的高度变量，如高度为定值，可以直接写
			var top = 0
			setTimeout(()=>{
				uni.getSystemInfo({
					//成功获取的回调函数，返回值为系统信息
					success: (sysinfo) => {
						height = sysinfo.windowHeight*0.95; 
						top = sysinfo.statusBarHeight + 45 //statusBarHeight是状态栏高度，45是顶部导航栏固定的高度
					}
				});
				var currentWebview = this.$scope.$getAppWebview(); //获取当前web-view
				var wv = currentWebview.children()[0];
				wv.setStyle({ //设置web-view距离顶部的距离以及自己的高度，单位为px
					top:top, //此处是距离顶部的高度，应该是你页面的头部
					height:  height , //webview的高度
					scalable: false, //webview的页面是否可以缩放
				})
				
			},300)


			// #endif
		},
		
	}
</script>

<style>
	page{
		height: 100%;
	}
</style>
