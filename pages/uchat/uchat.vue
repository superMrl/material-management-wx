<template>
    <view>
        <view class="qiun-columns" id="report">
            <view class="qiun-bg-white qiun-title-bar qiun-common-mt">
                <view class="qiun-title-dot-light" style="text-align:center;font-size:12px;">一周出/入库量</view>
            </view>
            <view class="qiun-charts">
                <canvas canvas-id="canvasColumn" id="canvasColumn" class="charts" @touchstart="touchColumn"></canvas>
            </view>
        </view>
        <view class="qiun-title-dot-light" style="text-align:center;font-size:12px;margin-top:8px;">库存(库存总值：{{total}}元)</view>
        <scroll-view id="scroll" scroll-y="true" :style="{height:scrollHeight}">
            <view class="cu-list menu card-menu margin-top-sm">
                <view v-for="(item,key) in list" :key="key" class="cu-item" :url="'../detail/detail?id=' + item.id">
                    <view class="content padding-tb-sm">
                        <view>产品名称：{{item.name}}</view>
                        <view class="text-blue">
                            库存数量：{{item.num}} {{item.unit}}
                        </view>
                        <view class="text-gray ">
                            入库单价：{{item.price}}
                        </view>
						
                    </view>
                    <view class="action">
                        <template v-if="item.num<100">
                            <view class="cu-tag round bg-red">紧缺</view>
                        </template>
                        <template v-else>
                            <view class="cu-tag round bg-green">充足</view>
                        </template>
                    </view>
                </view>
            </view>
        </scroll-view>
    </view>
</template>

<script>
    import uCharts from '../../components/u-charts/u-charts.js';
    var api = require('@/common/api.js');
    var _self;
    var canvaColumn = null;
    export default {
        data() {
            return {
                cWidth: '',
                cHeight: '',
                pixelRatio: 1,
                serverData: '',
                scrollHeight: '',
                list: [{
                        name: '黄金叶',
                        num: 200,
                        price: 10,
						unit:'箱'
                    },
                    {
                        name: '黄金叶',
                        num: 100,
                        price: 5,
						unit:'条'
                    },
                    {
                        name: '黄金3',
                        num: 50,
                        price: 10,
						unit:'g'
                    },
                    {
                        name: '黄金叶',
                        num: 270,
                        price: 10,
						unit:'包'
                    }
                ],
                total: '1000'
            }
        },
        onReady() {
            let _this = this;
            let segmented = uni.createSelectorQuery().select("#report");
            let sysinfo = uni.getSystemInfoSync();
            let Height = sysinfo.windowHeight;
            segmented.boundingClientRect(data => {
                console.log(data);
                let sH = (Height - data.top - 650).toFixed(); //这里减去650就是需要固定的高度
                _this.scrollHeight = sH + 'px';
                console.log(_this.scrollHeight);
            }).exec();
        },
        onLoad() {
            _self = this;
            this.cWidth = uni.upx2px(750);
            this.cHeight = uni.upx2px(500);
            this.getServerData();
            api.post({
                url: 'wms/Stock/index',
                data: {
                    device_type: api.DeviceType
                },
                success: data => {
                    console.log(data);
                    if (data.code == 1) {
                        this.list = data.data.list;
                        this.total = data.data.total;
                    }
                }
            });
        },
        methods: {
            getServerData() {
                // api.post({
                //     //url: 'https://www.ucharts.cn/data.json',
                //     url: 'wms/Search/chartData',
                //     data: {
                //         device_type: api.DeviceType
                //     },
                //     success: function(res) {
                //         //console.log(res.data)
                //         //下面这个根据需要保存后台数据，我是为了模拟更新柱状图，所以存下来了
                //         _self.serverData = res.data;
                //         let Column = {
                //             categories: [],
                //             series: []
                //         };
                //         //这里我后台返回的是数组，所以用等于，如果您后台返回的是单条数据，需要push进去
                //         Column.categories = res.data.days;
                //         console.log(Column.categories)
                //         //Column.series=res.data.counts;
                //         //这里果然是需要用push的，因为返回的counts只有一条数据
                //         Column.series.push(res.data.counts);
                //         //console.log(Column.series)
                //         _self.showColumn("canvasColumn", Column);
                //     },
                //     fail: () => {
                //         _self.tips = "网络错误，小程序端请检查合法域名";
                //     },
                // });
                //使用官方数据
                uni.request({
                    url: 'https://www.ucharts.cn/data.json',
                    data: {},
                    success: function(res) {
                        console.log(res.data.data)
                        //下面这个根据需要保存后台数据，我是为了模拟更新柱状图，所以存下来了
                        _self.serverData = res.data.data;
                        let Column = {
                            categories: [],
                            series: []
                        };
                        //这里我后台返回的是数组，所以用等于，如果您后台返回的是单条数据，需要push进去
                        Column.categories = res.data.data.Column.categories;
                        Column.series = res.data.data.Column.series;
                        _self.showColumn("canvasColumn", Column);
                    },
                    fail: () => {
                        _self.tips = "网络错误，小程序端请检查合法域名";
                    },
                });
            },
            showColumn(canvasId, chartData) {
                canvaColumn = new uCharts({
                    $this: _self,
                    canvasId: canvasId,
                    type: 'column',
                    legend: {
                        show: true
                    },
                    fontSize: 11,
                    background: '#FFFFFF',
                    pixelRatio: _self.pixelRatio,
                    animation: true,
                    categories: chartData.categories,
                    series: chartData.series,
                    xAxis: {
                        disableGrid: true,
                    },
                    yAxis: {
                        //disabled:true
                    },
                    dataLabel: true,
                    width: _self.cWidth * _self.pixelRatio,
                    height: _self.cHeight * _self.pixelRatio,
                    extra: {
                        column: {
                            type: 'group',
                            width: _self.cWidth * _self.pixelRatio * 0.45 / chartData.categories.length
                        }
                    }
                });

            },
            touchColumn(e) {
                canvaColumn.showToolTip(e, {
                    format: function(item, category) {
                        if (typeof item.data === 'object') {
                            return category + ' ' + item.name + ':' + item.data.value
                        } else {
                            return category + ' ' + item.name + ':' + item.data
                        }
                    }
                });
            },
        }
    }
</script>

<style>
    page {
        background: #F2F2F2;
        width: 750upx;
        overflow-x: hidden;
    }

    .qiun-padding {
        padding: 2%;
        width: 96%;
    }

    .qiun-wrap {
        display: flex;
        flex-wrap: wrap;
    }

    .qiun-rows {
        display: flex;
        flex-direction: row !important;
    }

    .qiun-columns {
        display: flex;
        flex-direction: column !important;
    }

    .qiun-common-mt {
        margin-top: 10upx;
    }

    .qiun-bg-white {
        background: #FFFFFF;
    }

    .qiun-title-bar {
        width: 96%;
        padding: 10upx 2%;
        flex-wrap: nowrap;
    }

    .qiun-title-dot-light {
        padding-left: 10upx;
        font-size: 32upx;
        color: #000000
    }

    .qiun-charts {
        width: 750upx;
        height: 510upx;
        background-color: #FFFFFF;
    }

    .charts {
        width: 750upx;
        height: 510upx;
        background-color: #FFFFFF;
    }
</style>
