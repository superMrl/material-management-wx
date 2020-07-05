<template>
	<view class="padding-top">
		<form class="" @submit="formSubmit" @reset="formReset">
			<view class="card-menu cu-list menu">
				<view class="cu-item ">
					<view class="content">
						<text class="text-orange cuIcon-roundrightfill"></text>物资入库
					</view>
				</view>
				<!-- 
				<view class="cu-form-group ">
					<view class="title">订单号<text class="text-red"></text></view>
					<input placeholder="订单号（非必须，后台会自动生成）" name="sn"></input>
				</view>
				-->
				<view class="cu-form-group">
					<view class="title">入库类型<text class="text-red">*</text></view>
					<picker @change="PickerChange" :value="picker[index]"  :range="picker" name="type">
						<view class="picker">
							{{index>-1?picker[index]:'点击选择'}}
						</view>
					</picker>
				</view>
				<view class="cu-form-group">
					<view class="title">物资名称<text class="text-red">*</text></view>
					<picker @change="PickerChange3" :value="picker3[index3]" :range="picker3" name="materialName">
						<view class="picker">
							{{index3>-1?picker3[index3]:'点击选择'}}
						</view>
					</picker>
				</view>
				
				<view class="cu-form-group">
					<view class="title">规格</view>
					<input placeholder="规格" name="specifiction" v-model="specifiction"></input>
				</view>
				
				<view class="cu-form-group">
					<view class="title">入库数量</view>
					<input placeholder="入库数量" v-model="inAmount" name="inAmount"></input>
				</view>
				
				<view class="cu-form-group">
					<view class="title">供应商 <text class="text-red">*</text></view>
					<picker @change="PickerChange2" :value="picker2[index2]" :range="picker2" name="supplier">
						<view class="picker">
							{{index2>-1?picker2[index2]:'点击选择'}}
						</view>
					</picker>
				</view>
				<!-- <view class="cu-form-group">
					<view class="title">车牌号</view>
					<input placeholder="请输入车牌号" name="car_no"></input>
				</view> -->
				<!-- <view class="cu-form-group">
					<view class="title">载货清单号</view>
					<input placeholder="载货清单号" name="detailed_no"></input>
				</view> -->
				<!-- <view class="cu-form-group">
					<view class="title">板序号</view>
					<input placeholder="板序号" name="ban_no"></input>
				</view> -->
				
				<!-- <view class="cu-form-group">
					<view class="title">入库数量<text class="text-red">*</text></view>
					<input placeholder="入库数量" name="num[0]"></input>
				</view>		 -->	
				<!-- <view class="cu-form-group">
					<view class="title">备注信息</view>
					<input placeholder="备注" name="desc"></input>
				</view> -->

				<view class="cu-form-group padding-top">
					<button class="cu-btn bg-blue shadow" form-type="submit">添加</button>
					 <button form-type="reset" style="display:none;">重置</button>\
					<!-- <button class="cu-btn bg-blue shadow" @tap="addInfo">添加</button> -->
				</view>
			</view>
			
		</form>
	
		
		<view class="main">
			<form  @submit="itemSubmit">
			<scroll-view id="scroll" scroll-y = "true" :style="{height:scrollHeight}">
				<view class="cu-list menu card-menu margin-top-sm" >
					<view class="cu-item"  v-for="(item,index) in logList" :key = "index">
						<view class="content padding-tb-sm ">							
							<view>物资名称：{{item.materialName}}</view>
							<view>规格：{{item.specifiction}}</view>
							<!-- <view class="text-gray ">
								
							</view> -->
							<view class="text-gray ">
								入库数量： {{item.inAmount}}
							</view>
							<view class="text-gray">供应商：{{item.supplyerName}}</view>
						</view>
						<view class="action">
							   <button class="mini-btn" type="warn" size="mini" @tap="deleteItemDetail(index)">删除</button>
						</view>
						
					</view>
				</view>
				</scroll-view>	
				<view v-if="logList.length != 0" class="card-menu cu-list menu margin-top-sm">
					<template>
						<button class="cu-btn bg-blue shadow" form-type="submit">入库</button>
					</template>
					
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
				bendi:false,
				index:-1,
				picker: ['采购入库', '销售退货'],
				index2:-1,
				picker2: ['红星','泸州','顺阳'],
				index3:-1,
				picker3: ['娃哈哈','小苹果','小橘子'],
				user_id:'',
				inAmount:'',
				specifiction:'',
				logList:[
					{
					    materialName:'红旗渠1',
					    specifiction:'200',
					    inAmount:'21',
					    supplyerName:'小米集团' 
					}
				]
			}
		},
		onLoad(e) {		
			var user = uni.getStorageSync('user');
			if(user){
				//console.log(user)
				this.user_id = user.id;
			}
			this.loadPicker();			
		},
		methods: {	
			deleteItemDetail(index){
				console.log("删除的索引为:"+index)
				this.logList.splice(index,1);
			},
			itemSubmit(e){
				this.loading = true;
				api.post({
					url: 'inStorageInfo/saveBatch',
					data:JSON.stringify(this.logList) ,
					success: data => {
						if (data.code == '000') {
							console.log(data);
							this.loading = false;
							//console.log(data);
							uni.showToast({
							    duration: 2500,
							    icon: 'success',
							    title: data.msg
							});
							setTimeout((e => {
								uni.reLaunch({
									url:'../index/index'
								});
							}), 2000);
							
						}else{
							this.loading = false;
							uni.showToast({
							    duration: 500,
							    icon: 'none',
							    title: data.msg
							});
						}
							
					}
				});	
			},
			
			formSubmit(e){
				console.log(e);
				var params = e.detail.value;
				params.user_id = this.user_id;
				console.log(params);
				params.supplyerName = this.picker2[this.index2]; //获取选中picker2对应index的内容，而不是index本身
				params.type = this.picker[this.index]; //默认type获取的picker的index值
				params['product[0]'] = this.picker3[this.index3]; //注意这里不能携程params.product[0]，否则获取不到值								
				if(typeof(params.type)=='undefined'){
					uni.showToast({
						title: '入库类型不能为空',
						duration: 1500,
						icon: "none"
					});
					return false
				}
				if(typeof(params['product[0]'])=='undefined'){
					uni.showToast({
						title: '产品不能为空',
						duration: 1500,
						icon: "none"
					});
					return false
				}
				if (params['num[0]'] == '') {
					uni.showToast({
						title: '入库数量不能为空',
						duration: 1500,
						icon: "none"
					});
					return false
				}
				this.logList.push(params);
				console.log(this.logList[0]);
				this.formReset(e);
			},
			
			formReset: function(e) {
				this.index = -1;
				this.index2 = -1;
				this.index3 = -1;
				this.inAmount = '';
				this.specifiction = '';
			    console.log('清空数据')
			},
			PickerChange(e) {
				this.index = e.detail.value
			},
			PickerChange2(e) {
				this.index2 = e.detail.value;
				this.specifiction = '500ml'
			},
			PickerChange3(e) {
				this.index3 = e.detail.value;				
			},
			loadPicker: function() {
				api.post({
					url: 'wms/Instorage/create',
					data: {
						device_type: api.DeviceType
					},
					success: data => {
						console.log(data);
						if (data.code == 1) {
							this.picker2 = data.data.supplyerName;
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
