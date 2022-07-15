<template>
	<view class="root-padding">

		<view class="login-mask" @click="login"></view>

		<!-- 搜索框 -->
		<uni-search-bar radius="10" placeholder="搜索自己喜欢的内容" clearButton="auto" @confirm="search">
			<template v-slot:searchIcon>
					<uni-icons  color="#ff5500" size="18" type="search" />
			</template>
		</uni-search-bar>
		
		<!-- <view class="search">
			<image src="../../static/icon/search.png" class="search-icon"></image>
			<input placeholder="搜索喜欢的内容" placeholder-class="search-input-placeholder" maxlength="20" class="search-input"
			 confirm-type="search" @confirm="searchMe" />
		</view> -->

		<!-- 轮播图 -->
		<swiper :indicator-dots="true" indicator-color="rgba(255, 90, 95,0.3)" indicator-active-color="#ff5a5f" :autoplay="true" current="0" class="swiper" circular="true">
			<swiper-item v-for="swiper in swiperList">
				<navigator open-type="navigate" :url="'../../pages/videoDetail/videoDetail?momentId=' + swiper.momentId">
					<image :src="swiper.coverUrl" mode="aspectFill" class="swiper-image"></image>
				</navigator>
			</swiper-item>
		</swiper>

		<!-- 板块 -->
		<view class="block">
			<view class="block-title">
				最近热门
			</view>
			<view class="block-item-list">
				<view class="block-item" v-for="blockItem in blockList">
					<navigator open-type="navigate" :url="'../../pages/videoDetail/videoDetail?momentId=' + blockItem.momentId">
						<image class="block-item-image" :src="blockItem.coverUrl" mode="aspectFill"></image>
						<view class="block-item-title">
							{{blockItem.title}}
						</view>
						<view class="block-item-desc">
							{{blockItem.desc}}
						</view>
					</navigator>
					
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
				swiperList: [],
				blockList: [],
				searchValue: ''
			}
		},

		async onLoad() {
			// 判断是否登录
			let currentPage = this.getCurrentPage(getCurrentPages());
			let [checkLoginTokenData, checkLoginTokenDataError] = await httpUtils.postJson("/login/checkLoginToken", {},
				currentPage);
			if (checkLoginTokenDataError === "请重新登录") {
				currentPage = encodeURIComponent(currentPage);
				uni.redirectTo({
					url: `/pages/login/login?fromPage=${currentPage}`
				});
				return;
			}

			// 轮播图
			let [tempData1] = await httpUtils.postJson("/index/queryIndexSwiper");
			this.swiperList = tempData1.body;
			
			// 最近热门板块
			let [tempData2] = await httpUtils.postJson("/index/queryIndexBlock");
			this.blockList = tempData2.body;
		},	

		async onPullDownRefresh() {
			this.swiperList = [];
			this.blockList = [];
			// 轮播图
			let [tempData1] = await httpUtils.postJson("/index/queryIndexSwiper");
			this.swiperList = tempData1.body;
			
			// 最近热门板块
			let [tempData2] = await httpUtils.postJson("/index/queryIndexBlock");
			this.blockList = tempData2.body;
			
			
			uni.stopPullDownRefresh();
		},

		async onReachBottom() {
			console.log("触底");
		},

		async onShareAppMessage(res) {
			await httpUtils.postJson("/share/share", {
				pageType: "INDEX"
			});

			let loginUser = uni.getStorageSync("loginUser");
			return {
				title: getApp().globalData.appName,
				path: `/pages/index/index?shareUserId=${loginUser.userId}`
			}
		},

		methods: {
			login() {
				console.log("login");
			},
			search(res) {
				uni.showToast({
					title: '搜索：' + res.value,
					icon: 'none'
				})
			}

		}
	}
</script>

<style>
	/* 搜索框 */
/* 	.search {
		display: flex;
		flex-direction: row;
		margin: 8rpx 0 12rpx 0;
	}

	.search-icon {
		width: 46rpx;
		height: 46rpx;
		margin-left: -8rpx;
	}

	.search-input {
		flex: 1;
		padding-left: 20rpx;
	}

	.search-input:focus {
		padding-left: 20rpx;
	} */

	/* 轮播图 */
	.swiper {
		width: 690rpx;
		height: 388rpx;
		border-radius: 6rpx;
		margin-bottom: 32rpx;
		overflow: hidden;
		border-radius: 20rpx;
	}

	.swiper-image {
		width: 690rpx;
		height: 388rpx;
	}

	/* 板块 */
	.block {
		margin-bottom: 22rpx;
	}

	.block-title {
		font-size: 34rpx;
		font-weight: bold;
		margin: 0 0 32rpx 0;
	}

	.block-item-list {
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		flex-wrap: wrap;
	}

	.block-item {
		width: 337rpx;
		border-radius: 20rpx;
		margin: 0 0 22rpx 0;
	}

	.block-item-image {
		width: 337rpx;
		height: 190rpx;
		border-radius: 2rpx;
	}

	.block-item-title {
		font-size: 29rpx;
		color: #151516;
		line-height: 52rpx;
	}

	.block-item-desc {
		font-size: 24rpx;
		color: #9e9ca5;
	}
</style>
