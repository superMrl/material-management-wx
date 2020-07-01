<template>
    <view class="zai-box">
        <image src="../../static/zaizai-login/logo.png" mode='aspectFit' class="zai-logo"></image>
        <view class="zai-title"></view>
        <view class="zai-form">
            <input class="zai-input" placeholder-class v-model="user.userName" placeholder="请输入用户名" />
            <input class="zai-input" placeholder-class v-model="user.telPhone" placeholder="请输入手机号" />
            <input class="zai-input" placeholder-class v-model="user.userPassword" password placeholder="请输入密码" />
            <input class="zai-input" placeholder-class v-model="user.reUserPassword" password placeholder="请再输入一次密码" />
            <button class="zai-btn" @tap="register">立即注册</button>
            <navigator url="../login/login" open-type='navigateBack' hover-class="none" class="zai-label">已有账号，点此去登录.</navigator>
        </view>
    </view>
</template>

<script>
    var api = require('@/common/api.js')
    export default {
        onLoad() {

        },
        data() {
            return {
                loading: false,
                user: {
                    userName: "",
                    telPhone: "",
                    userPassword: "",
                    reUserPassword: ""
                }
            };
        },
        methods: {
            register() {
                this.loading = true;
                if (this.user.userName == '') {
                    uni.showToast({
                        icon: 'none',
                        title: '请输入用户名'
                    });
                    this.loading = false;
                    return;
                }
                if (this.user.telPhone == '') {
                    uni.showToast({
                        icon: 'none',
                        title: '请输入手机号'
                    });
                    this.loading = false;
                    return;
                } else {
                    //验证手机号先注释
                    // var reg = /^1[3456789]\d{9}$/;
                    // if (!reg.test(value)) {
                    //     uni.showToast({
                    //         icon: 'none',
                    //         title: '请输入有效的手机号码'
                    //     });
                    //     this.loading = false;
                    //     return;
                    // }
                }
                if (this.user.userPassword == '') {
                    uni.showToast({
                        icon: 'none',
                        title: '请输入密码'
                    });
                    this.loading = false;
                    return;
                }
                if (this.user.reUserPassword == '') {
                    uni.showToast({
                        icon: 'none',
                        title: '请再输入一次密码'
                    });
                    this.loading = false;
                    return;
                }
                if (this.user.userPassword !== this.user.reUserPassword) {
                    uni.showToast({
                        icon: 'none',
                        title: '两次密码输入不一致'
                    });
                    this.loading = false;
                    return;
                }
                api.post({
                    url: 'user/register',
                    data: JSON.stringify(this.user),
                    success: data => {
                        if (data.code == '000') {
                            this.loading = false;
                            //console.log(data);
                            uni.showToast({
                                duration: 500,
                                icon: 'success',
                                title: data.msg
                            });
                            //强制页面重载，跳转到登录页面
                            this.user = {};
                            uni.reLaunch({
                                url: '../login/login'
                            });
                            // uni.setStorageSync('upload', 1)
                            // uni.setStorageSync('login', 1)
                            // uni.setStorageSync('token', data.data.token)
                            // uni.setStorageSync('user', data.data.user)
                            setTimeout((e => {
                                uni.navigateBack();
                            }), 500);
                        }else{
                           this.loading = false;
                           uni.showToast({
                               duration: 1500,
                               icon: 'none',
                               title: data.msg
                           }); 
                        }
                    }
                })
            }
        }
    }
</script>

<style>
    .zai-box {
        padding: 0 100upx;
        position: relative;
    }

    .zai-logo {
        width: 100%;
        width: 100%;
        height: 310upx;
    }

    .zai-title {
        position: absolute;
        top: 0;
        line-height: 360upx;
        font-size: 68upx;
        color: #fff;
        text-align: center;
        width: 100%;
        margin-left: -100upx;
    }

    .zai-form {
        margin-top: 200upx;
    }

    .zai-input {
        background: #e2f5fc;
        margin-top: 30upx;
        border-radius: 100upx;
        padding: 20upx 40upx;
        font-size: 36upx;
        height: 36px;
    }

    .input-placeholder,
    .zai-input {
        color: #94afce;
    }

    .zai-label {
        padding: 60upx 0;
        text-align: center;
        font-size: 30upx;
        color: #a7b6d0;
    }

    .zai-btn {
        margin-top: 30upx;
        background: #66ccff;
        color: #fff;
        border: 0;
        border-radius: 100upx;
        font-size: 36upx;
    }

    .zai-btn:after {
        border: 0;
    }

    /*按钮点击效果*/
    .zai-btn.button-hover {
        transform: translate(1upx, 1upx);
    }
</style>
