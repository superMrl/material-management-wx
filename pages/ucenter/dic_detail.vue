<template>
    <view>
        <view class="cu-bar bg-white margin-top">

            <navigator @tap="editDeail('')">
                <view class="action">
                    <button class="cu-btn round bg-green shadow">新增</button>
                </view>
            </navigator>
        </view>
        <scroll-view id="scroll" scroll-y="true" :style="{height:scrollHeight}">
            <view class="cu-list menu card-menu margin-top-sm">
                <view v-for="(item,key) in list" :key="id" class="cu-item">
                    <view class="content padding-tb-sm">
                        <view>{{item.name}}</view>
                    </view>
                    <view class="action">
                        <navigator class="cu-tag round bg-green" @tap="editDeail(list[key])">
                            <view>编辑</view>
                        </navigator>
                        <view class="cu-tag round bg-red" @tap="del(list[key])">删除</view>
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
                scrollHeight: '',
                list: [],
                search_text: '',
                idDetail: '',
                dicTypeName: '',
                dicType: ''
               
            }
        },
        onShow() {
            this.getList();
        },
        onLoad(options) {
            this.dicTypeName = options.dicTypeName;
            this.dicType = options.type;
            this.getList();
        },
        methods: {
            editDeail(item) {
                let detail = '';
                if (item != '') {
                    detail = JSON.stringify(item);
                }
                uni.navigateTo({
                    url: "../ucenter/dic_detail_add_or_update?item=" + detail + "&dicTypeName=" + this.dicTypeName +
                        "&dicType=" + this.dicType
                })
            },
            hideModal() {
                this.modalName = '0';
            },
            getList() {                
                api.post({
                    url: 'dict/list',
                    data: {
                        type: this.dicType
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
            del(item){
                api.post({
                    url: 'dict/del',
                    data: {
                        id: item.id
                    },
                    success: res => {
                        if (res.success == true) {
                            this.getList();
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
            }

        }


    }
</script>

<style>


</style>
