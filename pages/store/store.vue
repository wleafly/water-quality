<template>
	<view style="display: flex;flex-wrap: wrap;padding: 10rpx;">
		<view v-for="i,index in imgName" style="width: 50%">
			<view @click="toProductList(index)"
				style="background-color: white;display: flex;flex-direction: column;align-items: center;border-radius: 10rpx;padding: 50rpx 0;margin: 10rpx;">
				<image :src="`../../static/store/${i}.png`" class="type"></image>
				<view style="color: gray;margin-top: 20rpx;">{{typeArr[index]}}</view>
			</view>
		</view>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				imgName: ['normal', 'small', 'many', 'light', 'supply', 'controller', 'supporting','expect'],
				typeArr: ['常规传感器', '经济型小体积传感器', '多参数传感器', '国标光度法', '二次供水及供电站', '控制器及软件云平台', '配套设备','敬请期待...'],

				
			};
		},
		methods: {
			toProductList(index) {
				if(index!=this.typeArr.length-1){
					uni.navigateTo({
						url: './product/product?id=' + index + '&title=' + this.typeArr[index]
					})
				}

			}
		}
	}
</script>
<style lang="scss">
	page {
		background-color: #EAEDF2;
	}

	.type {
		filter: invert(69%) sepia(99%) saturate(7486%) hue-rotate(198deg) brightness(99%) contrast(101%);

		width: 180rpx;
		height: 180rpx;
	}
</style>