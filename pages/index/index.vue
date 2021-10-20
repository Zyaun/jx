<template>
	<view class="page">
		<!-- 图片 -->
		<view class="banner">
			<image src="../../static/image/banner.jpg" mode="widthFix"></image>
		</view>
		<!-- 滚动公告 -->
		<view class="notice-bar">
			<uni-notice-bar :show-icon="true" :show-close="true" :scrollable="true" :single="true" :speed="30" text="短视频解析平台上线啦" />
		</view>
		<!-- 分享 -->
		<button class="share" open-type="share">
			<image src="../../static/image/fx.png" mode=""></image>
		</button>
		<!-- 解析按钮 -->
		<view class="jiexi">
			<input type="text" v-model="url" placeholder="请在此处粘贴分享链接~" placeholder-style="font-size:14px">
			<view @click="jiexi">解析</view>
		</view>
		<view class="nav">
			<!-- 导航栏 -->
			<view class="nav-wapper">
				<scroll-view scroll-x="true" scroll-with-animation class="scroll-tab">
					<block v-for="(item,index) in tabBars" :key="index">
						<view class="scroll-tab-item" :class="{'active': tabIndex==index}" @tap="toggleTab(index)">
							<text>{{item.name}}</text>
							<view class="scroll-tab-line"></view>
						</view>
					</block>
				</scroll-view>
			</view>
			<!-- 内容区 -->
			<view class="nav-content" >
				<swiper :current="tabIndex" @change="tabChange"><!-- current:当前所在滑块的index -->
					<swiper-item v-for="(item,index) in navList" :key="index" >
						<view class="item-wapper" v-if="index == 0">
							<video :src="videoInfo.url" controls></video>
							<view class="save" @click="saveVideo">保存到相册(稳定)</view>
							<view class="shang" @click="prevImg">打赏一下,给服务器提提速呗</view>
							<view class="tips">视频均来自平台,本程序不做任何存储</view>
						</view>
						<view class="item-wapper" v-if="index == 1">
							<view class="title">{{videoInfo.title}}</view>
							<view class="save" @click="copy(videoInfo.title)">复制标题</view>
							<view class="shang" @click="prevImg">打赏一下,给服务器提提速呗</view>
							<view class="tips" >视频均来自平台,本程序不做任何存储</view>
						</view>
					</swiper-item>
				</swiper>
			</view>
		</view>
		<!-- 菜单 -->
		<view class="meau">
			<image class="left-meau" src="../../static/image/course.png"></image>
			<image class="right-meau" src="../../static/image/invite.png"></image>
			<image class="left-meau" src="../../static/image/questions.png"></image>
			<image class="right-meau" src="../../static/image/about-md5.png"></image>
		</view>
		<!-- 教程 -->
		<view class="course">
			<image src="../../static/image/jiexilook.png" mode=""></image>
		</view>
		<view class="copyright"> 作品归平台及作者所有，本应用不储存任何作品及图片。 </view>
	</view>
</template>

