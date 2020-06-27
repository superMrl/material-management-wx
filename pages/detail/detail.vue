<template>
	<view class="padding-top">
		<form class="" @submit="formSubmit">
			<view class="card-menu cu-list menu">
				<view class="cu-item ">
					<view class="content">
						<text class="text-orange cuIcon-roundrightfill"></text>订单详细资料 <text class="text-gray text-sm padding-left-sm">更新时间：{{List.add_time}}</text>
					</view>
				</view>
			</view>
			<template v-if="List.state==1">
			<view class="card-menu cu-list menu" >
				<view class="cu-item" >
					<view class="content">
						订单类型：
					</view>
					<view class="action">
						{{List.type}}
					</view>
				</view>
				<view class="cu-item" >
					<view class="content">订单号：</view>
					<view class="action">{{List.sn}}</view>
				</view>
				<view class="cu-item" >
					<view class="content">
						制单者：
					</view>
					<view class="action">
						{{List.author}}
					</view>
				</view>
				<view class="cu-item" >
					<view class="content">供应商：</view>
					<view class="action">{{List.supplier}}</view>
				</view>
				<view class="cu-item" >
					<view class="content">产品名称：</view>
					<view class="action">
						{{List.product_name}}
					</view>
				</view>
				<view class="cu-item" >
					<view class="content">入库单价：</view>
					<view class="action">{{List.price}}</view>
				</view>
				<view class="cu-item" >
					<view class="content">入库数量：</view>
					<view class="action">{{List.product_num}}</view>
				</view>	
				<view class="cu-item" >
					<view class="content">备注：</view>
					<view class="action">{{List.desc}}</view>
				</view>
			</view>
			</template>
			<template v-else>
			<view class="card-menu cu-list menu" >
				<view class="cu-item" >
					<view class="content">
						订单类型：
					</view>
					<view class="action">
						{{List.type}}
					</view>
				</view>
				<view class="cu-item" >
					<view class="content">订单号：</view>
					<view class="action">{{List.sn}}</view>
				</view>
				<view class="cu-item" >
					<view class="content">
						制单者：
					</view>
					<view class="action">
						{{List.author}}
					</view>
				</view>
				<view class="cu-item" >
					<view class="content">客户：</view>
					<view class="action">{{List.customer}}</view>
				</view>
				<view class="cu-item" >
					<view class="content">产品名称：</view>
					<view class="action">
						{{List.product_name}}
					</view>
				</view>
				<view class="cu-item" >
					<view class="content">出库单价：</view>
					<view class="action">{{List.price}}</view>
				</view>
				<view class="cu-item" >
					<view class="content">出库数量：</view>
					<view class="action">{{List.product_num}}</view>
				</view>
				<view class="cu-item" >
					<view class="content">本单利润：</view>
					<view class="action">
						<view class="cu-tag round bg-orange light">{{List.profit}}</view>
					</view>
				</view>
				<view class="cu-item" >
					<view class="content">备注：</view>
					<view class="action">{{List.desc}}</view>
				</view>
			</view>
			</template>
		</form>
	</view>
</template>
<script>	
	var api = require('@/common/api.js')
	export default {
		data() {
			return {
				List:{}
			}
		},		
		onLoad(e) {	
			console.log(e)
			var id = e.id		
			api.post({
				url: 'wms/Index/show',
				data: {
					  id: id,
					  device_type: api.DeviceType							
				},
				success: data => {
					console.log(data)								
					if (data.code == 1) {
						this.loading = false;
						this.List = data.data;
						//uni.showToast({
							//duration: 500,
							//title: data.msg
						//});								
					}
					if (data.code == 0) {
						this.loading = false;
						uni.showToast({
							duration: 1500,
							icon: 'none',
							title: data.msg
						});
					}
				}
			});			
		},
		methods: {
			async scan() {
				// #ifdef APP-PLUS
				let status = await this.checkPermission();
				if (status !== 1) {
				    return;
				}
				// #endif
				uni.scanCode({
					success: (res) => {
						this.result = res.result;
						api.post({
							url: 'device/Lists/devinfo',
							data: {
								sbsn: res.result							
							},
							success: data => {								
								if (data.code == 1) {
									this.loading = false;
									this.devinfo = data.data;
									uni.showToast({
										duration: 500,
										title: data.msg
									});								
								}
								if (data.code == 0) {
									this.loading = false;
									uni.showToast({
										duration: 1500,
										icon: 'none',
										title: data.msg
									});
								}
							}
						})
					},
					fail: (err) => {
						// #ifdef MP
						uni.getSetting({
							success: (res) => {
								let authStatus = res.authSetting['scope.camera'];
								if (!authStatus) {
									uni.showModal({
										title: '授权失败',
										content: '需要使用您的相机，请在设置界面打开相关权限',
										success: (res) => {
											if (res.confirm) {
												uni.openSetting()
											}
										}
									})
								}
							}
						})
						// #endif
					}
				});
			}
		}
	}
</script>

<style>
	.scan-result {
		min-height: 50upx;
		line-height: 50upx;
	}
</style>
