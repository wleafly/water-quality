<template>
	<view>
		<view style="height: 500rpx;display: flex;flex-direction: column;justify-content: center;align-items: center;">
			<image @click="alterHead()" :src="headPath" style="width: 200rpx;height: 200rpx;border-radius: 100rpx;"></image>
			<text style="color: gray;font-size: 35rpx;margin-top: 30rpx;">{{username}}</text>
		</view>
		<view style="display: flex;flex-wrap: wrap;justify-content: space-around;margin: 0 20rpx;">
			<view class="option" v-for="option,index in optionArr" @click="clickOption(index)">
				<image :src="imageUrlArr[index]"></image>
				<view>
					<text class="china_txt">{{option}}</text>
					<text class="en_txt">{{optionArrEn[index]}}</text>
				</view>
			</view>
		</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				username: '',
				optionArr:['设备管理','历史数据','报警中心','安全中心','下载查看','退出登录'],
				optionArrEn:['Device View','Historical Data','Alert Center','Safety Center','Download','exit'],
				imageUrlArr:[
					'../../static/ic_dev_manage.png',
					'../../static/ic_historical_data.png',
					'../../static/ic_warning.png',
					'../../static/ic_safe.png',
					'../../static/ic_download.png',
					'../../static/ic_exit.png'
				],
				headPath:'../../static/head.png' //默认头像
			};
		},
		onShow() {
			this.username = getApp().globalData.user.userName
			
			// uni.getSavedFileList({
			//   success: function (res) {
			//     console.log(res.fileList);
			//   }
			// });
		},
		onLoad(){
			if(uni.getStorageSync("headPath")){
				this.headPath = uni.getStorageSync("headPath")
			}
		},
		methods: {
			alterHead(){
				let that = this
				uni.chooseImage({
					count: 1, //只能选一张图
					crop:{
						width:100,
						height:100
					},
					// sourceType: ['album'], //从相册选择
					success: function (res) {
						uni.getSavedFileList({ //移除旧头像
						  success: function (res) {
							  for(let file of res.fileList){
								  console.log(file)
								  if(file.filePath.endsWith('.jpg')){ //删除掉之前保存的图片,处理后的图片都是jpg后缀
									  uni.removeSavedFile({
									    filePath: file.filePath,
									    complete: function (res) {
									      console.log('删除完毕',res);
									    }
									  });
								  }
							  }
						  }
						});
						let tempFilePaths = res.tempFilePaths;
						console.log('新头像路径',JSON.stringify(tempFilePaths));

						uni.saveFile({ //将裁剪后的头像保存在本地
						  tempFilePath: tempFilePaths[0],
						  success: function (res) {
							var savedFilePath = res.savedFilePath;
							console.log('保存的位置是',savedFilePath)
							uni.setStorageSync("headPath",savedFilePath)
							that.headPath = savedFilePath
						  }
						});
					}
				})
			},
			clickOption(index){
				switch(index){
					case 0:
						uni.navigateTo({
							url:'./device/device'
						})
						break
					case 1:
						uni.navigateTo({
							url:'./history/history'
						})
						break
					case 2:
						uni.navigateTo({
							url:'./alarm/alarm'
						})
						break
					case 3:
						uni.showToast({
							icon:'none',
							title:`点击了${this.optionArr[index]}`
						})
						break
					case 4:
						uni.showToast({
							icon:'none',
							title:`点击了${this.optionArr[index]}`
						})
						break
					case 5:  //退出
						uni.reLaunch({
							url: '../login/login?openMode=exit'
						})
				}
			}
			
		}
	}
</script>

<style lang="scss">
	page {
		background-color: #EAEDF2;
	}

	.option {
		width: 30%;
		height: 250rpx;
		background-color: white;
		margin-bottom: 30rpx;
		border-radius: 10rpx;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;

		image {
			height: 60rpx;
			width: 60rpx;
			margin-bottom: 20rpx;
		}

		view {
			display: flex;
			flex-direction: column;
			align-items: center;

			.china_txt {
				font-size: 33rpx;
				margin-bottom: 10rpx;
			}

			.en_txt {
				font-size: 20rpx;
				color: darkgray
			}
		}

	}
</style>