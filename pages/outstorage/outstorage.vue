<template>
	<view class="padding-top">
		<form class="" @submit="formSubmit">
			<view class="card-menu cu-list menu">
				<view class="cu-item ">
					<view class="content">
						<text class="text-orange cuIcon-roundrightfill"></text>产品出库
					</view>
				</view>
				<view class="cu-form-group ">
					<view class="title">订单号<text class="text-red"></text></view>
					<input placeholder="订单号（非必须，后台会自动生成）" name="sn"></input>
					<!-- <button class='cu-btn bg-green shadow'>上传识别</button> -->
				</view>
				<view class="cu-form-group">
					<view class="title">出库类型<text class="text-red">*</text></view>
					<picker @change="PickerChange" :value="index" :range="picker" name="type">
						<view class="picker">
							{{index>-1?picker[index]:'点击选择'}}
						</view>
					</picker>
				</view>
				<view class="cu-form-group">
					<view class="title">客户 <text class="text-red">*</text></view>
					<picker @change="PickerChange2" :value="index2" :range="picker2" name="customer">
						<view class="picker">
							{{index2>-1?picker2[index2]:'点击选择'}}
						</view>
					</picker>
				</view>
				<view class="cu-form-group">
					<view class="title">车牌号</view>
					<input placeholder="请输入车牌号" name="car_no"></input>
				</view>
				<view class="cu-form-group">
					<view class="title">载货清单号</view>
					<input placeholder="载货清单号" name="detailed_no"></input>
				</view>
				<view class="cu-form-group">
					<view class="title">板序号</view>
					<input placeholder="板序号" name="ban_no"></input>
				</view>
				<view class="cu-form-group">
					<view class="title">产品名称<text class="text-red">*</text></view>
					<picker @change="PickerChange3" :value="index3" :range="picker3" name="product"> 
						<view class="picker">
							{{index3>-1?picker3[index3]:'点击选择'}}
						</view>
					</picker>
				</view>
				<view class="cu-form-group">
					<view class="title">出库数量<text class="text-red">*</text></view>
					<input placeholder="出库数量" name="num"></input>
				</view>
				<view class="cu-form-group">
					<view class="title">销售单价<text class="text-red">*</text></view>
					<input placeholder="销售单价" name="price"></input>
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
				bendi:false,
				index:-1,
				picker: ['销售出库', '采购退货'],
				index2:-1,
				picker2: [],
				index3:-1,
				picker3: [],
				user_id:''				
			}
		},
		onLoad(e) {		
			var user = uni.getStorageSync('user');
			if(user){
				console.log(user);
				this.user_id = user.id;
			}
			this.loadPicker();
			
		},
		methods: {	
			formSubmit(e){
				var params = e.detail.value;
				console.log(params);				
				params.customer = this.picker2[this.index2]; //获取选中picker2对应index的内容，而不是index本身
				params.type = this.picker[this.index]; //默认type获取的picker的index值
				params.product = this.picker3[this.index3];				
				params.user_id = this.user_id;
				if(typeof(params.type)=='undefined'){
					uni.showToast({
						title: '出库类型不能为空',
						duration: 1500,
						icon: "none"
					});
					return false
				}
				if(typeof(params['product'])=='undefined'){
					uni.showToast({
						title: '产品不能为空',
						duration: 1500,
						icon: "none"
					});
					return false
				}
				if (params['num'] == '') {
					uni.showToast({
						title: '出库数量不能为空',
						duration: 1500,
						icon: "none"
					});
					return false
				}
				if (params['price'] == '') {
					uni.showToast({
						title: '价格不能为空',
						duration: 1500,
						icon: "none"
					});
					return false
				}
				api.post({
					url: 'wms/Outstorage/checknum',
					data: {
						name        : params.product,
						device_type : api.DeviceType
					},
					success: data => {
						console.log(data);
						if (data.code == 1) {
							if(params.num>data.data[0]){
								uni.showToast({
									title: '出库数不能大于总库存数',
									icon: "none",
									duration: 1000
								})
								return false;
							}else{
								api.post({
									url: 'wms/Outstorage/save',
									data: {
										sn 			: params['sn'],
										type 		: params['type'],
										desc 		: params['desc'],						
										customer 	: params['customer'],
										car_no 		: params['car_no'],
										ban_no 		: params['ban_no'],
										detailed_no : params['detailed_no'],
										product_sn  : params['sn'],
										price       : params['price'],
										product_name: params['product'],
										product_num : params['num'],
										user_id     : params.user_id,
										device_type : api.DeviceType
									},
									success: data => {
										console.log(data);
										if (data.code == 1) {
											uni.reLaunch({
												url:'../index/index'
											})						
										}
									}
								});	
							}																				
						}
					}
				});							
			},
			PickerChange(e) {
				this.index = e.detail.value
			},
			PickerChange2(e) {
				this.index2 = e.detail.value
			},
			PickerChange3(e) {
				this.index3 = e.detail.value;				
			},
			loadPicker: function() {
				api.post({
					url: 'wms/Outstorage/create',
					data: {
						device_type: api.DeviceType
					},
					success: data => {
						//console.log(data);
						if (data.code == 1) {
							this.picker2 = data.data.customer;
							this.picker3 = data.data.product;							
						}
					}
				});
			},
			save() {
				this.$refs.form.validate((res) => {
					if (res) {
						uni.showToast({
							title: '验证通过'
						})
					}
				})
				console.log(this.info.name);
				api.post({
					url: 'device/Apply/apply',
					data: {
						name: this.info.name,
						sblb: this.info.email,
						sydw: this.info.desc,
						addon: this.info.phone,						
					},
					success: (data) => {
						//console.log(data)
						if (data.code == 1) {
							uni.showToast({
								title: data.msg,
								icon: "success",
								duration: 1000
							})
							setTimeout((e => {
								uni.navigateBack();
							}), 1000);
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
