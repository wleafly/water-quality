<template>
	<view style="padding: 20rpx;">
		<view class="shadow">
			<view style="display: flex;align-items: center;">
				<image :src="state==0?'../../../static/online.png':'../../../static/offline.png'" style="width: 40rpx;height: 40rpx;margin-right: 20rpx;"></image>
				<text style="font-size: 35rpx;font-weight: bold;">{{devId}}</text>
				
			</view>
			<view style="margin-top: 20rpx;border-top: 1rpx solid #eee;border-bottom: 1rpx solid #eee;padding-bottom: 20rpx">
				<!-- 常规参数最后一条数据 -->
				<view v-if="isNormal">
					<view v-for="val,key,index in dataArr[0]" >
						<view v-if="val!=null && (key=='temp'||key.includes('_v'))" style="display: flex;justify-content: space-between;margin-top: 20rpx;">
							<view style="display: flex;align-items: center;">
								<image :src="'../../../static/param/'+key.split('_')[0]+'.jpg'" style="width: 30rpx;height: 30rpx;margin-right: 10rpx;"></image>
								{{paramMap.get(key)}}
							</view>
							<view>{{val+' '}}<text style="color: gray;">{{paramUnitMap.get(key)}}</text></view>
						</view>
					</view>
				</view>
				<!-- 定制参数最后一条数据 -->
				<view v-else>
					<view v-for="val,key,index in dataArr[0]" >
						<!-- 此处usefulKeys是经过参数表对应筛选过的 -->
						<view v-if="val!=null && key.includes('value') && usefulKeys.includes(key)" style="display: flex;justify-content: space-between;margin-top: 20rpx;">
							<view style="display: flex;align-items: center;">
								{{customParamList.find(item=>item.number==Number(key.slice(5))).name}}
							</view>
							<view>{{val+' '}}<text style="color: gray;">{{customParamList.find(item=>item.number==Number(key.slice(5))).unit}}</text></view>
						</view>
					</view>
				</view>

				<view v-if="usefulKeys.length==0" style="margin-top: 20rpx;color: gray;">
					无参数
				</view>
			</view>
			
			<view style="color: darkgray;margin-top: 20rpx;display: flex;justify-content: flex-end;">最后更新于{{dataArr[0]?dataArr[0].time:''}}</view>
		</view>
		
		<view class="shadow" style="margin-top: 20rpx;">
			<view style="display: flex;justify-content: space-around;margin-bottom: 20rpx;">
				<view @click="setTableDisplay(true)" :style="showTable?'color: royalblue;font-weight: bold':'color: gray'">近期数据</view>
				<view @click="setTableDisplay(false)" :style="showTable?'color: gray':'color: royalblue;font-weight: bold'">变化趋势</view>
			</view>
			<!-- 最新10条数据，表格显示 -->
			<uni-table v-if="showTable" :border="true" stripe >
				<uni-tr>
					<uni-th v-for="key in [...usefulKeys,'time']">{{isNormal?(paramMap.get(key)+(paramUnitMap.get(key)?`(${paramUnitMap.get(key)})`:'')):(key.includes('value')?(customParamList.find(item=>item.number==Number(key.slice(5))).name+'('+customParamList.find(item=>item.number==Number(key.slice(5))).unit+')'):'时间')}}</uni-th>
				</uni-tr>
				<uni-tr v-for="data in dataArr">
					<uni-th v-for="key in [...usefulKeys,'time']">{{data[key]}}</uni-th>
				</uni-tr>
			</uni-table>
			<!-- 最新10条数据，折线图按时间正序显示 -->
			<view v-else>
				<view style="display: flex;flex-wrap: wrap;justify-content: space-around;">
					<view v-for="key,index in usefulKeys" @click="seleceParam(index)" style="border: 1rpx solid lightgray;width: 23%;text-align: center;border-radius: 10rpx;padding:5rpx 0;margin-bottom: 20rpx;"
					:style="selectedParam==index?'background-color: #eee':'color:gray'">{{paramMap.get(key)?paramMap.get(key):customParamList.find(item=>item.number==Number(key.slice(5))).name}}</view>
				</view>
				<qiun-data-charts type="line" :opts="opts" :chartData="chartData"></qiun-data-charts>
			</view>
			<view style="text-align: right;color: darkgray;margin-top: 20rpx;">仅显示最近10条数据</view>
			
		</view>
		
		
	</view>
	
