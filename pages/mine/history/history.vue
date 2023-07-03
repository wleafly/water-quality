<template>
	<view style="padding: 20rpx;">
		<view class="select_view">
			<view class="select_title">设备筛选</view>
			<picker @change="selectDevice" :range="deviceArr.map((item)=>{return item.devSerialNumber})"
				class="select_picker">
				<text>{{currentDevice?currentDevice:'请选择设备'}}</text>
				<uni-icons type="bottom" size="16"></uni-icons>
			</picker>
		</view>
		<view style="color: gray;margin-right: 20rpx;margin-bottom: 20rpx;">时间筛选</view>
		<uni-datetime-picker style="margin-bottom: 20rpx;" v-model="datetimerange" type="datetimerange"
			rangeSeparator="~" @change="changeTime" />
		<view style="display: flex;align-items: center;margin-bottom: 20rpx;">
			<view style="color: gray;margin-right: 20rpx;">查看方式</view>
			<view @click="showTable=true" class="option"
				:style="showTable?'color: black;background-color: #eee':'color: gray;'" style="margin-right: 20rpx;">表格</view>
			<view @click="showTable=false" class="option"
				:style="showTable?'color: gray;':'color: black;background-color: #eee'">折线图</view>

		</view>
		<view v-if="showTable">
			<view v-if="tableData.length">
				<scroll-view scroll-y style="max-height: 920rpx;">
					<uni-table :border="true" stripe>
						<uni-tr>
							<uni-th
								v-for="key in usefulKeys">{{paramMap.get(key)+(paramUnitMap.get(key)?`(${paramUnitMap.get(key)})`:'')}}</uni-th>
						</uni-tr>
						<uni-tr v-for="data in tableData">
							<uni-th v-for="key in usefulKeys">{{data[key]!=null?data[key]:'--'}}</uni-th>
						</uni-tr>
					</uni-table>
				</scroll-view>
				<view style="display: flex;justify-content: space-between;color: gray;margin-top: 20rpx;">
					<view @click="lastPage">上一页</view>
					<view style="display: flex;" @click="inputPageNum">
						<text style="color: #2979FF;">{{pageNum}}</text>/{{totalPages}}
					</view>
					<view @click="nextPage">下一页</view>
				</view>
				<button @click="getExcel()" class="save_button" >导出Excel表格</button>
			</view>
		</view>
		<view v-else>
			<view style="display: flex;">
				<view style="color: gray;margin-right: 20rpx;line-height: 60rpx;">可选参数</view>
				<view style="display: flex;flex-wrap: wrap;width: 75%;">
					<view v-for="key,index in usefulKeys">
						<view @click="chooseParam(index)" v-if="key!='time'" class="option" :style="paramIndex==index?'color: black;background-color: #eee':'color: gray;'" style="margin-bottom: 20rpx;margin-right: 20rpx;">
							{{paramMap.get(key)}}
						</view>
					</view>
				</view>
			</view>

			<view v-if="chartData">
				<qiun-data-charts ref="mychart" @getImage="downloadImage" style="height: 700rpx;" type="line" :opts="opts"
					:chartData="chartData" ontouch onzoom></qiun-data-charts>
				<button @click="chartToImage()" class="save_button" >保存至相册</button>
			</view>

		</view>




	</view>
</template>

