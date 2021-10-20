<template>
	<view>
		<!-- 获取历史消息提示 这是我在github上增改的内容 -->
		<!-- <view class="tips color_fff size_12 align_c" :class="{ 'show':ajax.loading }"  v-if="loading">{{ajax.loadText}}</view> -->
		<!-- 聊天框 -->
		<view class="box-1" id="list-box">
			<view class="talk-list">
				<view v-for="(item,index) in talkList" :key="index" :id="`msg-${item.id}`">
					<view class="item flex_col" :class=" item.type == 1 ? 'push':'pull' ">
						<image :src="item.pic" mode="aspectFill" class="pic"></image>
						<view class="content">{{item.content}}</view>
					</view>
				</view>
				<view class="chat-bottom"></view>
			</view>
		</view>
		<!-- 输入框 -->
		<!-- 这是本地加的1 -->
		<!-- 这是本地加的2 -->
		<view class="box-2">
			<view class="flex_col">
				<view class="flex_grow">
					<input type="text" class="content" v-model="content" placeholder="请输入聊天内容" placeholder-style="color:#DDD;" :cursor-spacing="6">
				</view>
				<button class="send" @tap="send">发送</button>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				talkList:[],	// 聊天列表
				ajax:{
					loading:true,	// 加载中
					loadText:'正在获取消息'
				},
				content:''	// 输入框的值
			}
		},
		mounted() {
			this.$nextTick(()=>{
				this.getHistoryMsg();
				this.getChatData();
			});
		},
		// 距离顶部小于5获取历史记录
		onPageScroll(e){
			// if(e.scrollTop<5){
			// 	this.getHistoryMsg();
			// }
		},
		methods: {
			getChatData(){
				let ws,JWT, userInfo,userNum,isClick=0;
				uni.request({
				    url: 'https://peterz.top/index/index/getJwtString',
					method:'GET',
					data:{},
				    success: (res) => {
						res = "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpZCI6NTUsInVzZXJuYW1lIjoiIiwiaGVhZF91cmwiOiJodHRwOlwvXC90aGlyZHFxLnFsb2dvLmNuXC9nP2I9b2lkYiZrPXo4ZWdUcENNTlh1b012M0t1OWFuRGcmcz00MCZ0PTE1ODU4Mjc1OTUifQ.VxTeoQ4S_s-78r5s5aF9ugyNiF1UfqJw_CM9nZbmS_qRiMtYyjDCJfkkFAYBHTgrBK4edgJ2GYJ4KVCfEma3W86P-6UfR7J5jlrCZ7UgIoAHxBV4uO1WFpMu2Ku5Kw05GoBUJeolHPS3lxfB_XF8S5qKaGU0xL_PAnJPbPjxdQcMjQ40CnTe6FTbYD986k-gVSQctXouvVdnPp_1DrInoLRgellrlUqNaUeAZ-Qehd1rZttIuYVhqxPtxkCDrdw9dtk9pSCoS5SWaXVBCN4kazTcv5z-PPmGHNwHWbtOeBuha-C6nXcf7uzXnDuN1dfDjRMgJtHMmbMG5_pZqfwSOg"
						console.log(res)
						JWT = res;
						userInfo = JSON.parse(atob(JWT.split('.')[1]));
						
						
						// ws 建立
						ws = uni.connectSocket({
						  url: 'wss://peterz.top:9501'		// !!!浏览器端叫ws,uniapp为wss
						});
						// 打开连接
						uni.onSocketOpen(function (res) {
							console.log('WebSocket连接已打开！');
							uni.sendSocketMessage(JSON.stringify({
							    "method": "bind", 
							    "JWT": JWT
							}))
							
							// 获取用户列表
							uni.sendSocketMessage(JSON.stringify({
							     "method": "getUserList"
							}))
							
							
						});
					
						console.log(ws)
						return;
						
						
						
						 
						 // 收到消息时，让滚动条自动滚动到最底部
						 // $(".layim-chat-main").prop("scrollTop",  $(".layim-chat-main")[0].scrollHeight);
				    }
				});
				
		
				
				
			},
			
			
			// 获取历史消息
			getHistoryMsg(){
				let data = [];
				for(let i = 0; i < 20; i++){
					data.push({
						"id":i,	// 消息的ID
						"content":`这是历史记录的第${i+1}条消息`,	// 消息内容
						"type":Math.random() > 0.5 ? 1 : 0	,// 此为消息类别，设 1 为发出去的消息，0 为收到对方的消息,
						"pic":"/static/logo.png"	// 头像
					})
				}
				console.log('----- 模拟数据格式，供参考 -----');
				console.log(data);	// 查看请求返回的数据结构 
				this.talkList = data
				this.$nextTick(()=>{
					this.setPageScrollTo('.chat-bottom');	// 设置当前滚动的位置
				})
			},
			// 设置页面滚动位置
			setPageScrollTo(selector){
				let view = uni.createSelectorQuery().in(this).select(selector);
				view.boundingClientRect((res) => {
					uni.pageScrollTo({
					    scrollTop:res.top - 30,	// -30 为多显示出大半个消息的高度，示意上面还有信息。
					    duration: 0
					});
				}).exec();
			},
			// 发送信息
			send(){
				if(!this.content){
					uni.showToast({
						title:'请输入有效的内容',
						icon:'none'
					})
					return;
				}
				
				// 将当前发送信息 添加到消息列表。
				let data = {
					"id":new Date().getTime(),
					"content":this.content,
					"type":1,
					"pic":"/static/logo.png"
				}
				this.talkList.push(data);
				
				this.$nextTick(()=>{
					// 清空内容框中的内容
					this.content = '';
					uni.pageScrollTo({
					    scrollTop: 999999,	// 设置一个超大值，以保证滚动条滚动到底部
					    duration: 0
					});
				})
			}
		}
	}
