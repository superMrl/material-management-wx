<template>
	<view>		
		<view class="margin-top-sm" id="search">			
		</view>
		<scroll-view id="scroll" scroll-y = "true" :style="{height:scrollHeight}">
			<view class="cu-list menu card-menu margin-top-sm">
				<view v-for="(item,key) in list" :key="key" class="cu-item">
					<view class="content padding-tb-sm">
						<view>供应商名称：{{item.name}}</view>
						<view class="text-gray ">							 
							 联系人：{{item.contact}}							 
						</view>
						<view class="text-gray ">
							 电话：{{item.phone}}
						</view>
					</view>
					<view class="action">						
						<template v-if="item.status==0">
						<view class="cu-tag round bg-green">正常合作</view>
						</template>
						<template v-else>
						<view class="cu-tag round bg-orange">暂停合作</view>
						</template>						
					</view>
				</view>
			</view>
		</scroll-view>

		
	</view>
</template>

<script>
	var api = require('@/common/api.js');	
	export default {
		data() {
			return {								
				scrollHeight:'',
				list:[]
			}
		},		
		onReady() {
			let _this = this;
			let segmented = uni.createSelectorQuery().select("#search");
			let sysinfo = uni.getSystemInfoSync();
			let Height = sysinfo.windowHeight;
			segmented.boundingClientRect(data=>{
				console.log(data);
				let sH = (Height - data.top - 46).toFixed();
				_this.scrollHeight = sH+'px';
				console.log(_this.scrollHeight);
			}).exec();
		},
		onLoad(){
			api.post({
				url: 'wms/Supplier/index',
				data: {
					device_type: api.DeviceType
				},
				success: data => {
					console.log(data);
					if (data.code == 1) {
						this.list = data.data;												
					}
				}
			});			
		},
		methods: {
			
		}		
	}
</script>

<style>	
</style>
