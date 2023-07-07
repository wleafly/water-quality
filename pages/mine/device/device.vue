<template>
	<view style="padding: 0 20rpx;">
		<view class="item">
			<view>设备号</view>
			<picker class="static_font" @change="changeDevice" :range="deviceIdArr">
				{{deviceIdArr[currentIndex]}}<uni-icons type="bottom" size="16"></uni-icons></picker>
		</view>
		<view class="item " style="color: gray;">
			<view>创建时间</view>
			<view>{{deviceArr[currentIndex].createTime}}</view>
		</view>
		<view class="item" style="color: gray;">
			<view>当前状态</view>
			<view>{{deviceArr[currentIndex].stateId==0?'在线':'离线'}}</view>
		</view>
		<view class="item" style="color: gray;">
			<view>设备类型</view>
			<view>{{deviceArr[currentIndex].types=='定制'?'定制设备':'常规设备'}}</view>
		</view>
		<view class="item">
			<view>设备名称</view>
			<input type="text" v-model="devName" style="text-align: right;"/>
		</view>
		<view class="item">
			<view>设备位置</view>
			<input type="text" v-model="location" style="text-align: right;"/>
		</view>

		<view style="padding: 20rpx 0;">
			<view>设备描述</view>
			<view style="margin-top: 20rpx;border: 1rpx lightgray solid;border-radius: 10rpx;padding: 20rpx;">
				<textarea style="width: 100%;" v-model="devDescription"></textarea>
			</view>
		</view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				location:'',
				devName:'',
				devDescription:'',
				deviceArr: [],
				deviceIdArr:[],
				currentIndex:0,
				isNormal:null
			}
		},
		methods: {
			changeDevice(e){
				this.saveConfig(this.currentIndex)
				// console.log(e.detail.value)
				this.currentIndex = e.detail.value
				this.setInfo()
			},
			setInfo(){
				if(this.deviceArr.length){
					this.devName = this.deviceArr[this.currentIndex].devName
					this.location = this.deviceArr[this.currentIndex].location
					this.devDescription = this.deviceArr[this.currentIndex].devDescription
				}

			},
			saveConfig(index){
				let selectDevice = this.deviceArr[index]
				let isNormal = !(selectDevice.types == "定制")
				//有值改变时，发起请求
		
				if(this.devName!=selectDevice.devName || this.location!=selectDevice.location || this.devDescription!=selectDevice.devDescription){
					console.log('配置表有变动')
					// let url = getApp().globalData.baseUrl + 'changeDevInfoByDevId?'+
					// 'type='+isNormal?0:1+'&dev='+selectDevice.devSerialNumber+
					// '&userId='+getApp().globalData.user.userId+'&name='+this.devName
					// +'&location='+this.location+'&descri='+this.devDescription
					// console.log(url)
					uni.request({
						url:getApp().globalData.baseUrl + 'changeDevInfoByDevId',
						data:{
							type:isNormal?0:1,
							dev:selectDevice.devSerialNumber,
							userId:getApp().globalData.user.userId,
							name:this.devName,
							location:this.location,
							descri:this.devDescription
						},
						success:(res)=> {
							if(res.data.code == '200'){
								selectDevice.devName = this.devName
								selectDevice.location = this.location
								selectDevice.devDescription = this.devDescription
								uni.showToast({
									icon:'none',
									title:'配置已自动保存'
								})
							}
						}
					})
				}
	
				
			}
		},
		onLoad() {
			this.deviceArr = getApp().globalData.deviceArr
			console.log(this.deviceArr)
			this.deviceIdArr = this.deviceArr.map(item=>item.devSerialNumber)
			this.setInfo()

		},
		onBackPress() {
			this.saveConfig(this.currentIndex)
		}
	}
</script>

<style>
	.item {
		display: flex;
		justify-content: space-between;
		padding: 20rpx 0;
		border-bottom: 1rpx solid #ddd;
	}

</style>