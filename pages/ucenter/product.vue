<template>
	<view class="padding-top">
		<form class="" @submit="formSubmit">
			<view class="card-menu cu-list menu">
				<view class="cu-item ">
					<view class="content">
						<text class="text-orange cuIcon-roundrightfill"></text>添加产品类目
					</view>
				</view>
				<view class="cu-form-group ">
					<view class="title">产品编号</view>
					<input placeholder="产品编号（非必须，后台会自动生成）" name="sn"></input>
					<!-- <button class='cu-btn bg-green shadow'>上传识别</button> -->
				</view>
				<view class="cu-form-group ">
					<view class="title">产品名称<text class="text-red">*</text></view>
					<input placeholder="产品名称" name="name"></input>					
				</view>
				<view class="cu-form-group ">
					<view class="title">厂家编号</view>
					<input placeholder="产品出厂编号（非必须，后台会自动生成）" name="cjsn"></input>
					<!-- <button class='cu-btn bg-green shadow'>上传识别</button> -->
				</view>
				<view class="cu-form-group ">
					<view class="title">内部编号</view>
					<input placeholder="内部编号" name="nbsn"></input>					
				</view>
				<view class="cu-form-group">
					<view class="title">产品类别<text class="text-red">*</text></view>
					<picker @change="PickerChange" :value="index" :range="picker" name="category">
						<view class="picker">
							{{index>-1?picker[index]:'点击选择'}}
						</view>
					</picker>
				</view>
				<view class="cu-form-group">
					<view class="title">计量单位 <text class="text-red">*</text></view>
					<picker @change="PickerChange2" :value="index2" :range="picker2" name="unit">
						<view class="picker">
							{{index2>-1?picker2[index2]:'点击选择'}}
						</view>
					</picker>
				</view>
				<view class="cu-form-group">
					<view class="title">仓库 <text class="text-red">*</text></view>
					<picker @change="PickerChange3" :value="index3" :range="picker3" name="storage">
						<view class="picker">
							{{index3>-1?picker3[index3]:'点击选择'}}
						</view>
					</picker>
				</view>
				<view class="cu-form-group">
					<view class="title">库位 <text class="text-red">*</text></view>
					<picker @change="PickerChange4" :value="index4" :range="picker4" name="location">
						<view class="picker">
							{{index4>-1?picker4[index4]:'点击选择'}}
						</view>
					</picker>
				</view>
				<view class="cu-form-group">
					<view class="title">供应商 <text class="text-red">*</text></view>
					<picker @change="PickerChange5" :value="index5" :range="picker5" name="supplier">
						<view class="picker">
							{{index5>-1?picker5[index5]:'点击选择'}}
						</view>
					</picker>
				</view>
				<view class="cu-form-group">
					<view class="title">产品规格</view>
					<input placeholder="产品规格" name="spec"></input>
				</view>
				<view class="cu-form-group">
					<view class="title">产品价格<text class="text-red">*</text></view>
					<input placeholder="入库价格" name="price"></input>
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
				picker: [],
				index2:-1,
				picker2: [],
				index3:-1,
				picker3: [],
				index4:-1,
				picker4: [],
				index5:-1,
				picker5: [],							
			}
		},
		onLoad(e) {		
			this.loadPicker();		
		},
		methods: {	
			formSubmit(e){
				var params = e.detail.value;				
				console.log(params);
				params.category = this.picker[this.index]; //获取选中picker对应index的内容，而不是index本身
				params.unit = this.picker2[this.index2];
				params.storage = this.picker3[this.index3];
				params.location = this.picker4[this.index4];
				params.supplier = this.picker5[this.index5];											
				if(typeof(params.category)=='undefined'){
					uni.showToast({
						title: '产品类别不能为空',
						duration: 1500,
						icon: "none"
					});
					return false
				}
				if(typeof(params.unit)=='undefined'){
					uni.showToast({
						title: '产品单位不能为空',
						duration: 1500,
						icon: "none"
					});
					return false
				}
				if (params['name'] == '') {
					uni.showToast({
						title: '产品名称不能为空',
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
					url: 'wms/Product/save',
					data: {
						sn           : params['sn'],
						nbsn 		 : params['nbsn'],
						cjsn 		 : params['cjsn'],						
						name 	     : params['name'],
						category     : params['category'],
						storage      : params['storage'],
						location     : params['location'],
						unit         : params['unit'],
						supplier     : params['supplier'],
						spec         : params['spec'],
						price        : params['price'],
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
			PickerChange4(e) {
				this.index4 = e.detail.value;				
			},
			PickerChange5(e) {
				this.index5 = e.detail.value;				
			},
			loadPicker: function() {
				api.post({
					url: 'wms/Product/create',
					data: {
						device_type: api.DeviceType
					},
					success: data => {
						console.log(data);
						if (data.code == 1) {
							this.picker = data.data.category;
							this.picker2 = data.data.unit;
							this.picker3 = data.data.storage;
							this.picker4 = data.data.location;
							this.picker5 = data.data.supplier;
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
