<template>
	<view>
		<view class="face">
			<image :src="userinfo.avatar ? userinfo.avatar : '../../static/toux.jpg'" @tap="avatar"></image>
			<view @tap="avatar" class="title">上传头像</view>
		</view>
		<view class="padding-top">
			<form @submit="formSubmit" @reset="formReset">
				<view class="card-menu cu-list menu">
					<view class="cu-form-group ">
						<view class="title">昵称：<text class="text-red"></text></view>
						<input placeholder="请填写您的昵称" name="nickname" :value="userinfo.user_nickname"></input>
						<!-- <button class='cu-btn bg-green shadow'>上传识别</button> -->
					</view>
					<view class="cu-form-group">
						<view class="title">出生日期：</view>
						<picker @change="bindDateChange" :value="date" mode="date" :start="startDate" :end="endDate" name="date">
							<view class="picker">
								{{date == 0 ? '1990-01-01' : date}}
							</view>
						</picker>
					</view>
					<radio-group class="block" name="sex">
						<view class="cu-form-group">
							<view class="title">性别：</view>
							<view>
								<label v-if="userinfo.sex == 1">
									<radio class='blue radio' value="0" /><text class="padding-left-xs">男</text>
								</label>
								<label v-if="userinfo.sex == 0">
									<radio class='blue radio' value="0" checked="true" /><text class="padding-left-xs">男</text>
								</label>
								<label v-if="userinfo.sex == 1">
									<radio class='blue radio margin-left-sm' checked="true" value="1" /><text class="padding-left-xs">女</text>
								</label>
								<label v-if="userinfo.sex == 0">
									<radio class='blue radio margin-left-sm' value="1" /><text class="padding-left-xs">女</text>
								</label>
							</view>
						</view>
					</radio-group>
					<view class="cu-form-group ">
						<button class="cu-btn bg-blue shadow" form-type="submit">提交</button>
					</view>
			    </view>					
			</form>
		</view>
	</view>
</template>
<script>
	var api = require('@/common/api.js')
	export default {
		data() {
			return {
				userinfo: "",
				date: '',
			}
		},
		computed: {
			startDate() {
				return this.getDate('start');
			},
			endDate() {
				return this.getDate('end');
			}
		},
		onLoad() {
			this.userinfo = uni.getStorageSync('user')
			this.date = this.userinfo.birthday
		},
		methods: {
			avatar() {
				var that = this
				//上传头像
				uni.chooseImage({
					count: 1, //默认9
					sizeType: ['compressed'], //可以指定是原图还是压缩图，默认二者都有
					sourceType: ['camera', 'album'], //从相册选择
					success: function(res) {
						api.uploadFile({
							url: 'user/upload/one',
							filePath: res.tempFilePaths[0],
							name: 'file',
							success: (uploadFileRes) => {
								//console.log(uploadFileRes)
								if (uploadFileRes.code == 0) {
									uni.showToast({
										title: uploadFileRes.msg,
										mask: false,
										duration: 1500
									});
								} else {
									api.post({
										url: 'user/profile/userInfo',
										data: {
											avatar: uploadFileRes.data.url
										},
										success: (data) => {
											console.log(data)
											if (data.code == 1) {
												uni.setStorageSync('upload', 1);
												that.userinfo.avatar = res.tempFilePaths[0]
												uni.showToast({
													title: '修改头像成功',
													icon: "success",
													duration: 2000
												})
											}
											if (data.code == 0) {
												uni.showToast({
													title: '修改头像失败',
													icon: "none",
													duration: 2000
												})
											}
										}
									})
								}
							}
						})
					}
				})
			},
			formSubmit: function(e) {
				var value = e.detail.value
				var info = this.userinfo
				if (value.nickname == "") {
					uni.showToast({
						title: '昵称不能为空',
						duration: 1500,
						icon: "none"
					});
					return
				}
				if (value.nickname == info.user_nickname && value.date == info.birthday && value.sex == info.sex) {
					uni.showToast({
						title: '信息无变化',
						duration: 1500,
						icon: "none"
					});
					return
				}
				api.post({
					url: 'user/profile/userInfo',
					data: {
						user_nickname: value.nickname,
						birthday: this.date,
						sex: value.sex
					},
					success: (data) => {
						console.log(data)
						if (data.code == 1) {
							uni.setStorageSync('upload', 1);							
							uni.showToast({
								title: data.msg,
								icon: "success",
								duration: 500
							})
							//强制页面重载，跳转到首页
							uni.reLaunch({
								url: '../ucenter/ucenter'								
							});
							setTimeout((e => {
								uni.navigateBack();
							}), 500);
						}
						if (data.code == 0) {
							uni.showToast({
								title: data.msg,
								icon: "none",
								duration: 1000
							})
						}
					}
				})
			},
			bindDateChange: function(e) {
				this.date = e.target.value
			},
			getDate(type) {
				var date = new Date();

				let year = date.getFullYear();
				let month = date.getMonth() + 1;
				let day = date.getDate();

				if (type === 'start') {
					year = year - 60;
				} else if (type === 'end') {
					year = year + 2;
				}
				month = month > 9 ? month : '0' + month;;
				day = day > 9 ? day : '0' + day;

				return `${year}-${month}-${day}`;
			}
		}
	}
</script>

<style>
	page {
		background-color: #f1f1f1
	}

	.face {
		text-align: center;
		padding-top: 60upx;
	}

	.face image {
		width: 150upx;
		height: 150upx;
		border-radius: 100%
	}
</style>