</script>

<style lang="scss">
	@import "../../lib/global.scss";
	page{
		background-color: #F3F3F3;
		font-size: 28rpx;
	}
	
	/* 加载数据提示 */
	.tips{
		position: fixed;
		left: 0;
		top:var(--window-top);
		width: 100%;
		z-index: 9;
		background-color: rgba(0,0,0,0.15);
		height: 72rpx;
		line-height: 72rpx;
		transform:translateY(-80rpx);
		transition: transform 0.3s ease-in-out 0s;
		
		&.show{
			transform:translateY(0);
		}
	}
	
	.box-1{
		width: 100%;
		height: auto;
		padding-bottom: 100rpx;
		box-sizing: content-box;
		
		/* 兼容iPhoneX */
		margin-bottom: 0;  
		margin-bottom: constant(safe-area-inset-bottom);  
		margin-bottom: env(safe-area-inset-bottom);  
	}
	.box-2{
		position: fixed;
		left: 0;
		width: 100%;
		bottom: 0;
		height: auto;
		z-index: 2;
		border-top: #e5e5e5 solid 1px;
		box-sizing: content-box;
		background-color: #F3F3F3;
		
		/* 兼容iPhoneX */
		padding-bottom: 0;  
		padding-bottom: constant(safe-area-inset-bottom);  
		padding-bottom: env(safe-area-inset-bottom);  
		
		>view{
			padding: 0 20rpx;
			height: 100rpx;
		}
		
		.content{
			background-color: #fff;
			height: 64rpx;
			padding: 0 20rpx;
			border-radius: 32rpx;
			font-size: 28rpx;
		}
		
		.send{
			background-color: #42b983;
			color: #fff;
			height: 64rpx;
			margin-left: 20rpx;
			border-radius: 32rpx;
			padding: 0;
			width: 120rpx;
			line-height: 62rpx;
			
			&:active{
				background-color: #5fc496;
			}
		}
	}
	
	.talk-list{
		padding-bottom: 20rpx;
		
		/* 消息项，基础类 */
		.item{
			padding: 20rpx 20rpx 0 20rpx;
			align-items:flex-start;
			align-content:flex-start;
			color: #333;
			
			.pic{
				width: 92rpx;
				height: 92rpx;
				border-radius: 50%;
				border: #fff solid 1px;
			}
			
			.content{
				padding: 20rpx;
				border-radius: 4px;
				max-width: 500rpx;
				word-break: break-all;
				line-height: 52rpx;
				position: relative;
			}
			
			/* 收到的消息 */
			&.pull{
				.content{
					margin-left: 32rpx;
					background-color: #fff;
					
					&::after{
						content: '';
						display: block;
						width: 0;
						height: 0;
						border-top: 16rpx solid transparent;
						border-bottom: 16rpx solid transparent;
						border-right: 20rpx solid #fff;
						position: absolute;
						top: 30rpx;
						left: -18rpx;
					}
				}
			}
			
			/* 发出的消息 */
			&.push{
				/* 主轴为水平方向，起点在右端。使不修改DOM结构，也能改变元素排列顺序 */
				flex-direction: row-reverse;
				
				.content{
					margin-right: 32rpx;
					background-color: #a0e959;
					
					&::after{
						content: '';
						display: block;
						width: 0;
						height: 0;
						border-top: 16rpx solid transparent;
						border-bottom: 16rpx solid transparent;
						border-left: 20rpx solid #a0e959;
						position: absolute;
						top: 30rpx;
						right: -18rpx;
					}
				}
			}
		}
	}
</style>