<script>
	import uniNoticeBar from '@/components/uni-notice-bar/uni-notice-bar.vue'
	export default {
		components: {
			uniNoticeBar,
		},
		data() {
			return {
				tabIndex: 0, /* 选中标签栏的序列,默认显示第一个 */
				navList: ["视频下载","标题复制"],
				tabBars:[
					{name: '视频下载',id: 'down'},
					{name: '标题复制',id: 'copy'}
				],
				url:'https://v.douyin.com/eoybp5y/',		// 短视频地址
				videoInfo:{		// 视频信息
					'title':'',
					'url':'',
					'img':''
				},
				type:0,		// 0 表示客户端下载，1表示服务端下载
			}
		},
		created() {},
		onShareAppMessage() {
		    return {
		      title: '全网短视频去水印解析',
		      path: '/page/index/index',
		      imageUrl:'../../static/image/banner.jpg' 
		    }
		},
		onShareTimeline(){
			return {
			  title: '全网短视频去水印解析',
			  query: '/page/index/index',
			  imageUrl:'../../static/image/banner.jpg' 
			}
		},
		methods: {
			//打赏 预览图片
			prevImg(){
				uni.previewImage({
					urls: ['https://gaogeqiang.peterz.top/video/dy/ds.jpg']
				});
			},
			// 保存视频到相册
			saveVideo(){
				if(this.videoInfo.url == ""){
					uni.showToast({
						title: "请先复制地址解析视频~",
						icon: "none"
					})
					return;
				}
				uni.showLoading({
					title: "保存中~",
				})
				
				if(this.type){   // 服务端下载
					// const url = 'https://gaogeqiang.peterz.top/video/dy/save.php?url=' + encodeURIComponent(this.videoInfo.url)
					var url = 'https://www.vcsayha.com/index.php?m=Index&c=Index&a=checkFile&url=' + encodeURIComponent(this.videoInfo.url)		// 15s
				}else{		// 客户端下载
					var url = this.videoInfo.url
				}
				// console.log(this.videoInfo.url,url)
				
				uni.downloadFile({
					url: url,
					success: (res) => {
						console.log(res)
						// return;
						if (res.statusCode === 200) {
							uni.saveVideoToPhotosAlbum({
								filePath: res.tempFilePath,
								success: function() {
									uni.hideLoading()
									uni.showToast({
										title: "保存成功",
									});
								},
								fail: function(res) {
									console.log(res)
									uni.hideLoading()
									uni.showToast({
										title: "保存失败，请稍后重试",
										icon: "none"
									});
								}
							});
						}else{
							uni.hideLoading()
							uni.showToast({
								title: "保存失败，请稍后重试",
								icon: "none"
							});
						}
					}
				});
			},
			// 点击解析
			jiexi() {
				var that = this
				if(that.trimStr(that.url) == ""){
					uni.showToast({
					    title: '请复制粘贴短视频地址~',
						icon: 'none'
					});
					return;
				}
				uni.showLoading({
				    title: '疯狂解析中~',
				});
				uni.request({
					// url: 'https://gaogeqiang.peterz.top/video/dy/index.php', 
				    url: 'https://down.htsm.top/down.php?url='+ encodeURIComponent(that.url), 
					method:'POST',
					data:{
						url:encodeURIComponent(that.url)
					},
				    success: (res) => {
						console.log(res)
						if(res.data.data != undefined){ // 解析成功
							// console.log(res.data.data)
							// 判断如果不在合法域名中，则再次解析
							that.videoInfo = res.data.data
							console.log(that.videoInfo.url)
							uni.showToast({
							 	title: "解析成功",
							})
						}else{
							uni.showToast({
							 	title: "解析失败",
							 	icon: 'none'
							})
						}
						uni.hideLoading();
				    }
				});
			},
			// 点击复制 标题
			copy(title){
				if(this.videoInfo.url == ""){
					uni.showToast({
						title: "请先复制地址解析视频~",
						icon: "none"
					})
					return;
				}
				uni.setClipboardData({
				    data: title,
				    success: function () {
				        uni.showToast({
				         	title: '已复制到剪贴板',
				         	icon: 'none'
				        })
				    }
				});
			},
			// 去除首尾空格
			trimStr(str){
			  return str.replace(/(^\s*)|(\s*$)/g,"");
			},
			//切换选项卡
			toggleTab (index) { 
				this.tabIndex=index;
			},
			//滑动切换swiper
			tabChange (e) { 
				// console.log(e);
				this.tabIndex=e.detail.current;
			},
			// 保存视频到相册
			// saveVideo2(url){
			// 	var domain = url.match(/http:\/\/(\S*)douyinvod/)[1]   // 如v3.
			// 	// 比如解析出来的视频地址有 v1-dy v2-dy v3-dy ... v9-dy 将此9条或更多加入到自己小程序合法下载域名内 'v11.','v26.',
			// 	// 设置匹配数组 
			// 	var matchArr = ['v3.','v3-a.','v3-b.','v3-c.','v3-d.','v3-x.','v3-y.','v3-z.','v6.','v6-x.','v6-y.','v6-z.','v9.','v9-x.','v9-y.','v9-z.','v27.','v29.','v83.'];
			// 	var flag = 0
			// 	for (const i in matchArr) {
			// 	  if (domain == matchArr[i] ) {
			// 		  console.log(i,'匹配到了'+matchArr[i])
			// 	    // 执行下载逻辑
			// 	    // ... 下载逻辑
			// 	    flag = 1
			// 	    break
			// 	  }
			// 	  console.log('没有匹配到')
			// 	}
			// 	if (!flag) {
			// 		console.log("一个都没有MD!!!!!!!!!!!!!!!!!!!!!!!!!!!!!")
			// 	  // 执行解析逻辑 继续解析接口 在匹配 直到匹配完成 或 匹配10次未成功则提示 请重新解析
			// 	  uni.request({
			// 			url: 'https://gaogeqiang.peterz.top/video/dy/index.php', 
			// 			method:'GET',
			// 			data:{
			// 				url:this.trimStr(this.url)
			// 			},
			// 	      success: (res) => {
			// 	  		if(res.data.data != undefined){ // 解析成功
			// 	  			this.videoInfo = res.data.data
			// 	  			console.log(this.videoInfo.url)
			// 	  		}
			// 	      }
			// 	  });
			// 	}
			// },
		}
	}
	
