<template>
    <view class="padding-top">
        <form class="" @submit="formSubmit">
            <view class="card-menu cu-list menu">
                <view class="cu-item ">
                    <view class="content">
                        <text class="text-orange cuIcon-roundrightfill"></text>{{title}}
                    </view>
                </view>
                <view class="cu-form-group uni-form-item uni-column">
                    <view class="title">{{dicTypeName}}<text class="text-red">*</text>:</view>
                    <input name="name" :value="dict.name"></input>
                </view>

                <!-- <view class="cu-form-group">
					<view class="title">类型: <text class="text-red">*</text></view>
					<picker @change="PickerChange2" :value="index2" :range="picker2">
						<view class="picker">
							{{index2>-1?picker2[index2]:'点击选择'}}
						</view>
					</picker>
				</view> -->

                <!-- <view class="cu-form-group">
					<view class="title">备注信息</view>
					<input placeholder="备注" name="desc"></input>
				</view>
 -->
                <view class="cu-form-group ">
                    <button class="cu-btn bg-blue shadow" form-type="submit">提交信息</button>
                </view>

            </view>

        </form>
    </view>
</template>

<script>
    var api = require('@/common/api.js')
    export default {

        data() {
            return {
                name: '',
                index2: -1,
                picker2: ['物资分类', '计量单位'],
                id: '',
                title: '新增',
                unit: '',
                dict: '',
                dicTypeName: '',
                dicType: ''
            }
        },
        onLoad(options) {
            console.log(options)
            let detail = '';
            if (options.item != '') {
                detail = JSON.parse(options.item);
            }

            this.dicTypeName = options.dicTypeName;
            this.dicType = options.dicType;
            if (detail.id !== undefined) {
                this.id = detail.id;
                this.title = '修改'
                this.dict = detail;
            }
        },
        methods: {
            formSubmit(e) {
                var params = e.detail.value;
                console.log(params);
                // params.category = this.picker[this.index]; //获取选中picker对应index的内容，而不是index本身
                // params.unit = this.picker2[this.index2];
                // params.storage = this.picker3[this.index3];
                // params.location = this.picker4[this.index4];
                // params.supplier = this.picker5[this.index5];
                // if (typeof(params.category) == 'undefined') {
                //     uni.showToast({
                //         title: '产品类别不能为空',
                //         duration: 1500,
                //         icon: "none"
                //     });
                //     return false
                // }
                // if (typeof(params.unit) == 'undefined') {
                //     uni.showToast({
                //         title: '产品单位不能为空',
                //         duration: 1500,
                //         icon: "none"
                //     });
                //     return false
                // }
                if (params['name'] == '') {
                    uni.showToast({
                        title: this.dicTypeName + '不能为空',
                        duration: 1500,
                        icon: "none"
                    });
                    return false
                }
                api.post({
                    url: 'dict/save',
                    data: {
                        id: this.dict == '' ? '' : this.dict.id,
                        name: params['name'],
                        type: this.dict == '' ? this.dicType : this.dict.type
                    },
                    success: res => {
                        if (res.success == true) {
                            uni.navigateBack({
                                delta: 1,
                                url: '../ucenter/dic_detail?type=' + this.dicType +'&dicTypeName='+this.dicTypeName
                            })
                            
                        } else {
                            uni.showToast({
                                duration: 1500,
                                icon: 'none',
                                title: res.msg
                            });
                        }
                    }
                });
            },

            PickerChange2(e) {
                this.index2 = e.detail.value
            },

            getList: function() {
                // api.post({
                // 	url: 'wms/Product/create',
                // 	data: {
                // 		device_type: api.DeviceType
                // 	},
                // 	success: data => {
                // 		console.log(data);
                // 		if (data.code == 1) {
                // 			this.picker = data.data.category;
                // 			this.picker2 = data.data.unit;
                // 			this.picker3 = data.data.storage;
                // 			this.picker4 = data.data.location;
                // 			this.picker5 = data.data.supplier;
                // 		}
                // 	}
                // });
                this.name = "酒水";
                this.index2 = 0

            }
        }
    }
</script>

<style lang="scss">
    .evan-form-show {
        padding: 0 30rpx;

        .form-input {
            font-size: 28rpx;
            color: #333;
            text-align: right;
        }

        .form-input-placeholder {
            font-size: 28rpx;
            color: #999
        }

        &__button {
            width: 100%;
            height: 88rpx;
            border-radius: 8rpx;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 0;
            font-size: 36rpx;
            color: #fff;
            margin-top: 20rpx;
            background-color: #4790EF;

            &::before,
            &::after {
                border: none;
            }
        }

        .customize-form-item {
            &__label {
                font-size: 28rpx;
                color: #333;
                margin-bottom: 16rpx;
            }

            &__radio {
                display: flex;
                align-items: center;
                margin-bottom: 16rpx;

                &__text {
                    font-size: 28rpx;
                    color: #333;
                }
            }
        }
    }
</style>
