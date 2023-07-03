<template>
	<view style="height: 100%;">
		<view class="flex_column_center" style="height: 30%;">
			<image src="../../static/chemins_logo.png" style="width: 200rpx;height: 200rpx;"></image>
			<text style="color: gray;font-size: 70rpx;">水质监测</text>
		</view>
		<view class="flex_column_center" style="height: 30%;">
			<view class="input_box" style="display: flex;">
				<image src="../../static/username.png" style="width: 40rpx;height: 40rpx;margin-right: 10rpx;"></image>
				<input type="text" style="width: 90%;" placeholder="用户名" v-model:value="username"/>
			</view>
			<view class="input_box" style="display: flex;margin: 30rpx 0;">
				<image src="../../static/password.png" style="width: 40rpx;height: 40rpx;margin-right: 10rpx;"></image>
				<input type="text" style="width: 90%;" password placeholder="密码" v-model:value="password"/>
			</view>
			<view style="display: flex;justify-content: space-between;width: 70%;color: darkgray;">
				<view style="display: flex;align-items: center;" @click="switchAutoLogin()">
					<image :src="isAutoLogin?'../../static/checkbox_selected.png':'../../static/checkbox.png'" style="width: 40rpx;height: 40rpx;margin-right: 5rpx;"></image>自动登录
				</view>
				<view style="display: flex;align-items: center;" @click="switchSavePassword()">
					<image :src="isSavePassword?'../../static/checkbox_selected.png':'../../static/checkbox.png'" style="width: 40rpx;height: 40rpx;margin-right: 5rpx;"></image>记住密码
				</view>
			</view>
			
		</view>
		<view class="flex_column_center" style="height: 40%;">
			<button @click="login()" style="width: 70%;background-color: #105AA9;color: white;font-weight: bold;">登录</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				isAutoLogin:false,
				isSavePassword:true,
				username:'',
				password:''
			}
		},
		onLoad(option) {
			console.log(option.openMode)
			let loginSetting = uni.getStorageSync("loginSetting")
			let loginInfo = uni.getStorageSync("loginInfo")
			if(loginSetting){
				this.isAutoLogin = loginSetting.isAutoLogin
				this.isSavePassword = loginSetting.isSavePassword
			}
			if(loginInfo){
				this.username = loginInfo.username
				this.password = loginInfo.password
			}
			if(this.isAutoLogin && option.openMode != 'exit'){
				this.login()
			}
			
		
		},
		onShow() {
			
		},

		methods: {
			switchAutoLogin(){
				this.isAutoLogin = !this.isAutoLogin
				if(this.isAutoLogin){
					this.isSavePassword = true
				}
			},
			switchSavePassword(){
				this.isSavePassword = !this.isSavePassword
				if(!this.isSavePassword){
					this.isAutoLogin = false
				}
			},
			login(){
				if(this.username.trim()!='' && this.password.trim()!=''){
					uni.request({
						url:getApp().globalData.baseUrl+'getUserInfoByNameAndPwd?',
						data:{
							username:this.username,
							password:this.password
						},
						success: (res) => {
							if(res.data){
								console.log(res.data)
								uni.setStorageSync("loginSetting",{
									isAutoLogin:this.isAutoLogin,
									isSavePassword:this.isSavePassword
								})
								uni.setStorageSync("loginInfo",{
									username:this.username,
									password:this.isSavePassword?this.password:''
								})
								getApp().globalData.user = res.data
								uni.switchTab({
									url:'../home/home'
								})
							}else{
								uni.showToast({
									icon:'none',
									title:'用户名或密码不正确'
								})
							}
						},
						fail() {
							uni.showToast({
								icon:'none',
								title:'登录失败，请检查网络是否连接'
							})
						}
					})
				}else{
					uni.showToast({
						icon:'none',
						title:'请输入用户名和密码后再登录'
					})
				}

			}
		}
	}
</script>

<style>
	page {
		height: 100%;
	}

	.flex_column_center {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}

	.input_box {
		width: 70%;
		border-bottom: 1rpx solid lightgray;
		padding: 5rpx;
	}
</style>