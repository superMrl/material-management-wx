<template>
	<view class="main">
		<!--<cover-image src = "../../static/bg.png"></cover-image>-->
		<view class="bghead"></view>
		<view class="main">
			<!--左侧小区名称 右侧新增登记-->
			<view id="head1" class="flex justify-around margin-top-sm ">
				<navigator url="../ucenter/product_detail" class="flex align-center">					
					<view class="cuIcon-peoplelist text-white" style="font-size: 50upx"></view>
				</navigator>
				<view></view><view></view><view></view><view></view>
				<navigator url="../ucenter/dic" class="flex align-center">
					<view>
						<view class="cuIcon-group text-white text-bold" style="font-size: 50upx"></view>
					</view>
				</navigator>
			</view>
			<!--社区出行概况-->
			<view id="chuxing" class="flex  justify-center align-center margin-top-sm">
				<view class="head_box">
	
					<view class="flex justify-around text-df padding-top">
						<text>今日出入库情况</text>
						<text>（单位：元）</text>
					</view>
					<view class="flex justify-around padding-top-xl">
						<view class="flex flex-direction align-center">
							<text class="text-orange text-bold">{{today.totalinstorage}}元</text>
							<text>今日入库</text>
						</view>
						<view class="flex flex-direction align-center">
							<text class="text-orange text-bold">{{today.totaloutstorage}}元</text>
							<text>今日出库</text>
						</view>
						<!-- <view class="flex flex-direction align-center">
							<text class="text-orange text-bold">{{today.totalprofit}}元</text>
							<text>今日利润</text>
						</view> -->
					</view>
				</view>
			</view>
			<!--两个功能按钮-->
			<view id="btn" class="flex justify-around margin-top-sm">
				<navigator class="btn-box bg-gradual-blue flex align-center justify-center"  @tap="instorage">
					<text class="text-xxl text-shadow">入库登记</text>
					<text class="cuIcon-cart padding-left-xs" style="font-size: 90upx;opacity: 0.2"></text>
				</navigator>
				<view class="btn-box bg-gradual-green flex align-center justify-center" @tap="outstorage">
					<text class="text-xxl text-shadow">出库登记</text>
					<text class="cuIcon-deliver padding-left-xs" style="font-size: 90upx;opacity: 0.2"></text>
				</view>
			</view>
			<!--概况记录-->
			<view id="segmented" class="margin-top-sm">
				<uni-segmented-control :current="current" :values="items" style-type="text" :active-color="activeColor" @clickItem="onClickItem" />
			</view>
			<!-- class="scroll" -->
			<scroll-view id="scroll" scroll-y = "true" :style="{height:scrollHeight}">
				<view class="cu-list menu card-menu margin-top-sm" >
					<navigator :url="'../detail/detail?id=' + item.id" class="cu-item" v-for="(item,key) in logList" :key = "key">
						<view class="content padding-tb-sm">							
							<view>产品名称：{{item.materialName}}</view>
							<template v-if="status==1">
							<view class="text-gray ">
								入库数量： {{item.inAmount}}
							</view>
							</template>
							<template v-else>
								<view class="text-gray ">
									出库数量： {{item.outAmount}}
								</view>
							</template>
							<template v-if="status==1">
								<view class="text-gray ">
									入库时间： {{item.insertTime}}
								</view>
							</template>
							<template v-else>
								<view class="text-gray ">
									出库时间： {{item.outTime}}
								</view>
							</template>
						</view>
						<view class="action">
							<view class="cu-tag round bg-green">供应商：{{item.supplyerName}}</view>
						</view>
					</navigator>
				</view>
			</scroll-view>			
		</view>		
	</view>
