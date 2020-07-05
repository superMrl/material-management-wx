<template>
	<view>
		<view class="cu-bar bg-white margin-top">
			<!-- <view class="action">
                <text class="cuIcon-title text-orange "></text> 对话窗口
            </view> -->
			<navigator url="product">
				<view class="action">
					<button class="cu-btn round bg-green shadow">新增</button>
				</view>
			</navigator>
		</view>

		<scroll-view id="scroll" scroll-y="true" :style="{height:scrollHeight}">
			<view class="cu-list menu card-menu margin-top-sm">
				<view v-for="(item,key) in list" :key="id" class="cu-item">
					<view class="content padding-tb-sm">
						<view>{{item.materialName}}</view>
						<view>{{item.unitNum}}</view>
					</view>
					<view class="action">
						<!-- <navigator class="cu-tag round bg-green" @tap="editDeail">
							<view >编辑</view>
						</navigator> -->
						<view class="cu-tag round bg-green" @tap="editDeail(list[key])">编辑</view>
						<view class="cu-tag round bg-red">删除</view>
					</view>

				</view>
			</view>
		</scroll-view>
	</view>
</template>



<script>
	var api = require('@/common/api.js')
	export default {
		data() {
			return {
				scrollHeight: '',
				list: [],
				search_text: '',
				idDetail: '',
				dictMap:''
			}
		},
		onShow() {
			//页面显示的时候
			this.getProductList();
			this.getDictList();
		},
		methods: {
			//        showModal() {
			// // uni.reLaunch({
			// // 	url: '../ucenter/dic_detail_add_or_update'
			// // });
			// uni.navigateTo({
			// 	url: '../ucenter/dic_detail_add_or_update',
			// 	success: res => {},
			// 	fail: () => {},
			// 	complete: () => {}
			// });

			//        },
			editDeail(e) {
				// uni.navigateTo({
				// 	url :"../ucenter/dic_detail_add_or_update?item=" + encodeURIComponent(detail);
				// });
				let detail = JSON.stringify(e);
				console.log(detail)
				uni.navigateTo({
					url: "../ucenter/product?item=" + detail
				})
			},
			getProductList() {
				api.post({
					url: 'product/list',
					data: {
						companyId:'0'	
					},
					success: res => {
						if (res.success == true) {
							this.list = res.data;
						} else {
							uni.showToast({
								duration: 1500,
								icon: 'none',
								title: res.msg
							});
						}
					},
					fail: function(res) {
						uni.showToast({
							duration: 1500,
							icon: 'none',
							title: "服务连接失败"
						});
					}
				});
			},
			getDictList(){
				api.post({
					url: 'dict/material',
					data: {
						companyId:'0'	
					},
					success: res => {
						if (res.success == true) {
							if(res.data != null){
								var temp = JSON.parse(res.data);
								console.log(temp);
								
							}
						} else {
							uni.showToast({
								duration: 1500,
								icon: 'none',
								title: res.msg
							});
						}
					},
					fail: function(res) {
						uni.showToast({
							duration: 1500,
							icon: 'none',
							title: "服务连接失败"
						});
					}
				});
			},
			hideModal() {
				this.modalName = '0';
			}
		}


	}
</script>

<style>


</style>
