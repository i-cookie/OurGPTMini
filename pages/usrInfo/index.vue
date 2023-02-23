<template>
	<view class="my-info" @tap="openMyInfo">
		<view class="my-head">
			<image style="width: 150rpx; height: 150rpx;" src="../../static/UsrInfo/noWechat.png" mode="aspectFit"></image>
		</view>
		<view class="my-user-name">Cuhk _13$@% SZ</view>
		<view class="my-account-number">1145141919810</view>
		<view class="go-to-my-detail">
			个人信息
			<image style="width: 20rpx; height: 20rpx; position: absolute; top:9rpx; margin-left: 5rpx;" src="../../static/UsrInfo/iconRight.png" mode="aspectFit"></image>
		</view>
	</view>
	<view class="category" @tap="openJoinVip">
		<image class="category-icon" src="../../static/UsrInfo/icon1.png" mode="aspectFit"></image>
		<view class="category-title">加入会员</view>
		<image class="category-detail" src="../../static/UsrInfo/iconRight.png" mode="aspectFit"></image>
	</view>
	<view class="category" @tap="openMyActivities">
		<image class="category-icon" src="../../static/UsrInfo/icon2.png" mode="aspectFit"></image>
		<view class="category-title">我的活动</view>
		<image class="category-detail" src="../../static/UsrInfo/iconRight.png" mode="aspectFit"></image>
	</view>
	<view class="category" @tap="openMyPin">
		<image class="category-icon" src="../../static/UsrInfo/icon3.png" mode="aspectFit"></image>
		<view class="category-title">我的拼拼</view>
		<image class="category-detail" src="../../static/UsrInfo/iconRight.png" mode="aspectFit"></image>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			openMyInfo() {
				uni.navigateTo({
					url: "./subPages/myInfo",
					success: res => {console.log("myInfoOpened")},
					fail: () => {console.log("myInfoNotFound")},
					complete: () => {console.log("done")}
				});
			},
			openJoinVip() {
				uni.navigateTo({
					url: "./subPages/joinVip",
					success: res => {console.log("joinVipOpened")},
					fail: () => {console.log("joinVipNotFound")},
					complete: () => {console.log("done")}
				});
			},
			openMyActivities() {
				uni.navigateTo({
					url: "./subPages/myActivities",
					success: res => {console.log("myActivitiesOpened")},
					fail: () => {console.log("myActivitiesNotFound")},
					complete: () => {console.log("done")}
				});
			},
			openMyPin() {
				uni.navigateTo({
					url: "./subPages/myPin",
					success: res => {console.log("myPinOpened")},
					fail: () => {console.log("myPinNotFound")},
					complete: () => {console.log("done")}
				});
			}
		}
	}
</script>

<style>
	.my-info {
		height: 200rpx;
		padding-left: 30rpx;
		padding-top: 30rpx;
	}
	
	.my-head {
		height: 150rpx;
		width: 150rpx;
		border-radius: 50%;
		border: 3rpx solid, #eee;
		overflow: hidden;
		box-shadow: 0 10rpx 8rpx #d3d3d3;
	}
	
	.my-user-name {
		width: 350rpx;
		height: 40rpx;
		font-size: 35rpx;
		font-weight: 600;
		position: absolute;
		top: 60rpx;
		left: 230rpx;
	}
	
	.my-account-number {
		width: 350rpx;
		height: 20rpx;
		font-size: 22rpx;
		font-weight: 500;
		color: #a9a9a9;
		position: absolute;
		top: 130rpx;
		left: 230rpx;
	}
	
	.go-to-my-detail {
		width: 150rpx;
		height: 40rpx;
		font-size: 25rpx;
		font-weight: 500;
		color: #a9a9a9;
		position: absolute;
		top: 100rpx;
		right: 10rpx;
		text-align: center;
	}
	
	.category {
		margin-left: 30rpx;
		margin-right: 30rpx;
		height: 140rpx;
		position: relative;
		border-bottom: 3rpx solid #d3d3d3;
	}
	
	.category-icon {
		height: 50rpx;
		width: 50rpx;
		position: absolute;
		top: 45rpx;
	}
	
	.category-title {
		height: 50rpx;
		width: 350rpx;
		position: absolute;
		top: 48rpx;
		left: 90rpx;
		font-size: 32rpx;
		font-weight: 350;
		color: dimgrey;
		vertical-align: middle;
	}
	
	.category-detail {
		width: 20rpx; 
		height: 30rpx; 
		position: absolute; 
		top:57rpx;
		right: 0;
		margin-left: 5rpx;
	}
</style>