</template>
<script>
	var api = require('@/common/api.js');	
	import uniSegmentedControl from '@/components/uni-segmented-control/uni-segmented-control.vue';
	export default {
		data() {
			return {				
				//初始化数据
				current: 0,
				items: ['入库记录', '出库记录'],
				activeColor: '#66ccff',
				scrollHeight: '',
				today: {
				    totalinstorage: 0, //今日入库
				    totaloutstorage: 0, //今日出库
				    totalprofit: 0 //今日利润
				},
				shequ: {},
				logList: [],
				inStorageList:[],
				outStorageList:[],
				status:1,
				token:''
			}
		},
		components: {uniSegmentedControl},
		onReady() {
			let _this = this;
			let segmented = uni.createSelectorQuery().select("#segmented");
			let sysinfo = uni.getSystemInfoSync();
			let Height = sysinfo.windowHeight;
			segmented.boundingClientRect(data=>{
				// console.log(data);
				let sH = (Height - data.top - 32).toFixed();
				_this.scrollHeight = sH+'px';
				// console.log(_this.scrollHeight);
			}).exec();
			this.loadOutStrorageToday();
			this.loadInStorageToday();	
			
		},
		onLoad() {			
			this.token = uni.getStorageSync('token');
			if(this.token){
				console.log(this.token)
				console.log(api.DeviceType)
				this.loadOutStrorageToday();
				this.loadInStorageToday();
				this.getSumPrice();
			}else{
				console.log('token没有');
			}						
		},		
		//下拉刷新
		onPullDownRefresh() {			
			this.loadInStorageToday();	
			this.loadOutStrorageToday();
			//获取入库金额  出库金额
			this.getSumPrice();
			uni.stopPullDownRefresh();
		},		
		methods: {
			/**
			 * 加载今日数据
			 */
			loadInStorageToday: function() {
				console.log("加载入库信息")
				this.loading = true;
				var sumInstoragePrice = 0;
				api.post({
					url: 'inStorageInfo/selectTodayInStore',
					success: data => {
						console.log(data)
						if (data.code == '000') {
							console.log(data.data);
							this.loading = false;
							this.inStorageList = data.data;
							this.logList = data.data;
							for (var i=0;i<data.data.length;i++) {
								console.log("循环遍历"+data.data[i].instorePrice)
								sumInstoragePrice = sumInstoragePrice + parseFloat(data.data[i].instorePrice)
							}
							this.today.totalinstorage = sumInstoragePrice;
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
			loadOutStrorageToday: function() {
				console.log("加载出库信息")
				this.loading = true;
				var sumOutstoragePrice = 0;
				api.post({
					url: 'outStorageInfo/selectTodayOutStore',
					success: data => {
						console.log(data)
						if (data.code == '000') {
							console.log(data.data);
							this.loading = false;
							this.outStorageList = data.data;
							this.logList = data.data;
							for (var i=0;i<data.data.length;i++) {
								sumOutstoragePrice = sumOutstoragePrice + parseFloat(data.data[i].outPrice)
							}
							this.today.totaloutstorage = sumOutstoragePrice;
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
			//获取今日入库金额  今日出库金额
			getSumPrice(){
				console.log("循环遍历")
				if(this.inStorageList.length != 0){
					
				}
			},
			instorage(){
				uni.navigateTo({
					url:'../instorage/instorage'
				})
			},
			outstorage(){
				uni.navigateTo({
					url:'../outstorage/outstorage'
				})
			},	
			onClickItem(index) {
				//console.log(index)
				if (this.current !== index) {
					this.current = index
				}
				if(index === 0){//入库记录
					this.status = 1;
					this.getList(1)
				}else if(index === 1){ //出库记录
					this.status = 2;
					this.getList(2)
				}
			},				
			/**
			 * 加载文章列表
			 */
			getList(type){
				console.log(type)
				if(type === 1){
					this.loadInStorageToday();
				}else if(type === 2){
					this.loadOutStrorageToday();
				}
			}			
		}
	}
</script>

<style>
	cover-image {
		position: absolute;
		left: 0;
		top: 0;
		height: 300rpx;
		width: 100%;
	}
	.bghead{
		position: absolute;
		left: 0;
		top: 0;
		height: 300rpx;
		width: 100%;
		background-color: #66ccff;
	}	
	.main{
		position: absolute;
		top: 0;width: 100%;
	}
	.head_box{
		width: 700upx;
		height: 220upx;
		background-color: #ffffff;
		border-radius: 15upx;
	}
	.btn-box{
		width: 310upx;
		height: 150upx;
		border-radius: 20upx;
	}
	.scroll{
		height: 700rpx;
	}
	
	.swiper {
		height: 350upx;
	}

	.swiper-item {
		display: block;
		height: 350upx;
		width: 100%;
	}

	.uni-media-list-logo {
		width: 100upx;
		height: 100upx;
	}
	.scan-result {
		min-height: 50upx;
		line-height: 50upx;
	}
	.logbt {
		background: none;
		border: none !important;
		position: fixed;
		display: inline;
		margin-left: -76upx
	}

	.logbt:after {
		border: none !important;
	}

	image {
		width: 100upx;
		height: 100upx;
	}

	.landing {
		height: 84upx;
		line-height: 84upx;
		border-radius: 44upx;
		font-size: 32upx;
		background: linear-gradient(left, #86B5F4, #4790EF);
	}

	.login-btn {
		padding: 10upx 20upx;
		margin-top: 350upx;
	}

	.login-function {
		overflow: auto;
		padding: 20upx 20upx 30upx 20upx;
	}

	.login-forget {
		float: left;
		font-size: 26upx;
		color: #999;
	}

	.login-register {
		color: #666;
		float: right;
		font-size: 26upx;

	}

	.login-input input {
		background: #F2F5F6;
		font-size: 28upx;
		padding: 10upx 25upx;
		height: 62upx;
		line-height: 62upx;
		border-radius: 8upx;
	}

	.login-margin-b {
		margin-bottom: 25upx;
	}

	.login-input {
		padding: 10upx 20upx;
	}

	.login-head {
		font-size: 34upx;
		text-align: center;
		padding: 25upx 10upx 55upx 10upx;
	}

	.login-card {
		background: #fff;
		border-radius: 12upx;
		padding: 10upx 25upx;
		box-shadow: 0 6upx 18upx rgba(0, 0, 0, 0.12);
		position: relative;
		margin-top: 120upx;
	}

	.login-bg {
		height: 260upx;
		padding: 25upx;
		background: linear-gradient(#86B5F4, #4790EF);
	}
</style>
