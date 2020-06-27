<template>
    <view>
        <uni-calendar ref="calendar" :insert="false" :lunar="true" :startDate="'2020-01-01'" :endDate="'2025-12-31'"
            :range="true" @confirm="onSelected" />
        <!-- <mx-datepicker :show="showPicker" type="range" @confirm="onSelected" :show-seconds="true" @cancel="onCancel" /> -->
        <view class="cu-bar search bg-white" id="search">
            <view class="padding-left-sm">
                <picker :range="searchType" @change="change">
                    <view style="display: flex;align-items: center;">{{searchType[index]}}
                        <text class="cuIcon-triangledownfill" style="font-size: 16px;height: 16px;padding-left: 1upx;"></text>
                    </view>
                </picker>
            </view>
            <view class="search-form round" v-if="index < 2">
                <text class="cuIcon-search"></text>
                <input @focus="InputFocus" @blur="InputBlur" v-model="search_text" :adjust-position="false" type="text"
                    placeholder="请输入搜索内容" confirm-type="search">
            </view>
            <view class="search-form round flex justify-center" v-else-if="index == 2">
                <view style="border-bottom: 1px solid #d1d1d1;padding-left: 20upx;" @tap="openCalendar">{{startDate}}
                </view>
                <text style="padding: 0 10px;">至</text>
                <view style="border-bottom: 1px solid #d1d1d1;padding-left: 20upx;" @tap="openCalendar">{{endDate}}
                </view>
            </view>
            <view class="action">
                <button class="cu-btn bg-green shadow-blur round" @tap="searchSubmit">搜索</button>
            </view>
        </view>
        <scroll-view id="scroll" scroll-y="true" :style="{height:scrollHeight}">
            <view class="cu-list menu card-menu margin-top-sm">
                <navigator v-for="(item,key) in list" :key="key" class="cu-item" :url="'../detail/detail?id=' + item.id">
                    <view class="content padding-tb-sm">
                        <view>产品名称：{{item.product_name}}</view>
                        <view class="text-gray ">
                            <template v-if="item.state==1">
                                入库数量：{{item.product_num}}
                            </template>
                            <template v-else>
                                出库数量：{{item.product_num}}
                            </template>
                        </view>
                        <view class="text-gray ">
                            操作时间：{{item.add_time}}
                        </view>
                    </view>
                    <view class="action">
                        <template v-if="item.state==1">
                            <view class="cu-tag round bg-green">入库</view>
                        </template>
                        <template v-else>
                            <view class="cu-tag round bg-orange">出库</view>
                        </template>
                    </view>
                </navigator>
            </view>
        </scroll-view>


    </view>
</template>

<script>
    var api = require('@/common/api.js');
    import MxDatepicker from "@/components/mx-datepicker/mx-datepicker.vue";
    import uniCalendar from '@/components/uni-calendar/uni-calendar.vue';
    export default {
        data() {
            return {
                startDate: '开始日期',
                endDate: '结束日期',
                InputBottom: 0,
                index: 0,
                showPicker: false,
                searchType: ['订单号', '产品名称', '日期'],
                scrollHeight: '',
                list: [{
                    product_name: '订单1',
                    product_num: 100,
                    add_time: '2020-06-22'
                }, {
                    product_name: '订单2',
                    product_num: 200,
                    add_time: '2020-06-22'
                },
                {
                    product_name: '订单3',
                    product_num: 300,
                    add_time: '2020-06-22'
                },
                {
                    product_name: '订单4',
                    product_num: 400,
                    add_time: '2020-06-22'
                }],
                search_text: ''
            }
        },
        components: {
            MxDatepicker,
            uniCalendar
        },
        onReady() {
            let _this = this;
            let segmented = uni.createSelectorQuery().select("#search");
            let sysinfo = uni.getSystemInfoSync();
            let Height = sysinfo.windowHeight;
            segmented.boundingClientRect(data => {
                console.log(data);
                let sH = (Height - data.top - 46).toFixed();
                _this.scrollHeight = sH + 'px';
                console.log(_this.scrollHeight);
            }).exec();
        },
        onLoad() {
            api.post({
                url: 'wms/Search/index',
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
            change: function(e) {
                this.index = e.detail.value;
                if (this.index === 2) {
                    this.openCalendar();
                }
            },
            InputFocus(e) {
                this.InputBottom = e.detail.height
            },
            InputBlur(e) {
                this.InputBottom = 0
            },
            onCancel: function() {
                this.showPicker = false
            },
            onSelected(data) {
                if (data.range.after === "") {
                    uni.showToast({
                        title: '温馨提示,请选择正确的时间范围',
                        icon: "none",
                        duration: 1000
                    });
                    return false;
                }
                this.startDate = data.range.before;
                this.endDate = data.range.after;
                console.log(data);
            },
            searchSubmit() {
                let index = this.index;
                let params = {};
                params.type = index; //获取picker的索引值，用于下面判断是日期还是关键字				
                //params.shequ_id = uni.getStorageSync('shequ').shequ_id;
                if (index < 2) { //坑爹啊，判断应该用 == 而不是 ===，否则picker变，搜索框内容不变
                    params.text = this.search_text;
                    //console.log(index);
                    api.post({
                        url: 'wms/Search/find',
                        data: {
                            text: params.text,
                            device_type: api.DeviceType
                        },
                        success: data => {
                            console.log(data);
                            if (data.code == 1) {
                                this.list = data.data;
                            } else {
                                uni.showToast({
                                    title: '订单不存在',
                                    icon: "none",
                                    duration: 1000
                                });
                                this.list = '';
                            }
                        }
                    });
                } else if (index == 2) {
                    params.start_time = this.startDate;
                    params.end_time = this.endDate;
                    api.post({
                        url: 'wms/Search/findDate',
                        data: {
                            start_time: params.start_time,
                            end_time: params.end_time,
                            device_type: api.DeviceType
                        },
                        success: data => {
                            console.log(data);
                            if (data.code == 1) {
                                this.list = data.data;
                            } else {
                                uni.showToast({
                                    title: '订单不存在',
                                    icon: "none",
                                    duration: 1000
                                });
                                this.list = '';
                            }
                        }
                    });
                }
            },
            openCalendar() {
                this.$refs.calendar.open();
            }
        },
        //下拉刷新
        onPullDownRefresh() {
            page = 1
            reachbottom = true
            this.listData = []
            this.getList()
        },
        // 加载更多
        onReachBottom: function() {
            if (reachbottom) {
                this.getList();
            }
        }
    }
</script>

<style>
</style>