<script>
	export default {

		data() {
			return {
				datetimerange: [],
				deviceArr: [],
				currentDevice: '',
				showTable: true, //为false时显示折线图
				timeRange: [],
				usefulKeys: [],
				
				pageNum: 1, //默认页数
				totalPages: 1, //总页数
				pageAmount: 100, //单页最多显示条数
				tableData: [], //分页显示用，dataArr的一部分
				dataArr: [],
				
				paramIndex:0,//当前折线图选择的参数索引
				opts: {
					padding:[10,10,0,0],//上右下左
					enableScroll: true,
					dataLabel: false,
					dataPointShape: false,
					update: true,
					legend: {
						show: false
					},
					animation: false,
					duration: 0,
					xAxis: {
						boundaryGap: "justify", //消除左右间距
						fontSize: 10,
						axisLabel: {
							rotate: 40
						},
						labelCount: 5
					},
					yAxis: {
						showTitle: true,
						data: [{
							title: "", //纵坐标单位
							tofix: 0, //保留几位小数，0表示随数据自动调整
						}]
					},
					extra: {
						line: {
							width: 1 //折线图线条宽度
						}
					}
				},
				chartData: null,
				
				dayArr: [], //存储日期，防止重复获取参数表
				paramMap: getApp().globalData.paramMap,
				paramUnitMap: getApp().globalData.paramUnitMap,
			}
		},

		onLoad() {
			// console.log(getApp().globalData.deviceArr)
			this.deviceArr = getApp().globalData.deviceArr
			// this.test()
		},
		methods: {
			chartToImage() {
				this.$refs.mychart.getImage()
			},
			downloadImage(e) {
				console.log(e)
				// this.imgUrl = e.base64
				let base64 = e.base64;
				uni.saveImageToPhotosAlbum({
					filePath: e.base64,
					success: function() {
						uni.showModal({
							content:'图片保存成功',
							showCancel:false
						})
					}
				});
					
			},
			
			chooseParam(index){
				this.paramIndex = index
				let key = this.usefulKeys[this.paramIndex]
				if(key){
					let categories = []
					let data = []
					for(let i of this.dataArr){
						if(i[key]!=null){
							categories.push(i.time.slice(2,-3))
							data.push(i[key])
						}
					}
					this.opts.xAxis.itemCount = data.length
					this.opts.yAxis.data[0].title = this.paramUnitMap.get(key)
					this.chartData = {
						categories: categories,
						series: [{
							name: "",
							data: data
						}]
					}
					// console.log(categories.length)
				}
			},
			getExcel(){
				/*#ifdef APP-PLUS*/
				let tableData = this.dataArr
				if(tableData.length){				
					let excelData = [this.usefulKeys.map(item=>this.paramMap.get(item)+(this.paramUnitMap.get(item)?`(${this.paramUnitMap.get(item)})`:''))] //二维数组
					for(let item of tableData){
						let rowData = []
						for(let key of this.usefulKeys){
							rowData.push(item[key]?item[key]:'--')
						}
						excelData.push(rowData)
					}
					let fileName = `${this.currentDevice}历史数据${this.timeRange[0]+'~'+this.timeRange[1]}`
					this.createExcelInApp(excelData,this.currentDevice,fileName)
				}
				/*#endif*/
			}, 
			createExcelInApp(excelData,sheetName,fileName){ //app生成表格模板
				  let worksheet = sheetName
				  let str = ''
				  //循环遍历，每行加入tr标签，每个单元格加td标签
				  for (let i = 0; i < excelData.length; i++) {
					str += '<tr>'
					for (let unitValue of excelData[i]) {
					  //增加\t为了不让表格显示科学计数法或者其他格式
					  str += `<td>${ unitValue + '\t'}</td>`
					}
					str += '</tr>'
				  }
				  //下载的表格模板数据
				  let template = `<html xmlns:o="urn:schemas-microsoft-com:office:office"
					xmlns:x="urn:schemas-microsoft-com:office:excel"
					xmlns="http://www.w3.org/TR/REC-html40">
					<head><!--[if gte mso 9]><xml encoding="UTF-8"><x:ExcelWorkbook><x:ExcelWorksheets><x:ExcelWorksheet>
					<x:Name>${worksheet}</x:Name>
					<x:WorksheetOptions><x:DisplayGridlines/></x:WorksheetOptions></x:ExcelWorksheet>
					</x:ExcelWorksheets></x:ExcelWorkbook></xml><![endif]-->
					</head><body><table>${str}</table></body></html>`
				  //下载模板
				  this.exportExcel(template, 'excel',fileName)
			},
			exportExcel (fileData, documentName = 'excel',fileName) { //app导出表格的方法
			  /*
			  PRIVATE_DOC: 应用私有文档目录常量
			  PUBLIC_DOCUMENTS: 程序公用文档目录常量
			  */
			  plus.io.requestFileSystem(plus.io.PUBLIC_DOCUMENTS, function(fs) {
			    let rootObj = fs.root
			    let fullPath = rootObj.fullPath
			    console.log("开始导出数据...")
			    // 创建文件夹
			    rootObj.getDirectory(documentName, {
			      create: true
			    }, function(dirEntry) {
			      // 创建文件,防止重名
			      // let fileName = new Date().getTime()
			      console.log(fileName)
			      dirEntry.getFile(`${fileName}.xlsx`, {
			        create: true
			      }, function(fileEntry) {
			        fileEntry.createWriter(function(writer) {
			          writer.onwritestart = (e) => {
						uni.showLoading({
							title:"正在导出..."
						})
			          }
			          writer.onwrite = (e) => {
						uni.hideLoading()
			            setTimeout(() => {
						  uni.showToast({
						  	icon:'success',
							title:'导出成功'
						  })
			              const path = `file://${fullPath}${documentName}/${fileName}.xlsx`
			              console.log('文件位置：', path)
			              // 打开文件
			              uni.openDocument({
			                filePath: path,
			                success: res => {
			                  console.log('打开文档成功', res)
			                },
			                fail: e => {
			                  console.log('打开失败', e)
			                }
			              })
			            },500)
			          }
			          // 写入内容
			          writer.write(fileData)
			        }, function(e) {
			          console.log(e.message)
			        })
			      })
			    })
			  })
			},
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
			handleData() {  //搜索完毕时，执行
				let that = this
				that.usefulKeys = []
				that.dayArr = []
				for (let item of that.dataArr) {
					if (!that.dayArr.includes(item.time.split(' ')[0])) {
						that.dayArr.push(item.time.split(' ')[0])
						Object.keys(item).forEach(key => {
							if (item[key] != null && (key == 'temp' || key.includes('_v'))) {
								if (!that.usefulKeys.includes(key)) {
									that.usefulKeys.push(key)
								}
							}
						})

					}
				}
				if (that.usefulKeys.length) {
					that.usefulKeys.push('time')
				}
				console.log(that.dayArr)
				console.log(that.usefulKeys)
			},

			test() {
				this.dataArr = []
				this.usefulKeys = []
				this.dayArr = []
				let i = 0
				while (i < 200) {
					i += 1
					this.dataArr.push({
						"devSerialNumber": 10350,
						"time": "2023-06-2" + (i < 100 ? '0' : '1') + " 13:20:" + (60 - i),
						"temp": 26.9+Math.round(Math.random()*10),
						"vad": 12.22,
						"reId": 15235063,
						"e": 0,
						"n": 0,
						"orp_v": i < 100 ? null : (1.5+Math.round(Math.random()*10)),
						"orp_t": null,
						"phg_v": 5.4+Math.round(Math.random()*10),
						"zs_v": 3,
						"zs_t": null,
						"cod_v": i < 100 ? 12.5 : null,
						"cod_t": null,
						"nhn_v": 0.355,
						"nhn_t": 26.9,
						"cl_v": 551.5+Math.round(Math.random()*10)
					})
				}
				this.updateTable()
				this.handleData()
				this.chooseParam(0)
			},
			changeTime(e) {
				console.log(e)
				this.timeRange = e
				this.getData()
			},
			selectDevice(e) {
				console.log(this.deviceArr[e.detail.value].devSerialNumber)
				this.currentDevice = this.deviceArr[e.detail.value].devSerialNumber
				this.getData()
			},
			getData() {
				if (this.timeRange.length == 2 && this.currentDevice) {
					let that = this
					uni.request({
						url: getApp().globalData.baseUrl + 'getRecordNewByDevAndTime',
						data: {
							dev: this.currentDevice,
							begin: this.timeRange[0],
							end: this.timeRange[1]
						},
						success: (res) => {
							console.log(res.data)
							if(res.data.length==0){
								uni.showModal({
									content:'未查询到数据',
									showCancel:false
								})
								return
							}
							that.dataArr = res.data
							that.updateTable()
							that.handleData()
							that.chooseParam(0)
						}
					})
				}
			}
		}
	}
</script>

<style>
	.option {
		border-radius: 10rpx;
		width: 150rpx;
		padding: 10rpx 0;
		text-align: center;
		border: 1px solid lightgray;
		
	}
	.save_button{
		margin-top: 20rpx;background-color: lightseagreen;color: white;font-weight: bold;
	}
</style>