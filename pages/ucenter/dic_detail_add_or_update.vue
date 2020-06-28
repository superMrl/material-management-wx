<template>
	<view class="padding-top">
		<form class="" @submit="formSubmit">
			<view class="card-menu cu-list menu">
				<view class="cu-item ">
					<view class="content">
						<text class="text-orange cuIcon-roundrightfill"></text>{{title}}
					</view>
				</view>
				<view class="cu-form-group ">
					<view class="title">分类名称:<text class="text-red">*</text></view>
					<input placeholder="分类名称" name="name"></input>					
				</view>
				
				<view class="cu-form-group">
					<view class="title">类型: <text class="text-red">*</text></view>
					<picker @change="PickerChange2" :value="index2" :range="picker2" name="unit">
						<view class="picker">
							{{index2>-1?picker2[index2]:'点击选择'}}
						</view>
					</picker>
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
				picker2: ['物资分类','计量单位'],
				index3:-1,
				picker3: [],
				index4:-1,
				picker4: [],
				index5:-1,
				picker5: [],
				id:'',
				title:'新增'
											
			}
		},
		onLoad(e) {		
			console.log(e.id)
			console.log( e != undefined)
			if(e != null || e != '' || e != 'undefined' || e != undefined){
				this.id = e.id;
				this.title = '修改'
			}
			//this.loadPicker();		
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
