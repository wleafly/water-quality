<template>
	<view style="padding: 20rpx;height: 100%">
		<view style="display: flex;justify-content: space-between;margin-bottom: 20rpx;">
			<view style="color: #105AA9;font-weight: bold;">报警记录</view>
			<navigator url="settings/settings" style="color: gray;font-size: 25rpx;display: flex;align-items: center;"><uni-icons type="gear" color="gray" style="margin-right: 10rpx;"></uni-icons>设置</navigator>
			
		</view>
		<view class="select_view">
			<view class="select_title">设备筛选</view>
			<picker class="select_picker" @change="selectDevice" :range="deviceArr">
				<text>{{currentDevice?currentDevice:"请选择设备"}}</text>
				<uni-icons type="bottom" size="16"></uni-icons>
			</picker>
		</view>
		<view class="select_view">
			<view class="select_title">时间筛选</view>
			<uni-datetime-picker  type="daterange" @change="maskClick"></uni-datetime-picker>
		</view>
		<scroll-view scroll-y style="height: 75%;margin-top: 20rpx;">
			<uni-table :border="true" stripe>
				<uni-tr>
					<uni-th v-for="key in tableHead">{{key}}</uni-th>
				</uni-tr>
				<uni-tr v-for="item in tableData">
					<uni-th>{{item.time}}</uni-th>
					<uni-th>{{item.type==0?'常规设备':'定制设备'}}</uni-th>
					<uni-th>{{item.devSerialNumber}}</uni-th>
					<uni-th>{{item.name}}</uni-th>
					<uni-th>{{item.value}}</uni-th>
					<uni-th>{{`x<${item.min},x>${item.max}`}}</uni-th>
				</uni-tr>
			</uni-table>
			<view style="color: darkgray;text-align: right;margin-top: 20rpx;">
				默认显示最近10条数据
			</view>
		</scroll-view>
	
	</view>
</template>

<script>
	export default {
		data() {
			return {
				deviceArr:[],
				tableHead:['时间','设备类型','监测设备','监测参数','触发值','触发条件'],
				// tableData:['2023-06-29 09:57','10350','定制设备','COD','12.5mg/L','x<25,x>50']
				tableData:[],
				currentDevice:null,
				timeRange:[]
			}
		},
		methods: {
			selectDevice(e){
				this.currentDevice=this.deviceArr[e.detail.value]
				if(this.currentDevice){
					this.updateData()
				}
			},
			maskClick(e){
				this.timeRange = e
				this.timeRange[0] = this.timeRange[0] + " 00:00:00"
				this.timeRange[1] = this.timeRange[1] + " 23:59:59"
				console.log(this.timeRange)
				if(this.timeRange.length==2){
					this.updateData()
				}
			},
			updateData(){
				if(this.timeRange.length && this.currentDevice){
					if(this.currentDevice == "全部"){
						uni.request({
							url:getApp().globalData.baseUrl+"getAlarmNewByUserIdAndTime",
							data:{
								UserId:getApp().globalData.user.userId,
								begin:this.timeRange[0],
								end:this.timeRange[1]
							},
							success: (res) => {
								this.tableData = res.data
							}
						})
					}else{
						uni.request({
							url:this.baseUrl+"getAlarmNewByDevAndTime",
							data:{
								DevSerialNumber:this.currentDevice,
								begin:this.timeRange[0],
								end:this.timeRange[1]
							},
							success: (res) => {
								this.tableData = res.data
							}
						})
					}
				}

			}
		},
		onLoad() {
			this.deviceArr = ["全部",...getApp().globalData.deviceArr.map((item)=>{return item.devSerialNumber})]
			uni.request({
				url:getApp().globalData.baseUrl + "getAlarmRecordByUserIdAndNum",
				data:{
					UserId:getApp().globalData.user.userId,
					num:10
				},
				success: (res) => {
					this.tableData = res.data
				}
			})
		}
	}
</script>

<style>
	page{
		height: 100%;
	}

</style>
