<template>
	<view >
		<wyb-table style="auto" ref="table" 
			:width="deviceWidth" 
			:height="deviceHeight" 
			:min-height="minHeight"
			:font-size="fontSize"
			:default-col-width="defaultColWidth"
			:computed-col="computedCol"
			:headers="headers" 
			:contents="contents" 
			:sortCol="sortCol" 
			:first-line-fixed="true"  />
	</view>
</template>

<script>
	import wybTable from '@/components/wyb-table/wyb-table.vue'
	export default {
	    components: { 
	        wybTable
	    },
	    data() {
	        const theUrl = '/pages/demos/noticeBar-demo/more' // 本地的某个页面
	        const httpUrl = 'https://ext.dcloud.net.cn/plugin?id=2667' // 某个网址
	        return {
				isLandScape:false,		// 是否横屏，默认否
				deviceWidth:'auto',		// 屏幕宽度
				deviceHeight:'auto',	// 屏幕高度
				minHeight:[70],			// 单元格最小高度
				fontSize:[30],			// 表格字体大小
				defaultColWidth:165,		// 表格中所有列的列宽，其值可被headers数组的元素中的width覆盖
				
	            headers: [{
	                label: '歌名',
	                key: 'name'
	            }, {
	                label: '来源',
	                key: 'source'
	            }, {
	                label: '截止日期',
	                key: 'etime',
					width:'200',
	            }, {
	                label: '评论置顶',
	                key: 'is_comment',
					width:'200',
	            }, {
	                label: '加话题',
	                key: 'is_topic',
	            },
				{
	                label: '回填',
	                key: 'is_back'
	            },{
				    label: '统计',
				    key: 'is_count'
				},{
				    label: '收款',
				    key: 'is_over'
				},
				{
				    label: '价格',
				    key: 'money'
				},
				{
				    label: '类型',
				    key: 'type'
				},
				{
				    label: '点赞奖励',
				    key: 'reward'
				},
				// {
				// 	label: '路由',
				// 	key: 'url'
				// }, 
				// {
	   //              label: '回填',
	   //              key: 'link'
	   //          },
				],
	            contents: [
					// {
					// id:1,
	    //             name: '晴天',
	    //             source: '北忆',
	    //             etime: '7.23',
	    //             is_comment: 0,
	    //             is_topic: 1,
					// is_back: 0,
					// is_count:0,
					// money:'100',
					// type:'征稿',
					// reward:'800赞300',
	    //         },
				],
				sortCol:[{
						key: 'name',    // 所在列的key值（表头的key）
						isNumber: false // 该列数据是不是数字或者日期，日期例子：(2020-09-04 08:00) (2020/06/04) (08:00:06)
					},{
					key: 'source',   
					isNumber: false 
				}, {
					key: 'etime',
					isNumber: true
				}, {
					key: 'is_comment',
					isNumber: true
				}, {
					key: 'is_topic',
					isNumber: true
				}, {
					key: 'is_back',
					isNumber: true
				},
				{
					key: 'is_count',
					isNumber: true
				}, 
				{
					key: 'is_over',
					isNumber: true
				},
				{
					key: 'money',
					isNumber: true
				}, {
					key: 'type',
					isNumber: true
				},
				],
				computedCol:['money'],
				
				totalPage:1,			// 总页数
				page:1,					// 当前页 
	        }
	    },
		onLoad(){
			this.setDeviceSize()
			// this.getData()
		},
		// onPullDownRefresh() {
		// 	console.log('refresh');
		// 	setTimeout(function () {
		// 		uni.stopPullDownRefresh();
		// 	}, 1000);
		// },
		onPageScroll(){
			console.log(33)
			uni.$emit('onPageScroll');
		},
		onReachBottom(){
			 console.log(111)
			// if(this.page >= this.totalPage){
			// 	return ;	// 没有了
			// }
			uni.$emit('onReachBottom');
			// this.getData({ page: this.page + 1 })	// 列表参数加上分页
		},
		// 监听屏幕旋转
		onResize(){
			this.setDeviceSize()
		},
		methods: {
			// 
			// getData(e={}){
			// 	uni.request({
			// 	    url: 'https://gaogeqiang.peterz.top/video/dy/getList.php', 
			// 		method:'GET',
			// 		data:e,
			// 	    success: (res) => {
						
			// 			this.page = res.data.data.pageData.current_page;			// 当前页
			// 			this.totalPage = res.data.data.pageData.last_page;		// 总页数
			// 			if(this.page == 1){	// 不同列表  不合并,覆盖
			// 				this.contents = res.data.data.list
			// 				// console.log(res.data.data)
			// 			}else{	// 相同列表 分页,合并数组
			// 				var tempList = res.data.data.list				// 临时列表
			// 				this.contents = this.contents.concat(tempList);	// 将新查询的数组放到末尾
			// 			}
						
						
			// 			console.log(222)
			// 			// console.log(res.data,this.contentsSort)
			// 			// this.contents = res.data.data.list
			// 			// this.sourceList = res.data.data.sourceList
			// 			// this.contentsSort = res.data.data.list
			// 			// this.oContentsSort = JSON.parse(JSON.stringify(res.data.data.list)) 
			// 			// this.countInfo = res.data.data.count
			// 	    }
			// 	});
			// },
			
			
			// 设置屏幕尺寸
			setDeviceSize(){
				let that = this
				uni.getSystemInfo({  
					success: function(res) {  
						// console.log(res.windowWidth,res.windowHeight)
						that.isLandScape = (res.windowWidth > res.windowHeight)?true:false
						that.deviceWidth = res.windowWidth
						that.deviceHeight = res.windowHeight + 'px'
						that.minHeight = that.isLandScape?[10]:[70]
						that.fontSize = that.isLandScape?[16]:[30],
						that.defaultColWidth = that.isLandScape?85:165
						that.headers[2].width = that.isLandScape?115:200	// 截止日期
						that.headers[3].width = that.isLandScape?115:200	// 评论置顶
						that.headers[4].width = that.isLandScape?100:200	// 加话题
						that.headers[5].width = that.isLandScape?100:200	// 加话题
						that.headers[10].width = that.isLandScape?115:200	// 点赞奖励
					}  
				})
			}
		}
	}
</script>

<style>

</style>
