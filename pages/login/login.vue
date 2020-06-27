<template>	
	<view class="zai-box">
		<image src="../../static/zaizai-login/logo.png" mode='aspectFit' class="zai-logo"></image>
		<view class="zai-title"></view>
		<view class="zai-form">
			<input class="zai-input" type="number" v-model="username" placeholder-class placeholder="请输入手机号" />
			<input class="zai-input" :password="true" v-model="password" placeholder-class password placeholder="请输入密码"/>
			<view class="zai-label" @tap="forget">忘记密码？</view>
			<button class="zai-btn" @tap="login">立即登录</button>
			<navigator url="../register/register" hover-class="none" class="zai-label">还没有账号？点此注册.</navigator>
		</view>
	</view>
</template>

<script>
	var api = require('@/common/api.js')
	export default {
		onLoad(e) {
			if (e.token) {
				uni.showLoading()
				//console.log(e.token);
				uni.setStorageSync('upload', 1)
				uni.setStorageSync('login', 1)
				uni.setStorageSync('token', e.token)
				uni.showToast({
					duration: 1000,
					title: '登录成功'
				});
				setTimeout((e => {
					uni.hideLoading()
					uni.switchTab({
						url: '../user/user'
						//url: '../index/index'
					});
				}), 1000);
			}
		},
		data() {
			return {
				loading: false,
				username: "",
				password: "",
			};
		},
		methods: {
			qqlogin() {
				uni.navigateTo({
					url: '../qqlogin/qqlogin',
					success: res => {},
					fail: () => {},
					complete: () => {}
				});
			},
			login() {
				this.loading = true;
				// if (this.username == '') {
				// 	uni.showToast({
				// 		icon: 'none',
				// 		title: '请输入手机号或邮箱'
				// 	});
				// 	this.loading = false;
				// 	return;
				// }
				// if (this.password == '') {
				// 	uni.showToast({
				// 		icon: 'none',
				// 		title: '请输入密码'
				// 	});
				// 	this.loading = false;
				// 	return;
				// }
                uni.reLaunch({
                	url: '../index/index'
                	//url: '../grid/grid'
                });
				// api.post({
				// 	url: 'user/public/login',
				// 	data: {
				// 		username: this.username,
				// 		password: this.password,
				// 		device_type: api.DeviceType
				// 	},
				// 	success: data => {
				// 		//console.log(data);
				// 		if (data.code == 1) {
				// 			this.loading = false;
				// 			//console.log(data);
				// 			uni.showToast({
				// 				duration: 500,
				// 				icon: 'success',
				// 				title: data.msg
				// 			});
				// 			//强制页面重载，跳转到首页
				// 			uni.reLaunch({
				// 				url: '../index/index'
				// 				//url: '../grid/grid'
				// 			});
				// 			uni.setStorageSync('upload', 1)
				// 			uni.setStorageSync('login', 1)
				// 			uni.setStorageSync('token', data.data.token)
				// 			uni.setStorageSync('user', data.data.user)
				// 			setTimeout((e => {
				// 				uni.navigateBack();
				// 			}), 500);
				// 		}
				// 		if (data.code == 0) {
				// 			this.loading = false;
				// 			uni.showToast({
				// 				duration: 1500,
				// 				icon: 'none',
				// 				title: data.msg
				// 			});
				// 		}
				// 	}
				// })
			},
			reg() {
				uni.navigateTo({
					url: '../register/register'
				});
			},
			forget() {
				uni.showToast({
					duration: 1500,
					icon: 'none',
					title: '请联系系统管理员'
				});
			},
			//小程序登录
			onGotUserInfo() {
				uni.login({
					success: loginRes => {
						if (loginRes.code) {
							uni.getUserInfo({
								withCredentials: true,
								success: res => {
									//console.log(res)
									api.post({
										url: 'wxapp/public/login',
										data: {
											code: loginRes.code,
											encrypted_data: res.encryptedData,
											iv: res.iv,
											raw_data: res.rawData,
											signature: res.signature
										},
										success: data => {
											console.log(data)
											if (data.code == 1) {
												uni.showToast({
													title: '登录成功!',
													icon: 'success',
													duration: 500
												});
												//强制页面重载，跳转到首页
												uni.reLaunch({
													url: '../index/index'
												});
												try {
													uni.setStorageSync('upload', 1)
													uni.setStorageSync('login', 1)
													uni.setStorageSync('token', data.data.token)
													uni.setStorageSync('user', data.data.user)
												} catch (e) {}
												setTimeout(function() {
													uni.navigateBack()
												}, 500)
											}
										}
									});
								},
								fail: (res) => {
									if (res.errMsg == "getUserInfo:cancel" || res.errMsg == "getUserInfo:fail auth deny") {
										uni.showModal({
											title: '用户授权失败',
											showCancel: false,
											content: '请删除此小程序重新授权!',
											success: function(res) {
												if (res.confirm) {
													console.log('用户点击确定')
												}
											}
										})
									}

								}
							})
						}
					}
				})
			}
		}
	}
</script>

<style>
	.zai-box{
		padding: 0 100upx;
		position: relative;
	}
	.zai-logo{
		width: 100%;
		width: 100%;
		height: 310upx;
	}
	.zai-title{
		position: absolute;
		top: 0;
		line-height: 360upx;
		font-size: 68upx;
		color: #fff;
		text-align: center;
		width: 100%;
		margin-left: -100upx;
	}
	.zai-form{
		margin-top: 300upx;
	}
	.zai-input{
		background: #e2f5fc;
		margin-top: 30upx;
		border-radius: 100upx;
		padding: 20upx 40upx;
		font-size: 36upx;
		height: 36px;
	}
	.input-placeholder, .zai-input{
		color: #94afce;		
	}
	.zai-label{
		padding: 60upx 0;
		text-align: center;
		font-size: 30upx;
		color: #a7b6d0;
	}
	.zai-btn{
		background: #66ccff;
		color: #fff;
		border: 0;
		border-radius: 100upx;
		font-size: 36upx;
	}
	.zai-btn:after{
		border: 0;
	}
	/*按钮点击效果*/
	.zai-btn.button-hover{
		transform: translate(1upx, 1upx);
	}

</style>
