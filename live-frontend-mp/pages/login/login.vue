<template>
	<view class="login-root">
		<image class="login-logo" src="https://s2.loli.net/2022/07/11/RaFYWoIAlfQhvDy.png"
		 mode="aspectFill"></image>
		<view class="login-button-wrapper">
			<view class="login-wechat-icon">
			</view>
			<button class="login-button" @click="getUserProfile">
				微信快速登录
			</button>
		</view>

		<!-- 弹窗 -->
		<view class="popup-mask" v-if="mastOpened">
		</view>
		<view class="popup-area" v-if="mastOpened">
			<view class="popup-content">
				<view class="popup-title">
					微信登录
				</view>
				<view class="popup-desc">
					需要您登录下哦~
				</view>
				<view class="popup-button">
					<view class="popup-cancel" @click="cancelPopup">
						取消
					</view>
					<button class="popup-confirm"  @click="getUserProfile">
						确认
					</button>
				</view>
			</view>
		</view>

	</view>
</template>

<script>
	import httpUtils from '../../common/util/httpUtils.js';

	export default {
		data() {
			return {
				mastOpened: true,
				fromPage: "/pages/index/index"
			}
		},

		async onLoad(options) {
			console.log("登录页options：", options);
			if (options && options.fromPage) {
				this.fromPage = decodeURIComponent(options.fromPage);
			}

			// #ifdef H5
			this.setLoginUser({
				"artistId": 86643,
				"artistName": "awesome",
				"userId": 25,
				"userName": "天",
				"userAvatarUrl": "https://thirdwx.qlogo.cn/mmopen/vi_32/Q0j4TwGTfTI7bQEDmf6wXs5aZO82wlKMC48icPvgJDpfe1ayWv57nLm7QnrcNwlZIMrCCVfiaKT8xSr1XhYkUFZg/132",
				"unionId": "o-6hX60Kzblb4PaUGQcLo_4nupUY",
				"token": "eyJhbGciOiJIUzI1NiJ9.eyJ1bmlvbklkIjoiby02aFg2MEt6YmxiNFBhVUdRY0xvXzRudXBVWSIsImlzcyI6ImNuLmVncmV0IiwiYXJ0aXN0SWQiOjg2NjQzLCJhcnRpc3ROYW1lIjoiYXdlc29tZSIsInVzZXJOYW1lIjoi5aSpIiwiaWF0IjoxNjU3MzcwOTA0NTUyLCJ1c2VySWQiOjI1fQ.-CC4fBh2ozuyvLDc-6-rC-e3KvsHcXQY69VzvclYKrk"
			});
			uni.switchTab({
				url: '../index/index'
			});
			// #endif
		},

		methods: {
			getUserProfile(res) {
				// 登录
				uni.showLoading({
					title: "正在登录..."
				});

				uni.getProvider({
					service: 'oauth',
					success: (res) => {
						console.log("uni.getProvider：", res);
						this.provider = res.provider[0];
						uni.login({
							provider: this.provider,
							success: (res) => {
								console.log("uni.login：", res);
								this.code = res.code;
								}
						})
						uni.getUserProfile({
							desc: '用于完善用户资料',
							lang: 'zh_CN',
							success: (res) => {
								console.log("uni.getUserProfile：", res);
								this.login(this.code, res.userInfo, res.encryptedData, res.iv);
								/* uni.login({
									provider: this.provider,
									success: (res) => {
										console.log("uni.login：", res);
										this.code = res.code;
										// this.login(this.code, res.userInfo, res.encryptedData, res.iv);
									},
									fail: (res) => {
										console.log('出错')
									
									}
								}) */
							},
							fail: (res) => {
								console.log('出错');
								console.log(res);
							}
						})
						/* uni.login({
							provider: this.provider,
							success: (res) => {
								console.log("uni.login：", res);
								this.code = res.code;
								uni.getUserProfile({
									desc: '用于完善用户资料',
									lang: 'zh_CN',
									// provider: this.provider,
									success: (res) => {
										console.log("uni.getUserProfile：", res);
										this.login(this.code, res.userInfo, res.encryptedData, res.iv);
									},
									fail: (res) => {
										console.log(res)
									}
								})
							}
						}); */
					}
				});
			},

			login(code, userInfo, encryptedData, iv) {
				uni.request({
					url: getApp().globalData.domain + "/login/login",
					method: "POST",
					data: {
						randomKey: getApp().globalData.randomKey,
						provider: this.provider,
						code,
						userInfo,
						encryptedData,
						iv
					},
					timeout: 6000,
					dataType: "json",
					success: (res) => {
						console.log('request成功')
						console.log(res)
						if (res.statusCode !== 200) {
							uni.showToast({
								title: "服务器内部错误",
								duration: 500,
							});
							return;
						}
						if (res.data.retcode != 0) {
							uni.showToast({
								title: "接口异常" + res.data.retcode,
								duration: 500,
							});
							return;
						}
						this.setLoginUser(res.data.body);
						uni.hideLoading();
						console.log("跳转到：", this.fromPage);
						if (this.fromPage === "/pages/index/index" || this.fromPage === "/pages/square/square") {
							uni.switchTab({
								url: this.fromPage
							});
						} else {
							uni.redirectTo({
								url: this.fromPage
							});
						}

					},
					fail:(res) => {
						console.log('出错了')
					}
					
				});
			},

			cancelPopup() {
				this.mastOpened = false;
			}
		}
	}
