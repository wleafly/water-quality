<template>
	<view>
		<qiun-data-charts @getImage="getImage" ref="mychart" style="height: 800rpx;" type="line" :opts="opts"
			:chartData="chartData" ontouch onzoom></qiun-data-charts>
		<button @click="chartToImage">截图</button>
	</view>

</template>

<script>
	export default {

		data() {
			return {
				opts: {
					padding:[0,10,0,0],//上右下左
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
				chartData: {
					categories: [],
					series: [{
						name: "",
						data: []
					}]
				}
			}
		},
		onShow() {
			let categories = []
			let data = []
			for (let i = 0; i < 100; i++) {
				categories.push('2023-06-'+i)
				data.push(Math.round(Math.random() * 100))
			}
			this.opts.xAxis.itemCount = data.length
			this.chartData = {
				categories: categories,
				series: [{
					name: "",
					data: data
				}]
			}
		},
		methods: {
			chartToImage() {
				this.$refs.mychart.getImage()
			},
			getImage(e) {
				console.log(e)
				// this.imgUrl = e.base64
				let base64 = e.base64;
				uni.saveImageToPhotosAlbum({
					filePath: e.base64,
					success: function() {
						uni.showToast({
							title: '图片保存成功',
							icon: 'none'
						})
					}
				});
					
			}


		}
	}
</script>

<style>

</style>