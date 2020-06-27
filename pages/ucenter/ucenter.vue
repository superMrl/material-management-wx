<template>
	<view>
		<view class="header">
			<view class="bg">
				<view class="box">
					<view class="box-hd">
						<view class="avator">
							<image :src="userinfo.avatar ? userinfo.avatar : '../../static/toux.jpg'" @tap="avatar"></image>
						</view>
						<view class="phone-number">当前用户:{{userinfo.user_nickname ? userinfo.user_nickname : "未设置昵称"}}</view>
					</view>
					<view class="box-bd">
						<navigator url="dic" class="item">
							<view class="icon"><img src="../../static/user/product.png"></view>
							<view class="text">基础数据</view>
						</navigator>
						<!-- <navigator url="customer" class="item">
							<view class="icon"><img src="../../static/user/customer.png"></view>
							<view class="text">客户管理</view>
						</navigator>
						<navigator url="supplier" class="item">
							<view class="icon"><img src="../../static/user/supplier.png"></view>
							<view class="text">供应商管理</view>
						</navigator> -->
					</view>
				</view>
			</view>
		</view>
		<view class="list-content">
			<view class="list">
				<view class="li noborder" >
					<view class="icon"><image src="../../static/user/edition.png"></image></view>
					<view class="text">当前版本</view>
					<view class="to">
						<text>v{{version}}</text>
					</view>
				</view>
			</view>
			<view class="list">
				<!--<view class="li " @click="changeSkin">
					<view class="icon"><image src="../../static/user/skin.png"></image></view>
					<view class="text">主题切换</view>
					<image class="to" src="../../static/user/to.png"></image>
				</view>-->
				<navigator url="help_u" class="li " >
					<view class="icon"><image src="../../static/user/help.png"></image></view>
					<view class="text">帮助中心</view>
					<image class="to" src="../../static/user/to.png"></image>
				</navigator>
				<navigator url="about_us" class="li " >
					<view class="icon"><image src="../../static/user/about.png"></image></view>
					<view class="text">关于我们</view>
					<image class="to" src="../../static/user/to.png"></image>
				</navigator>
				<view class="li "  @tap="logout">
					<view class="icon"><image src="../../static/user/exit.png"></image></view>
					<view class="text">退出登录</view>
					<image class="to" src="../../static/user/to.png"></image>
				</view>
			</view>
			<view class="list">
				<view class="li noborder" @click="setting">
					<view class="icon"><image src="../../static/user/set.png"></image></view>
					<view class="text">系统设置</view>
					<image class="to" src="../../static/user/to.png"></image>
				</view>
			</view>
		</view>
	</view>	
</template>
<script>
	var api = require('@/common/api.js');	
	import {version} from 'utils/config.js';
	import tkiQrcode from '@/components/tki-qrcode/tki-qrcode.vue';	
	export default {		
		components: {tkiQrcode},
		data() {
			return {
				userinfo: "",
				avatar: "../../static/toux.jpg",
				version:'1.0.1',
				src: '', // 二维码生成后的图片地址或base64
				foreground: '#000000', // 前景色
				pdground: '#66ccff', // 角标色
				background:'#ffffff',
				shequ:{},
				type:0,
				val:''
			}
		},
		onLoad() {
			// if (this.islogin()) {
			// 	this.userinfo = uni.getStorageSync('user');
			// 	console.log(this.userinfo)
			// } else {
			// 	console.log("未登录");
			// }			
			this.version = version;
			this.shequ = uni.getStorageSync('shequ');
			this.type = uni.getStorageSync('type');
			this.val = "https://www.zgnnet.com/aliapp/shequ?id="+uni.getStorageSync('shequ').shequ_id;
		},		
		methods: {
			qrR(res) {
				this.src = res
			},
			setting() {
				if (this.islogin()) {
					uni.navigateTo({
						url: '/pages/infomation/infomation'
					});
				}
			},
			SelectShequ(){
				uni.$once('shequ_select',function(res){
					if(res.type === 1){
						post(that.ali_app + 'today_data',{id:res.shequ_id,type:3}).then(res=>{
							uni.setStorageSync('shequ',res.data.shequ);
							this.shequ = res.data.shequ;
						});
					}
				});
				uni.navigateTo({
					url:'../index/shequ_list/shequ_list'
				})
			},
			//注销
			logout() {
				var that = this;
				uni.showModal({
					title: '提示',
					content: '确定要退出登录？',
					success: function(res) {
						if (res.confirm) {
							api.post({
								url: 'user/public/logout',
								method: 'POST',
								success: (data) => {
									uni.clearStorageSync()
									that.userinfo = ''
									//强制页面重载，跳转到登录页
									uni.reLaunch({
										url: '../login/login'
									});
								}
							});
						} else if (res.cancel) {
							//console.log('用户点击取消')
						}
					}
				})
			}
		}
	}
</script>

<style lang="scss">
	page{
		background-color:#f1f1f1;
		font-size: 30upx;
	}
	.header{
		background: #fff;
		height: 400upx;
		padding-bottom: 110upx;
		.bg{
			width: 100%;
			height:200upx;
			padding-top:100upx;
			background-color:#66ccff;
		}
	}	
	.box{
		width: 650upx;
		height: 280upx;
		border-radius: 20upx;
		margin: 0 auto;
		background: #fff;
		box-shadow: 0 5upx 20upx 0upx rgba(0, 0, 150, .2); 
		.box-hd{
			display: flex;
			flex-wrap: wrap;
			flex-direction: row;
			justify-content: center;
			.avator{
				width: 160upx;
				height: 160upx;
				background: #fff;
				border: 5upx solid #fff;
				border-radius: 50%;
				margin-top: -80upx;
				overflow: hidden;
				image{
					width: 100%;
					height: 100%;
				}
			}
			.phone-number{
				width: 100%;
				text-align: center;
			}
		}
		.box-bd{
			display: flex;
			flex-wrap: nowrap;
			flex-direction: row;
			justify-content: center;
			.item{
				flex: 1 1 auto;
				display: flex;
				flex-wrap: wrap;
				flex-direction: row;
				justify-content: center;
				border-right: 1px solid #f1f1f1;
				margin: 15upx 0;
				&:last-child{
					border: none;
				}
				.icon{
					width: 60upx;
					height: 60upx;
					img{
						width: 100%;
						height: 100%;
					}
				}
				.text{
					width: 100%;
					text-align: center;
					margin-top: 10upx;
				}
			}
		}
	}
	.list-content{
		background: #fff;		
	}
	.list{
		width:100%;
		border-bottom:15upx solid  #f1f1f1;
		background: #fff;
		&:last-child{
			border: none;
		}
		.li{
			width:92%;
			height:100upx;
			padding:0 4%;
			border-bottom:1px solid rgb(243,243,243);
			display:flex;
			align-items:center;
		&.noborder{
			border-bottom:0
			}
			.icon{
				flex-shrink:0;
				width:50upx;
				height:50upx;
				image{
					width:50upx;
					height:50upx;
				}				
			}
			.text{
				padding-left:20upx;
				width:100%;
				color:#666;				
			}
			.to{
				flex-shrink:0;
				width:40upx;
				height:40upx;
			}
		}
	}	
</style>
