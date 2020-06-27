<template>
	<view class="content">
		<view id="title">
			<view class="cu-bar  solid-bottom">
				<view class="action ">
					<text class="cuIcon-friendfill text-blue"></text><text class="title">星星进销存</text> 
				</view>
			</view>
		</view>
		<view id="connect" class="margin-top-xs">
			<rich-text>简单的仓储进销存管理系统，能对商品种类、商品库存、采购、货位、入库、出库、盘点等仓储动作和数据进行管理。主要包括wms设置、入库管理、出库管理、库存管理、报表查询、系统管理、用户权限管理等。系统web管理端采用流行的php、mysql设计，前端支持微信小程序、安卓app等场景应用，能胜任基本的进销存管理需求，易于用户进行二次开发。</rich-text>
		</view>
		<view id="connect" class="margin-top-xs">
			<rich-text>需要后台源码，请联系</rich-text>
			<rich-text>星星（开发者）微信：<text style="font-size: 36upx;color:orange;">freshzhou1983</text></rich-text>
		</view>
	</view>
</template>

<script>
	var api = require('@/common/api.js');
	export default {
		data() {
			return {
				nodes:'',
				title:''
			}
		},
		onLoad() {
			
		},
		methods: {
			
		}
	}
</script>

<style>
	page{
		height: 100%;
		background: #fff;
	}
	.content{
		display: flex;
		flex-direction: column;
		padding: 20upx 30upx;
		background: #fff;
		line-height: 1.5;
	}
</style>
