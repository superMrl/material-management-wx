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
                        <view>{{item.product_name}}</view>
						<view>{{item.produc_specfiction}}</view>
                    </view>
                    <view class="action">
						<navigator class="cu-tag round bg-green" @tap="editDeail(item)">
							<view >编辑</view>
						</navigator>
                        <view class="cu-tag round bg-red">删除</view>
                    </view>
                
				</view>
            </view>
        </scroll-view>
    </view>
</template>



<script>
    export default {
        data() {
            return {
                scrollHeight: '',
                list: [{
						id:'1',
                        product_name: '哇呵呵',
						produc_specfiction:'500ml'

                    },{
						id:'2',
                        product_name: '矿泉水',
						produc_specfiction:'500ml'

                    },
                    {
						id:'3',
                        product_name: '农夫山泉',
						produc_specfiction:'500ml'

                    },
                    {
						id:'4',
                        product_name: '茶派',
						produc_specfiction:'500ml'

                    }
                ],
                search_text: '',
				idDetail:''
            }
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
			editDeail(item){
				// uni.navigateTo({
				// 	url :"../ucenter/dic_detail_add_or_update?item=" + encodeURIComponent(detail);
				// });
				debugger
				let detail = JSON.stringify(item);
				console.log(detail)
				uni.navigateTo({
					url: "../ucenter/product?item=" + detail
				})
			},
            hideModal() {
                this.modalName = '0';
            }
        }


    }
</script>

<style>


</style>