</script>

<style>
page {display: flex;flex-direction: column;box-sizing: border-box;background-color: #efeff4;min-height: 100%;height: auto;}
view {font-size: 14px;line-height: inherit;}
.banner,.banner image{width: 100%;}
.notice-bar {flex-direction: row;flex-wrap: wrap;justify-content: center;padding: 0;font-size: 14px;background-color: #ffffff;margin-top: -10rpx;}
.share,.share image{width: 100%;height: 166rpx;margin-top: 10rpx;}
.jiexi{display: flex;flex-direction: row;margin: 30rpx;justify-content: space-between;}
.jiexi input{width:70%;height: 80rpx;line-height:80rpx;border:1px solid #f1f1f1;background: #fff;padding:  0 30rpx;border-radius: 8rpx;box-shadow: 1px 1px 1px 0px rgba(0,0,0,0.1) ;}
.jiexi view{font-size:14px;color: #fff;background:#fccc02;width: 18%;height: 80rpx;line-height:80rpx;text-align: center;border-radius: 8rpx;}
.course image{width: 100%;height: 380rpx;margin: 20rpx 0;}
.meau{display: flex;flex-direction: row;flex-wrap: wrap;}
.meau image{width: 50%;height: 150rpx;margin-bottom: 20rpx;}
.meau .left-meau{position: relative;left: -10rpx;}
.meau .right-meau{position: relative;left:10rpx;}
.copyright{font-size: 12px;text-align: center;margin-bottom: 40rpx;color: #999;}

.nav{margin: 30rpx;background: #fff;border-radius: 20rpx;padding-bottom: 30rpx;}
.nav-wapper{margin:0;padding-bottom: 30rpx;}
.nav-wapper .active{color: #00baff;}
.nav-wapper .scroll-tab{white-space: nowrap; /* 必要，导航栏才能横向*/border-bottom: 1rpx solid #eee;text-align: center;}
.nav-wapper .scroll-tab-item{display: inline-block; /* 必要，导航栏才能横向*/margin: 20rpx 35rpx ;}
.nav-wapper .active .scroll-tab-line{border-bottom: 3rpx solid #00baff;border-top: 3rpx solid #00baff;border-radius: 20rpx;margin-top: 5rpx; /* width: 70rpx; */}
.nav-content{margin: 0 30rpx;}

swiper{height: 560rpx;}
swiper-item{display: flex;flex-direction: column;}
.item-wapper{display: flex;flex-direction: column;justify-content: center;align-items: center;}
video{width: 100%;height: 300rpx;margin-bottom: 30rpx;}
.save{margin-bottom: 20rpx;height: 80rpx;line-height: 80rpx;text-align: center;border-radius: 40rpx;width: 500rpx;background: #00baff;color: #fff;}
.shang{margin-bottom: 20rpx;height: 80rpx;line-height: 80rpx;text-align: center;border-radius: 40rpx;width: 500rpx;background: #4fc866;color: #fff;}
.tips{font-size: 10px;text-align: center;color: #ccc;}
.title{color: #333;font-size: 14px;height: 330rpx;line-height: 50rpx;width: 100%;}



      
     
button{
	background: none;
	border: none;
	outline: none;
	background: none;
	color: none;
}
button::after{
	background: none;
	border: none;
	outline: none;
	background: none;
	color: none;
}

</style>