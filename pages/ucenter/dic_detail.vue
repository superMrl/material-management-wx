<template>
    <view>
        <view class="cu-bar bg-white margin-top">
            <view class="action">
                <text class="cuIcon-title text-orange "></text> 对话窗口
            </view>
            <view class="action">
                <button class="cu-btn round bg-green shadow" @tap="showModal">新增</button>
            </view>
        </view>
        <view class="cu-modal {{modalName=='1'?'show':''}}">
            <view class="cu-dialog">
                <view class="cu-bar round bg-white justify-end">
                    <view class="content">新增</view>
                    <view class="action" @tap="hideModal">
                        <text class="cuIcon-close text-red"></text>
                    </view>
                </view>
                <view class="padding-xl">
                    <view class="cu-form-group">
                        <view class="title">编码</view>
                        <input placeholder="编码"></input>
                    </view>
                    <view class="cu-form-group">
                        <view class="title">名称</view>
                        <input placeholder="名称"></input>
                    </view>
                </view>
                <view class="cu-bar bg-white justify-end">
                    <view class="action">
                        <button class="cu-btn round line-green text-green" @tap="hideModal">取消</button>
                        <button class="cu-btn round bg-green margin-left" @tap="hideModal">确定</button>
                    </view>
                </view>
            </view>
        </view>
        <scroll-view id="scroll" scroll-y="true" :style="{height:scrollHeight}">
            <view class="cu-list menu card-menu margin-top-sm">
                <navigator v-for="(item,key) in list" :key="key" class="cu-item">
                    <view class="content padding-tb-sm">
                        <view>{{item.product_name}}</view>
                    </view>
                    <view class="action">
                        <view class="cu-tag round bg-green" @tap="showModal">编辑</view>
                        <view class="cu-tag round bg-red">删除</view>
                    </view>
                </navigator>
            </view>
        </scroll-view>


    </view>
</template>

<script>
    export default {
        data() {
            return {
                modalName: '0',
                scrollHeight: '',
                list: [{
                        product_name: '百事可乐'

                    }, {
                        product_name: '可口可乐'

                    },
                    {
                        product_name: '娃哈哈'

                    },
                    {
                        product_name: '茶派'

                    }
                ],
                search_text: ''
            }
        },

        methods: {
            showModal() {

                this.modalName = '1';


            },
            hideModal() {
                this.modalName = '0';
            }
        }


    }
</script>

<style>
</style>
