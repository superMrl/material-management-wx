<template>
	<view class="content">
		<view id="title">
			<view class="cu-bar  solid-bottom">
				<view class="action ">
					<text class="cuIcon-friendfill text-blue"></text><text class="title">星星进销存系统说明</text> 
				</view>
			</view>
		</view>
		<view id="connect" class="margin-top-xs">
			<rich-text>系统前端采用uni-app开发，理论上可以编译至小程序和app各个端，目前测试了微信小程序和安卓客户端。后台使用thinkphp5.1和mysql，运行环境推荐“宝塔面板”，要求php7.0以上，mysql5.6以上，添加好网站后记得在nginx里配置一下api的规则。</rich-text>
		</view>
		<view id="connect" class="margin-top-xs">
			<rich-text>系统搭建、调试、运行过程中，如有疑问，请联系</rich-text>
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