</template>

<script>
	export default {
		data() {
			return {
				isNormal:null,
				customParamList:null, //定制设备的参数表
				devId:'',
				state:'',
				dataArr:[],
				paramMap:getApp().globalData.paramMap,
				paramUnitMap:getApp().globalData.paramUnitMap,
				showTable:true,
				usefulKeys:[],

				opts: {
					// enableScroll: true,
					// scrollPosition:'right',
					update:true,
					legend: {show:false},
					animation:false,
					duration:0,	
					xAxis: {
					  // itemCount: 11,
					  boundaryGap: "justify",//消除左右间距
					  disableGrid: false,//绘制纵向线条
					  fontSize:11,
					  axisLabel:{
						  rotate:40
					  },
					  labelCount:6
					},
					yAxis: {
						showTitle:true,
						data: [{
							title: "", //纵坐标单位
							tofix: 0, //保留几位小数，0表示随数据自动调整
						}]
					}
				},
				chartData: {
					categories:[],
					series: [{
						name: "",
						data: []
					}]
				},
				selectedParam:0
			}
		},
		methods: {
			seleceParam(index){
				this.selectedParam = index
				let key = this.usefulKeys[index] //常规设备就是cod_v这样的，定制设备就是value1这样的
				let title
				let series_name
				if(this.isNormal){ //常规设备的情况
					title = this.paramUnitMap.get(key) //设置纵坐标顶部单位
					series_name = this.paramMap.get(key) //设置弹窗中的化学参数名
				}else{
					let paramInfo = this.customParamList.find(item=>item.number==Number(key.slice(5))) //根据number从参数表中获取对应参数的信息
					title = paramInfo.unit //设置纵坐标顶部单位
					series_name = paramInfo.name //设置弹窗中的化学参数名
				}
				
				this.opts.yAxis.data[0].title = title
				let categories = []
				let series_data = []
				for(let i = this.dataArr.length-1;i>=0;i--){
					let data = this.dataArr[i]
					if(i!=0){
						// console.log(data.time.slice(6,-3))
						categories.push(data.time.slice(6,-3))
					}else{
						categories.push('')
					}
					series_data.push(data[key])
				}

				this.chartData = {
					categories:categories,
					series: [{
						name: series_name,
						data: series_data
					}]
				}
			},
			setTableDisplay(bool){
				this.showTable = bool
				if(!bool){
					if(this.usefulKeys.length){
						this.seleceParam(this.selectedParam)
					}else{
						uni.showToast({
							icon:'none',
							title:'无有效数据'
						})
					}
				}
			},


		},
		onLoad(option) {
			this.state = option.stateId //0表示在线，1表示离线
			this.devId = option.devSerialNumber  
			this.isNormal = !getApp().globalData.customArr.includes(Number(this.devId)) //用于判断是常规还是定制
			if(this.devId){
				let url_suf = this.isNormal?'getRecordNewByDevSerialNumberAndNum':'getCusRecordByDevAndNum' //常规设备与定制设备是不同的接口
				uni.request({
					url:getApp().globalData.baseUrl+url_suf,
					data:{
						num:10,
						dev:this.devId
					},
					success: (res) => {
						console.log(res.data)
						this.dataArr = res.data
						if(this.dataArr.length){
							if(this.isNormal){
								//常规设备的参数都是cod_v类型的，temp也算
								Object.keys(this.dataArr[0]).forEach(key=>{
									if(this.dataArr[0][key]!=null && (key=='temp'||key.includes('_v'))){
										this.usefulKeys.push(key) //usefulKeys存储所有的有效参数名，但不做处理
									}
									
								})
							}else{
								//customParamList通过devId从deviceArr中获取
								this.customParamList = getApp().globalData.deviceArr.find(item=>item.devSerialNumber == this.devId).customParaList
								console.log(this.customParamList)
								//定制设备的参数都是value1类型的
								Object.keys(this.dataArr[0]).forEach(key=>{
									if(this.dataArr[0][key]!=null && key.includes('value')){
										if(this.customParamList.find(item=>item.number==Number(key.slice(5)))){
											this.usefulKeys.push(key) //同样存储所有的有效参数名，但不做处理
										}
									}
									
								})
								console.log(this.usefulKeys)
							}
		
						}
						
					}
				})
				
			}
		}
	
	}
</script>

<style>

</style>
