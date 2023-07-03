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
			<view>{{deviceArr[currentIndex].types}}</view>
		</view>
		<view class="item" style="color: gray;">
			<view>参数数量</view>
			<view>{{paramNum}}</view>
		</view>
		<view class="item">
			<view>设备名称</view>
			<input type="text" v-bind:value="deviceArr[currentIndex].devName" style="text-align: right;"/>
		</view>
		<view class="item">
			<view>设备位置</view>
			<input type="text" v-bind:value="location" style="text-align: right;"/>
		</view>

		<view style="padding: 20rpx 0;">
			<view>设备描述</view>
			<view style="margin-top: 20rpx;border: 1rpx lightgray solid;border-radius: 10rpx;padding: 20rpx;">
				<textarea style="width: 100%;" v-bind:value="devDescription"></textarea>
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
				paramNum:0,//有效参数个数
			}
		},
		methods: {
			changeDevice(e){
				// console.log(e.detail.value)
				this.currentIndex = e.detail.value
				this.getParamNum()
			},
			getParamNum(){
				uni.request({
					url:getApp().globalData.baseUrl+'getRecordNewByDevSerialNumberAndNum',
					data:{
						dev:this.deviceIdArr[this.currentIndex],
						num:1
					},
					success: (res) => {
						console.log(res)
						if(res.statusCode == 200 && res.data.length){
							console.log(res.data[0])
							let i = 0
							Object.keys(res.data[0]).forEach(key=>{
								if(res.data[0][key]!=null && key.includes('_v')){
									i++
								}
								
							})
							this.paramNum = i
						}else{
							this.paramNum = 0
						}
					}
				})
			}
		},
		onLoad() {
			this.deviceArr = getApp().globalData.deviceArr
			console.log(this.deviceArr)
			if(this.deviceArr.length){
				this.deviceIdArr = this.deviceArr.map(item=>item.devSerialNumber)
				this.getParamNum()
			}

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