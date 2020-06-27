<template>
    <view>
        <scroll-view id="scroll" scroll-y="true" :style="{height:scrollHeight}">
            <view class="cu-list menu card-menu margin-top-sm">
                <navigator v-for="(item,key) in list" :key="key" class="cu-item" :url="'../ucenter/dic_detail?id=' + item.id">
                    <view class="content padding-tb-sm">
                        <view>{{item.dic_name}}</view>
                    </view>
                    <view class="action">
                        <view class="cu-tag round bg-blue">详情</view>
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
                    id:1,
                    dic_name: '品牌'
                    
                }, {
                    id:2,
                   dic_name: '计量单位'
                },
                {
                    id:3,
                    dic_name: '商品分类'
                },
                {
                    id:4,
                    dic_name: '库位'
                }]
                
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
