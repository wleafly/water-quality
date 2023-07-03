<template>
	<view style="padding: 20rpx;">

		<view class="shadow" style="margin-bottom: 20rpx;">
			<view style="color: #105AA9;margin-bottom: 20rpx;display: flex;align-items: center;font-weight: bold">
				<image src="../../../../static/notify_set_list.png" style="width: 40rpx;height: 40rpx;"></image>
				报警方案</view>
				<view class="select_view">
					<view class="select_title">设备筛选</view>
					<picker class="select_picker" @change="">
						<text>请选择设备</text>
						<uni-icons type="bottom" size="16"></uni-icons>
					</picker>
				</view>
				<scroll-view scroll-y style="max-height: 920rpx;">
					<uni-table :border="true" stripe>
						<uni-tr>
							<uni-th
								v-for="item in ['监测项','触发条件']">{{item}}</uni-th>
						</uni-tr>
						<uni-tr v-for="data in 6">
							<uni-th v-for="i in ['氨氮','x<30,x>60']">{{i}}</uni-th>
							
						</uni-tr>
					</uni-table>
				</scroll-view>
		</view>
		
		<view class="shadow">
			<view style="color: #105AA9;margin-bottom: 20rpx;display: flex;align-items: center;font-weight: bold">
				<image src="../../../../static/edit.png" style="width: 40rpx;height: 40rpx;"></image>
				范围设置</view>
			<view class="select_view">
				<view class="select_title">设备编号</view>
				
					<text>未选择</text>
		
			</view>
			<view class="select_view">
				<view class="select_title">指标筛选</view>
				<picker class="select_picker" @change="">
					<text>请选择参数</text>
					<uni-icons type="bottom" size="16"></uni-icons>
				</picker>
			</view>
			<view class="select_view">
				<view class="select_title">合法值上限</view>
				<input type="text" value="15" style="border: 1rpx solid #ddd;border-radius: 5rpx;padding: 0 10rpx;width: 100rpx;"/>
				<text style="color: gray;margin-left: 10rpx;">mg/L</text>
			</view>
			<view class="select_view">
				<view class="select_title">合法值下限</view>
				<input type="text" value="5" style="border: 1rpx solid #ddd;border-radius: 5rpx;padding: 0 10rpx;width: 100rpx;"/>
				<text style="color: gray;margin-left: 10rpx;">mg/L</text>
			</view>
		
			<view style="display: flex;justify-content: space-between;">
				<button style="background-color: darkgray;color: white;width: 48%;">重置</button>
				<button style="background-color: #69b4ff;color: white;width: 48%;">更新</button>
			</view>
			
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				deviceArr:getApp().globalData.deviceArr,
			}
		},
		methods: {
			
		}
	}
</script>

<style>

</style>
