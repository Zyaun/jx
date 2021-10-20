<template>
	<view class="page">
		<view class="bg"></view>
		<!-- 用户信息 start -->
		<view class="user-info">
			<view class="user-wapper">
				<block v-if="true">
					<image src="../../static/image/nologin.jpg"></image>
					<button class="loginbtn" type="default" plain="true">登录</button>
				</block>
				<block v-else>
					<image src="../../static/image/u.jpg"></image>
					<view>peterz</view>
				</block>
			</view>
			<view class="data-wapper">
				<view>
					<view class="num">0 次</view>
					<view class="tips">共为您解析次数</view>
				</view>
				<view >
					<view class="num">1 天</view>
					<view class="tips">已为您服务天数</view>
				</view>
			</view>
		</view>
		<!-- 用户信息 end -->
				
		<!-- 菜单列表 start -->
		<view class="meau" >
			<view class="meau-wapper" >
				<!-- <view class="single-meau single-meau1" @click="collectionZan(0)">
					<view class="left-ico iconfont icon-favorfill"><image src="../../static/image/down.png" ></image></view>
					<view class="meau-text">下载记录</view>
					<view class="right-ico iconfont icon-right"><image src="../../static/image/right.png" ></image></view>  
				</view> -->
				<view class="single-meau" @click="showQrcode">
					<view class="left-ico iconfont icon-sponsorfill"><image src="../../static/image/shang.png" ></image></view>  
					<text class="meau-text">打赏支持</text>
					<view class="right-ico iconfont icon-right"><image src="../../static/image/right.png" ></image></view>  
				</view>
		<!-- 		<view class="single-meau" @click="watering">
					<view class="left-ico iconfont icon-noticefill"></view>  
					<text class="meau-text">我的小树</text>
					<view class="right-ico iconfont icon-right"><image src="../../static/image/right.png" ></image></view>  
				</view> -->
				<view class="line"></view>
				<view class="single-meau" @click="addWX">
					<view class="left-ico iconfont icon-sponsorfill"><image src="../../static/image/wx.png" ></image></view>  
					<text class="meau-text">加个微信不迷路</text>
					<view class="right-ico iconfont icon-right"><image src="../../static/image/right.png" ></image></view>  
				</view>
				<!-- <view class="padd"></view> -->
				<view class="line"></view>
					<view class="single-meau" @click="share">
						<view class="left-ico iconfont icon-guanyuwomen"><image src="../../static/image/share.png" ></image></view>  
						<text class="meau-text">分享</text>
						<view class="right-ico iconfont icon-right"><image src="../../static/image/right.png" ></image></view>  
					</view>
			<!-- 	<view class="single-meau" @click="feedback">
					<view class="left-ico iconfont icon-writefill"></view>  
					<text class="meau-text">意见反馈</text>
					<view class="right-ico iconfont icon-right"><image src="../../static/image/right.png" ></image></view>  
				</view> -->
					<view class="single-meau single-meau0">
						<view class="left-ico iconfont icon-formfill"><image src="../../static/image/kf.png" ></image></view>  
						<button plain="true" class="meau-text contact" open-type="contact" @contact="contact">在线客服</button>
						<view class="right-ico iconfont icon-right"><image src="../../static/image/right.png" ></image></view>  
					</view>
			</view>
		</view>
		<!-- 菜单列表 end -->
		<view class="copyright"> 作品归平台及作者所有，本应用不储存任何作品及图片。 </view>
	</view>
</template>

<script>
	export default {
		data() {
			return {}
		},
		created() {},
		methods: {
			//展示打赏二维码
			showQrcode() {
				wx.previewImage({
					urls: ['https://gaogeqiang.peterz.top/video/dy/ds.jpg']
				})
			},
			// 复制微信
			addWX(){
				uni.showModal({
				    title: '温馨提示',
				    content: '您将复制客服微信Zyaun01',
					showCancel:false,
				    success: function (res) {
				        if (res.confirm) {
							uni.setClipboardData({
							    data: 'Zyaun01',
							    success: function () {
							        uni.showToast({
							            title: '已复制微信',
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

<style scoped>
	@import url("mine.css");
	.single-meau{
		border-bottom: 1px solid #F1F1F1;
	}
</style>