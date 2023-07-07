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
			<view v-if="defaultShow" style="color: darkgray;text-align: right;margin-top: 20rpx;">
				默认显示最近10条报警数据
			</view>
			<view v-else style="display: flex;justify-content: space-between;color: gray;margin-top: 20rpx;">
				<view @click="lastPage">上一页</view>
				<view style="display: flex;" @click="inputPageNum">
					<text style="color: #2979FF;">{{pageNum}}</text>/{{totalPages}}
				</view>
				<view @click="nextPage">下一页</view>
			</view>
		</scroll-view>
	
	</view>
</template>

<script>
	export default {
		data() {
			return {
				defaultShow:true,
				deviceArr:[],
				tableHead:['时间','设备类型','监测设备','监测参数','触发值','触发条件'],
				dataArr:[],
				currentDevice:null,
				timeRange:[],
				
				pageNum: 1, //默认页数
				totalPages: 1, //总页数
				pageAmount: 20, //单页最多显示条数
				tableData: [], //分页显示用，dataArr的一部分
			}
		},
		methods: {
			lastPage() {
				if (this.pageNum > 1) {
					this.pageNum--
					this.sliceTableData()
				}
			},
			nextPage() {
				if (this.pageNum < this.totalPages) {
					this.pageNum++
				}
				this.sliceTableData()
			},
			inputPageNum() {
				let that = this
				uni.showModal({
					title: '输入跳转的页数',
					editable: true,
					success(res) {
						if (res.confirm) {
							let num = parseInt(res.content)
							if (num != NaN && num > 0 && num <= that.totalPages && num != that.pageNum) {
								that.pageNum = num
								that.sliceTableData()
							}
						}
					}
				})
			},
			sliceTableData() { //每页截100条数据
				this.tableData = this.dataArr.slice((this.pageNum - 1) * this.pageAmount, this.pageNum * this.pageAmount)
			},
			updateTable(){
				this.pageNum = 1
				this.totalPages = Math.ceil(this.dataArr.length / this.pageAmount) //计算页数
				this.tableData = this.dataArr.slice(0, this.pageAmount)
			},
			
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
					if(this.defaultShow){
						this.defaultShow = false
					}
					let url = getApp().globalData.baseUrl
					let data = {
						begin:this.timeRange[0],
						end:this.timeRange[1]
					}
					if(this.currentDevice == "全部"){
						url += "getAlarmNewByUserIdAndTime"
						data.userId = getApp().globalData.user.userId
					}else{
						url += "getAlarmNewByDevAndTime"
						data.dev = this.currentDevice

					}
					uni.request({
						url:url,
						data:data,
						success: (res) => {
							this.dataArr = res.data
							this.updateTable()
							console.log(this.dataArr)
						}
					})
				}

			}
		},
		onLoad() {
			this.deviceArr = ["全部",...getApp().globalData.deviceArr.map((item)=>{return item.devSerialNumber})]
			uni.request({
				url:getApp().globalData.baseUrl + "getAlarmRecordByUserIdAndNum",
				data:{
					userId:getApp().globalData.user.userId,
					num:10
				},
				success: (res) => {
					this.tableData = res.data
					console.log(this.tableData)
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