</script>

<style>
	.login-root {
		width: 100%;
		height: 100%;
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.login-logo {
		width: 200rpx;
		height: 200rpx;
		border-radius: 50%;
		margin-top: 76rpx;
	}

	.login-button-wrapper {
		position: relative;
		width: 560rpx;
		height: 100rpx;
		margin-top: 250rpx;
		background-color: #42b133;
		border-radius: 40rpx;
		border: none;
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: center;
	}

	.login-wechat-icon {
		position: absolute;
		left: 106rpx;
		top: 10rpx;
		width: 80rpx;
		height: 80rpx;
		background: url('../../static/icon/wechat-logo.png') no-repeat;
		background-size: 100% 100%;
	}

	.login-button {
		width: 560rpx;
		height: 100rpx;
		padding-left: 70rpx;
		font-size: 34rpx;
		line-height: 100rpx;
		background-color: transparent;
		color: white;
		border: none;
	}

	button:after {
		border: none;
	}

	/* 弹窗 */
	.popup-mask {
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
		background-color: #000000;
		opacity: 0.39;
	}

	.popup-area {
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}

	.popup-content {
		position: relative;
		background-color: #ffffff;
		width: 602rpx;
		border-radius: 12rpx;
	}

	.popup-title {
		font-size: 32rpx;
		font-weight: bold;
		height: 130rpx;
		box-sizing: border-box;
		text-align: center;
		padding-top: 60rpx;
		color: #121212;
	}

	.popup-desc {
		margin-top: 10rpx;
		text-align: center;
		font-size: 32rpx;
		padding-bottom: 172rpx;
		padding-left: 50rpx;
		padding-right: 50rpx;
		display: flex;
		flex-direction: row;
		justify-content: center;
		color: #1C1C1C;
		border-bottom: 2rpx solid #F1F1F1;
	}

	.popup-button {
		position: absolute;
		bottom: 0;
		left: 0;
		display: flex;
		flex-direction: row;
		border-top: 2rpx solid #F1F1F1;
	}

	.popup-cancel {
		font-size: 32rpx;
		width: 300rpx;
		height: 110rpx;
		line-height: 110rpx;
		text-align: center;
		border-right: 2rpx solid #F1F1F1;
		color: #0B0B0B;
		font-weight: bold;
	}

	.popup-confirm {
		font-size: 32rpx;
		width: 300rpx;
		height: 110rpx;
		line-height: 110rpx;
		text-align: center;
		color: #f6423d;
		font-weight: bold;
		background-color: white;
	}

	.popup-cancel:active,
	.popup-confirm:active {
		background-color: #f0f0f0;
	}
</style>
