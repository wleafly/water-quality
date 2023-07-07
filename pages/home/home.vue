<template>
	<view style="padding: 20rpx;">
		<view
			style="margin-bottom: 20rpx;background-color: white;border-radius: 10rpx;display: flex;justify-content: center;padding: 20rpx 0;">
			<view class="count_item" style="color: royalblue;">
				<view style="font-size: 50rpx;">{{deviceArr.length}}</view>
				<view style="font-size: 25rpx;">总设备</view>
			</view>
			<view class="count_item"
				style="color: limegreen;border-right: 1rpx solid gray;border-left: 1rpx solid gray;">
				<view style="font-size: 50rpx;">{{onlineArr.length}}</view>
				<view style="font-size: 25rpx;">在线</view>
			</view>
			<view class="count_item" style="color: gray;">
				<view style="font-size: 50rpx;">{{offlineArr.length}}</view>
				<view style="font-size: 25rpx;">离线</view>
			</view>
		</view>

<!-- 		<view style="background-color: white;border-radius: 10rpx;padding: 20rpx;margin-bottom: 20rpx;">
			<view style="display: flex;justify-content: space-between;margin-bottom: 20rpx;">
				<view style="display: flex;align-items: center;">
					<image src="../../static/ic_historical_data.png"
						style="width: 50rpx;height: 50rpx;margin-right: 20rpx;"></image>
					<text style="font-size: 32rpx;color: steelblue;">数据分布</text>
				</view>
				<picker style="display: flex;align-items: center;" :range="deviceArr.map((device)=>(device.devSerialNumber))">
					<text style="color: gray;" >2148</text>
					<image src="../../static/arrow_down.png" style="width: 30rpx;height: 30rpx;margin-left: 20rpx;">
					</image>
				</picker>
			</view>
			<qiun-data-charts style="height: 300rpx;" type="line" :opts="opts" :chartData="chartData"
				:tooltipShow='false' />
		</view> -->

		<view style="background-color: white;border-radius: 10rpx;padding: 20rpx;background-color:white;">

			<view style="display: flex;align-items: center;font-size: 28rpx;margin-bottom: 20rpx;">
				<text style="margin-right: 40rpx;color: gray;">设备筛选</text>
				<view v-for="i,index in deviceOptions" @click="setDeviceType(index)" class="device_item"
					:style="deviceType==index?'':'background-color: transparent;color: dimgray;'">{{i}}</view>
			</view>
			<scroll-view scroll-y style="max-height: 1100rpx;">
				<view v-for="device,index in displayArr" @click="toLatestData(index)" style="border: 1rpx solid lightgray;border-radius: 10rpx;margin-bottom: 20rpx;">
					<view style="display: flex;align-items: center;padding: 20rpx;border-bottom: 1rpx solid #eee;">
						<image :src="device.stateId==0?'../../static/online.png':'../../static/offline.png'" style="width: 40rpx;height: 40rpx;margin-right: 20rpx;"></image>
						<text style="font-size: 35rpx;font-weight: bold;">{{device.devSerialNumber}}</text>
					</view>
					<view class="device_info_row">
						<view>设备名称：<text style="color: gray;">{{device.devName}}</text></view>
						<view>{{device.types=='定制'?'定制设备':device.types}}</view>
					</view>
					<view class="device_info_row">
						<view>创建时间：<text style="color: gray;">{{device.createTime}}</text></view>
						<view :style="device.stateId==0?'color: limegreen':'color: gray'">{{device.stateId==0?'在线':'离线'}}</view>
					</view>
					
				</view>
			</scroll-view>
			
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				user: {},
				deviceType: 0,
				deviceOptions: ['所有设备', '在线设备', '离线设备'],
				opts: {
					height: 200,
					padding: [15, 40, 0, 15],
					enableScroll: false,
					legend: {
						show: false
					},
					xAxis: {
						disableGrid: false,
						gridType: "dash",
						splitNumber: 8,
						boundaryGap: "justify",
						min: 0,
						max: 24,
						title: '时间',
						titleOffsetX: 12
					},
					yAxis: {
						disableGrid: false,
						splitNumber: 5,
						showTitle: true,
						data: [{
							title: "数量", //纵坐标单位
							titleOffsetY: -5
						}]
					},
				},
				chartData: {
					series: [{
						name: "",
						data: [
							[1.5, 82],
							[4.5, 0],
							[7.5, 7],
							[10.5, 81],
							[13.5, 33],
							[16.5, 66],
							[19.5, 81],
							[22.5, 63]
						]
					}]
				},
				deviceArr:[], //包含所有设备
				onlineArr:[], //在线设备
				offlineArr:[], //离线设备
				displayArr:[] //当前显示的设备

			};
		},
		onLoad() {
			getApp().globalData.deviceArr=[]
			getApp().globalData.customArr=[]
			this.user = getApp().globalData.user
			
			if(this.user.userId){
				let tails = ['getAllDeviceInfoByUserId','getCusDevByUserId']
				
				for(let tail of tails){
					uni.request({
						url:getApp().globalData.baseUrl+tail,
						data:{
							userId:this.user.userId,
							UserId:this.user.userId
						},
						success: (res) => {
							console.log(res.data)
							if(tail == 'getCusDevByUserId'){
								getApp().globalData.customArr = res.data.map(item=>(item.devSerialNumber))
							}
							this.deviceArr = [...this.deviceArr,...res.data]
							getApp().globalData.deviceArr = [...getApp().globalData.deviceArr,...res.data]
							// this.onlineArr = []
							// this.offlineArr = []
							for(let device of res.data){
								if(device.stateId==0){
									this.onlineArr.push(device)
								}else{
									this.offlineArr.push(device)
								}
							}
							this.setDeviceType(this.deviceType)
						}
					})
				}
				
				
				
			}
			
		},
		onShow() {

		},
		methods: {
			toLatestData(index){
				// console.log(this.displayArr[index])
				uni.navigateTo({
					url:`/pages/home/latest-data/latest-data?devSerialNumber=${this.displayArr[index].devSerialNumber}&stateId=${this.displayArr[index].stateId}`
				})
			},
			setDeviceType(index) {
				this.deviceType = index
				switch(index){
					case 0:
						this.displayArr = this.deviceArr
						break
					case 1:
						this.displayArr = this.onlineArr
						break
					case 2:
						this.displayArr = this.offlineArr
				}
			}
		}
	}
</script>

<style lang="scss">
	page {
		background-color: #EAEDF2;
	}

	.count_item {
		width: 33%;
		text-align: center;
	}

	.device_item {
		background-color: #eee;
		padding: 10rpx 20rpx;
		border-radius: 10rpx;
		margin-right: 20rpx;
		border: 1rpx solid lightgray;
	}
	
	.device_info_row{
		display: flex;
		justify-content: space-between;
		padding: 10rpx 20rpx;
	}
</style>