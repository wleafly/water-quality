<template>
	<view style="padding: 20rpx;">

		<view class="shadow" style="margin-bottom: 20rpx;">
			<view style="color: #105AA9;margin-bottom: 20rpx;display: flex;align-items: center;font-weight: bold">
				<image src="../../../../static/notify_set_list.png" style="width: 40rpx;height: 40rpx;"></image>
				报警方案</view>
				<view class="select_view">
					<view class="select_title">设备筛选</view>
					<picker class="select_picker" @change="changeDevice" :range="deviceArr.map(device=>device.devSerialNumber)">
						<text>{{deviceIndex>=0?deviceArr[deviceIndex].devSerialNumber:'请选择设备'}}</text>
						<uni-icons type="bottom" size="16"></uni-icons>
					</picker>
				</view>
				<scroll-view scroll-y style="max-height: 920rpx;">
					<uni-table :border="true" stripe>
						<uni-tr>
							<uni-th
								v-for="item in ['监测项','触发条件']">{{item}}</uni-th>
						</uni-tr>
						<uni-tr v-for="data in monitors">
							<!-- 常规设备根据简称找到paramMap的参数名，定制设备原样显示 -->
							<uni-th>{{paramMap.get(data.name+'_v')?paramMap.get(data.name+'_v'):data.name}}</uni-th>
							<uni-th>
								<text v-if="data.min!=null">x<{{data.min}}</text>
								<text v-if="data.max!=null">,x>{{data.max}}</text>
								</uni-th>
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
				
					<text>{{deviceIndex>=0?deviceArr[deviceIndex].devSerialNumber:'未选择'}}</text>
		
			</view>
			<view class="select_view">
				<view class="select_title">指标筛选</view>
				<picker class="select_picker" @change="changeParam" :range="params">
					<text>{{paramIndex>=0?params[paramIndex]:'请选择参数'}}</text>
					<uni-icons type="bottom" size="16"></uni-icons>
				</picker>
			</view>
			<view class="select_view">
				<view class="select_title">合法值上限</view>
				<input type="number" v-model:value="max" style="border: 1rpx solid #ddd;border-radius: 5rpx;padding: 0 10rpx;width: 100rpx;text-align: right;"/>
				<text style="color: gray;margin-left: 10rpx;">{{monitors[paramIndex]?(monitors[paramIndex].unit?monitors[paramIndex].unit:paramUnitMap.get(monitors[paramIndex].name+'_v')):''}}</text>
			</view>
			<view class="select_view">
				<view class="select_title">合法值下限</view>
				<input type="number" v-model:value="min" style="border: 1rpx solid #ddd;border-radius: 5rpx;padding: 0 10rpx;width: 100rpx;text-align: right;"/>
				<text style="color: gray;margin-left: 10rpx;">{{monitors[paramIndex]?(monitors[paramIndex].unit?monitors[paramIndex].unit:paramUnitMap.get(monitors[paramIndex].name+'_v')):''}}</text>
			</view>
		
			<view style="display: flex;justify-content: space-between;">
				<button @click="recoverRange()" style="background-color: darkgray;color: white;width: 48%;">重置</button>
				<button @click="updataRange()" style="background-color: #69b4ff;color: white;width: 48%;">更新</button>
			</view>
			
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				deviceArr:getApp().globalData.deviceArr,
				deviceIndex:-1,
				paramIndex:-1,
				monitors:[], //当前所选设备的全部监测项的信息
				params:[], //当前所选设备的全部监测项的名字
				min:null, //合法值下限
				max:null,//合法值下限
				paramMap:getApp().globalData.paramMap,
				paramUnitMap:getApp().globalData.paramUnitMap
			}
		},
		methods: {
			recoverRange(){
				if(this.monitors.length){ //有监测项才执行
					this.min = this.monitors[this.paramIndex].min
					this.max = this.monitors[this.paramIndex].max
				}
			},
			updataRange(){
				if(this.paramIndex>=0 && this.monitors.length){
					//上下限修改过，才发起请求
					if(this.min !== this.monitors[this.paramIndex].min ||this.max !== this.monitors[this.paramIndex].max){
						let data = {
							dev:this.deviceArr[this.deviceIndex].devSerialNumber,
							min:this.min,
							max:this.max
						}
						console.log(data)
						// if(this.min){ 
						// 	data.min = this.min
						// }
						// if(this.max){
						// 	data.max = this.max
						// }
						let url = getApp().globalData.baseUrl
						if(this.deviceArr[this.deviceIndex].types=='定制'){ //更新定制设备某一参数的上下限，用number表示参数
							data.number = this.monitors[this.paramIndex].number
							url += 'changeCusAlarmBoundsByDevAndName'
						}else{ //常规设备，用name
							data.name = this.monitors[this.paramIndex].name
							url += 'changeConAlarmBoundsByDevAndName'
						}
						uni.request({
							url:url,
							data:data,
							success:(res)=>{
								console.log(res)
								if(res.data.code==200){
									this.monitors[this.paramIndex].min = this.min
									this.monitors[this.paramIndex].max = this.max
									uni.showToast({
										icon:'success',
										title:'修改成功'
									})
								}
							}
						})
					}

				}

				
			},
			changeParam(e){
				if(this.monitors.length){ //有监测项才执行
					this.paramIndex = e.detail.value
					this.min = this.monitors[this.paramIndex].min
					this.max = this.monitors[this.paramIndex].max
				}

			},
			changeDevice(e){
				// console.log(e.detail.value)
				this.deviceIndex = e.detail.value
				this.paramIndex = -1
				let devId = this.deviceArr[this.deviceIndex].devSerialNumber
				if(this.deviceArr[this.deviceIndex].types!='定制'){ //常规设备获取上下限要请求接口
					uni.request({
						url:getApp().globalData.baseUrl+'getAlarmManageByDev',
						data:{
							dev:devId
						},
						success:(res)=>{
							console.log(res.data)
							this.monitors = res.data
							//定制设备获取到的参数名是英文简称，需要修改为中文
							this.params = this.monitors.map(item=>this.paramMap.get(item.name+'_v')?this.paramMap.get(item.name+'_v'):item.name)
			
						}
					})
				}else{ //定制设备各参数上下限直接从deviceArr获取
					console.log(this.deviceArr[this.deviceIndex].customParaList)
					this.monitors = this.deviceArr[this.deviceIndex].customParaList
					this.params = this.monitors.map(item=>item.name)

				}
				
			}
		}
	}
</script>

<style>

</style>
