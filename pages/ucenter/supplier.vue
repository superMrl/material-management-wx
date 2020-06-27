<template>
	<view class="padding-top">
		<form class="" @submit="formSubmit">
			<view class="card-menu cu-list menu">
				<view class="cu-item ">
					<view class="content">
						<text class="text-orange cuIcon-roundrightfill"></text>新增供应商
					</view>
				</view>
				<view class="cu-form-group ">
					<view class="title">供应商编号<text class="text-red"></text></view>
					<input placeholder="供应商编号（非必须，后台会自动生成）" name="sn"></input>
					<!-- <button class='cu-btn bg-green shadow'>上传识别</button> -->
				</view>				
				<view class="cu-form-group">
					<view class="title">供应商名称<text class="text-red">*</text></view>
					<input placeholder="请输入供应商名称" name="name"></input>
				</view>
				<view class="cu-form-group">
					<view class="title">供应商地址<text class="text-red">*</text></view>
					<input placeholder="供应商地址" name="address"></input>
				</view>
				<view class="cu-form-group">
					<view class="title">联系人<text class="text-red">*</text></view>
					<input placeholder="联系人姓名" name="contact"></input>
				</view>
				<view class="cu-form-group">
					<view class="title">邮箱<text class="text-red">*</text></view>
					<input placeholder="邮箱地址" name="email"></input>
				</view>
				<view class="cu-form-group">
					<view class="title">传真<text class="text-red"></text></view>
					<input placeholder="传真" name="fax"></input>
				</view>
				<view class="cu-form-group">
					<view class="title">手机号<text class="text-red">*</text></view>
					<input placeholder="手机号" name="phone"></input>
				</view>			
				<view class="cu-form-group">
					<view class="title">备注信息</view>
					<input placeholder="备注" name="desc"></input>
				</view>

				<view class="cu-form-group ">
					<button class="cu-btn bg-blue shadow" form-type="submit">提交信息</button>
				</view>
			</view>
			
		</form>
	</view>
</template>

<script>
	var api = require('@/common/api.js')	
	export default {
		
		data() {
			return {										
			}
		},
		onLoad() {			
			
		},
		methods: {	
			formSubmit(e){
				var params = e.detail.value;
					console.log(params)
				if (params['name'] == '') {
					uni.showToast({
						title: '名称不能为空',
						duration: 1500,
						icon: "none"
					});
					return false
				}
				if (params['address'] == '') {
					uni.showToast({
						title: '地址不能为空',
						duration: 1500,
						icon: "none"
					});
					return false
				}
				if (params['contact'] == '') {
					uni.showToast({
						title: '联系人不能为空',
						duration: 1500,
						icon: "none"
					});
					return false
				}
				if (params['phone'] == '') {
					uni.showToast({
						title: '电话不能为空',
						duration: 1500,
						icon: "none"
					});
					return false
				}
				api.post({
					url: 'wms/Supplier/save',
					data: {
						sn           : params['sn'],												
						name 	     : params['name'],
						address      : params['address'],
						contact      : params['contact'],
						phone        : params['phone'],
						email        : params['email'],
						fax          : params['fax'],
						desc         : params['desc'],
						device_type : api.DeviceType
					},
					success: data => {
						console.log(data);
						if (data.code == 1) {
							uni.reLaunch({
							url: '../ucenter/ucenter'
						})							
						}
					}
				});				
			}				
		}
	}
</script>

<style lang="scss">
	.evan-form-show {
		padding: 0 30rpx;

		.form-input {
			font-size: 28rpx;
			color: #333;
			text-align: right;
		}

		.form-input-placeholder {
			font-size: 28rpx;
			color: #999
		}

		&__button {
			width: 100%;
			height: 88rpx;
			border-radius: 8rpx;
			display: flex;
			align-items: center;
			justify-content: center;
			padding: 0;
			font-size: 36rpx;
			color: #fff;
			margin-top: 20rpx;
			background-color: #4790EF;

			&::before,
			&::after {
				border: none;
			}
		}

		.customize-form-item {
			&__label {
				font-size: 28rpx;
				color: #333;
				margin-bottom: 16rpx;
			}

			&__radio {
				display: flex;
				align-items: center;
				margin-bottom: 16rpx;

				&__text {
					font-size: 28rpx;
					color: #333;
				}
			}
		}
	}
</style>
