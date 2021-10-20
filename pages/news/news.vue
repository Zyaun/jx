<template>
	<view class="page">
		<view class="list">
			<view class="single-list">
				<view class="img"><image src="/static/image/test.jpg" ></image></view>
				<view class="list-right">
					<view class="title">视频教程视频教程视频教程视频教程视频教程视频教程视频教程视频教程视频教程视频教程</view>
					<view class="btn-wapper">
						<view class="btn" @click="share"><image src="/static/image/share2.png"></image></view>
						<view class="btn2" @click="getCourse">立即领取</view>
					</view>
				</view>
			</view>
			<view class="single-list">
				<view class="img"><image src="/static/image/test.jpg" ></image></view>
				<view class="list-right">
					<view class="title">视频教程视频教程视频教程视频教程视频教程视频教程视频教程视频教程视频教程视频教程</view>
					<view class="btn-wapper">
						<view class="btn" @click="share"><image src="/static/image/share2.png"></image></view>
						<view class="btn2" @click="getCourse">立即领取</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {}
		},
		created() {},
		methods: {
			getCourse() {
				uni.showModal({
				    title: '教程名称',
				    content: '教程名称教程名称教程名称',
					showCancel:false,
					confirmText:'复制下载链接',
				    success: function (res) {
				        if (res.confirm) {
							uni.setClipboardData({
							    data: 'Zyaun01',
							    success: function () {
							        uni.showToast({
							            title: '已复制链接，请前往浏览器下载',
							            duration: 2000
							        });
							    }
							});
				        }
				    }
				});
			},
			//分享朋友
			share(){
				uni.share({
				    provider: "weixin",
				    scene: "WXSceneSession",
				    type: 0,
				    href: "http://uniapp.dcloud.io/",
				    title: "全网短视频去水印",
				    summary: "我正在使用全网短视频去水印小程序，赶紧跟我一起来体验！",
				    imageUrl: "https://img-cdn-qiniu.dcloud.net.cn/uniapp/images/uni@2x.png",
				    success: function (res) {
				        console.log("success:" + JSON.stringify(res));
				    },
				    fail: function (err) {
				        console.log("fail:" + JSON.stringify(err));
				    }
				});
			},
		}
	}
</script>

<style>
page {display: flex;flex-direction: column;box-sizing: border-box;background-color: #efeff4;min-height: 100%;height: auto;}
view {font-size: 14px;}
.list{display: flex;flex-direction: column;}
.single-list{display: flex;flex-direction: row;margin-bottom: 10rpx;padding: 20rpx;background: #fff;}
.img image{width: 220rpx;height: 220rpx;}
.list-right{display: flex;flex-direction: column;}
.title{font-size: 15px;height: 160rpx;line-height: 40rpx;margin-left: 22rpx;}
.btn-wapper{display: flex;flex-direction: row;justify-content: space-around;}
.btn{border-radius: 50rpx;background: #00baff;width: 180rpx;height:60rpx;line-height:60rpx;text-align: center;display: flex;flex-direction: row;align-items: center;justify-content: center;}
.btn image{width: 48rpx;height: 48rpx;}
.btn2{color: #fff;font-size: 12px;border-radius: 50rpx;background: #00baff;width: 180rpx;height:60rpx;line-height:60rpx;text-align: center;display: flex;flex-direction: row;align-items: center;justify-content: center;}
</style>