<template>
	<view style="padding: 20rpx;">
		<view class="shadow">
			<view style="display: flex;align-items: center;">
				<image :src="state==0?'../../../static/online.png':'../../../static/offline.png'" style="width: 40rpx;height: 40rpx;margin-right: 20rpx;"></image>
				<text style="font-size: 35rpx;font-weight: bold;">{{devId}}</text>
				
			</view>
			<view style="margin-top: 20rpx;border-top: 1rpx solid #eee;border-bottom: 1rpx solid #eee;padding-bottom: 20rpx">
				<view v-for="val,key,index in dataArr[0]" >
					<view v-if="val!=null && (key=='temp'||key.includes('_v'))" style="display: flex;justify-content: space-between;margin-top: 20rpx;">
						<view style="display: flex;align-items: center;">
							<image :src="'../../../static/param/'+key.split('_')[0]+'.jpg'" style="width: 30rpx;height: 30rpx;margin-right: 10rpx;"></image>
							{{paramMap.get(key)}}
						</view>
						<view>{{val+' '}}<text style="color: gray;">{{paramUnitMap.get(key)}}</text></view>
					</view>
				</view>
				<view v-if="usefulKeysWithoutTime.length==0" style="margin-top: 20rpx;color: gray;">
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
			
			<uni-table v-if="showTable" :border="true" stripe >
				<uni-tr>
					<uni-th v-for="key in usefulKeys">{{paramMap.get(key)+(paramUnitMap.get(key)?`(${paramUnitMap.get(key)})`:'')}}</uni-th>
				</uni-tr>
				<uni-tr v-for="data in dataArr">
					<uni-th v-for="key in usefulKeys">{{data[key]}}</uni-th>
				</uni-tr>
			</uni-table>
			<view v-else>
				<view style="display: flex;flex-wrap: wrap;justify-content: space-around;">
					<view v-for="key,index in usefulKeysWithoutTime" @click="seleceParam(index)" style="border: 1rpx solid lightgray;width: 23%;text-align: center;border-radius: 10rpx;padding:5rpx 0;margin-bottom: 20rpx;"
					:style="selectedParam==index?'background-color: #eee':'color:gray'">{{paramMap.get(key)}}</view>
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
				devId:'',
				state:'',
				dataArr:[],
				paramMap:getApp().globalData.paramMap,
				paramUnitMap:getApp().globalData.paramUnitMap,
				showTable:true,
				usefulKeys:[],
				usefulKeysWithoutTime:[],
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
				let key = this.usefulKeys[index]
				this.opts.yAxis.data[0].title = this.paramUnitMap.get(key)
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
						name: this.paramMap.get(key),
						data: series_data
					}]
				}
			},
			setTableDisplay(bool){
				this.showTable = bool
				if(!bool){
					if(this.usefulKeysWithoutTime.length){
						this.seleceParam(this.selectedParam)
					}else{
						uni.showToast({
							icon:'none',
							title:'无有效数据'
						})
					}
				}
			},
			test(){
				let i = 0
				this.dataArr = []
				this.usefulKeys = []
				this.usefulKeysWithoutTime = []
				while(i<20){
					i+=2
					this.dataArr.push({
						"devSerialNumber":10350,
						"time":"2023-06-20 13:20:"+(60-i),
						"temp":26.9,
						"vad":12.22,
						"reId":15235063,
						"e":0,
						"n":0,
						"orp_v":1.5,
						"orp_t":null,
						"phg_v":5.4,
						"zs_v":3,
						"zs_t":null,
						"cod_v":null,
						"cod_t":null,
						"nhn_v":0.355,
						"nhn_t":26.9,
						"cl_v":55
						})
				}
				console.log(this.dataArr.length)
				if(this.dataArr.length){
					Object.keys(this.dataArr[0]).forEach(key=>{
						if(this.dataArr[0][key]!=null && (key=='temp'||key.includes('_v'))){
							this.usefulKeys.push(key)
						}
						
					})
					this.usefulKeys.push('time')
					this.usefulKeysWithoutTime = this.usefulKeys.slice(0,this.usefulKeys.length-1)
					// console.log(this.usefulKeysWithoutTime)
				}
			}

		},
		onLoad(option) {
			this.state = option.stateId
			// this.state = option.stateId == 0?'在线':'离线'
			this.devId = option.devSerialNumber
			if(this.devId){
				uni.request({
					url:getApp().globalData.baseUrl+`getRecordNewByDevSerialNumberAndNum?dev=${this.devId}&num=10`,
					success: (res) => {
						console.log(res.data)
						this.dataArr = res.data
						if(this.dataArr.length){
							Object.keys(this.dataArr[0]).forEach(key=>{
								if(this.dataArr[0][key]!=null && (key=='temp'||key.includes('_v'))){
									this.usefulKeys.push(key)
								}
								
							})
							this.usefulKeys.push('time')
							this.usefulKeysWithoutTime = this.usefulKeys.slice(0,this.usefulKeys.length-1)
						}
					}
				})
			}
		},
		onShow() {
			// this.test()
		}
	
	}
</script>

<style>

</style>
