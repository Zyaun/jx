<template>
	<view class="wyb-table-box" style="width: 100%;border-bottom: 1px solid #e1e1e1;" >
		<view v-if="loading" class="wyb-table-loading-box" :style="{
			width: width === 'auto' ? screenWidth : width,
			width:'100%',
			height: height === 'auto' ? '300rpx' : height,
			backgroundColor: loaderBgColor,
			borderTop: '1px solid' + borderColor,
			borderBottom: '1px solid' + borderColor,
			borderLeft: showLeftAndRightBorder ? '1px solid' + borderColor : 'none',
			borderRight: showLeftAndRightBorder ? '1px solid' + borderColor : 'none'}">
			<view class="loader-one" :style="{
				 width: loaderSize + 'rpx',
				 height: loaderSize + 'rpx',
				 borderTop: '3px solid ' + loadingColor.top,
				 borderRight: '3px solid ' + loadingColor.right,
				 borderBottom: '3px solid ' + loadingColor.bottom,
				 borderLeft: '3px solid ' + loadingColor.left}" />
		</view>
		<view v-if="!loading" class="wyb-table-scroll-view" :style="{
			width: '100%',
			height: height,
			borderTop: '1px solid' + borderColor,
			borderLeft: showLeftAndRightBorder ? '1px solid' + borderColor : 'none',
			borderRight: showLeftAndRightBorder ? '1px solid' + borderColor : 'none'}">
			<view class="wyb-table-header" :style="{borderBottom: '1px solid' + borderColor}">
				<view class="wyb-table-header-item" v-if="enableCheck" :style="{
					 minWidth: checkColWidth + 'rpx',
					 maxWidth: checkColWidth + 'rpx',
					 minHeight: minHeight[0] + 'rpx',
					 textAlign: textAlign,
					 justifyContent: textAlign === 'center' ? textAlign : (textAlign === 'left' ? 'flex-start' : 'flex-end'),
					 fontSize: fontSize[0] + 'rpx',
					 color: headerFtColor,
					 padding: padding[0] + 'rpx ' + (padding[1] || padding[0]) + 'rpx',
					 backgroundColor: headerBgColor,
					 borderRight: '1px solid' + borderColor,
					 zIndex: 30,
					 left: 0, 
					 color: headerFtColor,
					 backgroundColor: headerBgColor,
					 position: 'sticky'}">
						<view 
						 class="wyb-table-checkbox"
						 v-if="enableCheck === 'multiple'"
						 @tap.stop="onCheckAllTap"
						 :style="{
							width: checkColWidth * 0.5 + 'rpx',
							height: checkColWidth * 0.5 + 'rpx',
							backgroundColor: checkerBoxBgColor,
							border: '1px solid ' + checkerBorderColor}">
							<text 
							 class="iconfont icon-check"
							 v-show="checkAll"
							 :style="{
								color: checkerColor,
								backgroundColor: checkerBgColor,
								paddingTop: (fontSize[1] || fontSize[0]) * 0.15 + 'rpx',
								fontSize: (fontSize[1] || fontSize[0]) + 'rpx'}" />
						</view>
					 </view>
				<view ref="iosBug" class="wyb-table-header-item" v-for="(item, index) in headers" :key="item.key" @tap="onHeaderItemTap(index)"
				 :style="{
					 minWidth: (item.width || defaultColWidth) + 'rpx',
					 maxWidth: (item.width || defaultColWidth) + 'rpx',
					 minHeight: minHeight[0] + 'rpx',
					 textAlign: textAlign,
					 justifyContent: textAlign === 'center' ? textAlign : (textAlign === 'left' ? 'flex-start' : 'flex-end'),
					 fontSize: fontSize[0] + 'rpx',
					 fontWeight: headerWeight ? 'bold' : 'normal',
					 color: headerFtColor,
					 padding: padding[0] + 'rpx ' + (padding[1] || padding[0]) + 'rpx',
					 backgroundColor: headerBgColor,
					 borderRight: index === headers.length - 1 || (!showVertBorder && index !== 0) ? 'none' : '1px solid' + borderColor,
					 zIndex: index === 0 ? 20 : 0,
					 left: index === 0 && firstLineFixed ? (enableCheck ? checkColWidth + 'rpx' : 0) : 'auto', 
					 position: index === 0 ? 'sticky' : 'static'}">
					 <text :style="{marginLeft: autoSortShow(index) && textAlign !== 'left' ? fontSize[0] * 0.65 + 'rpx' : 0}">
						 {{item.label || emptyString}}
					 </text>
					 <view class="wyb-table-header-icon" v-if="autoSortShow(index)">
					 	<text class="iconfont icon-arrow-up" :style="{
							color: sortWays[sortWay] === 'asc' && sortActiveKey === item.key ? 
								headerFtColor : RGBChange(headerFtColor, 0.7, 'light'),
							fontWeight: 'normal',
							marginBottom: '-12px',
							transform: 'scale(0.4)'}" />
					 	<text class="iconfont icon-arrow-down" :style="{
							color: sortWays[sortWay] === 'inv' && sortActiveKey === item.key ? 
								headerFtColor : RGBChange(headerFtColor, 0.7, 'light'),
							fontWeight: 'normal',
							transform: 'scale(0.4)'}" />
					 </view>
				</view>
			</view>
			<!-- 表格内容 -->
			<view class="wyb-table-content" >
				<view class="wyb-table-content-line" v-for="(content, cIndex) in contentsSort" :key="contentLineKey(content, cIndex)"
				 :style="{borderTop: cIndex === 0 ? 'none' : '1px solid' + borderColor}">
					<!-- 多选 -->
					<view class="wyb-table-content-item" v-if="enableCheck" :style="{
						 minWidth: checkColWidth + 'rpx',
						 maxWidth: checkColWidth + 'rpx',
						 textAlign: textAlign,
						 justifyContent: textAlign === 'center' ? textAlign : (textAlign === 'left' ? 'flex-start' : 'flex-end'),
						 fontSize: (fontSize[1] || fontSize[0]) + 'rpx',
						 minHeight: (minHeight[1] || minHeight[0]) + 'rpx',
						 padding: padding[0] + 'rpx ' + (padding[1] || padding[0]) + 'rpx',
						 borderRight: '1px solid' + borderColor,
						 zIndex: 21,
						 color: contentFtColor,
						 backgroundColor: checkerCellBgColor,
						 left: 0,
						 position: 'sticky'}">
							<view 
							 class="wyb-table-checkbox" 
							 @tap.stop="onCheckItemTap(cIndex)"
							 :style="{
								width: checkColWidth * 0.5 + 'rpx',
								height: checkColWidth * 0.5 + 'rpx',
								backgroundColor: checkerBoxBgColor,
								border: '1px solid ' + checkerBorderColor}">
								<text 
								 class="iconfont icon-check" 
								 v-show="contentsSort[cIndex].checked"
								 :style="{
									color: checkerColor,
									backgroundColor: checkerBgColor,
									paddingTop: (fontSize[1] || fontSize[0]) * 0.15 + 'rpx',
									fontSize: (fontSize[1] || fontSize[0]) + 'rpx'}" />
							</view>
					</view>
					<!-- 具体每一个单元格 -->
					<view
					 class="wyb-table-content-item" 
					 v-for="(header, hIndex) in headers"
					 @tap.stop="onContentItemTap(cIndex, hIndex)"
					 :key="contentItemKey(header, hIndex)" 
					 :style="{
						 minWidth: (header.width || defaultColWidth) + 'rpx',
						 maxWidth: (header.width || defaultColWidth) + 'rpx',
						 textAlign: textAlign,
						 justifyContent: textAlign === 'center' ? textAlign : (textAlign === 'left' ? 'flex-start' : 'flex-end'),
						 fontSize: (fontSize[1] || fontSize[0]) + 'rpx',
						 textDecoration: autoTextDecoration(cIndex, hIndex),
						 color: autoContentColor(cIndex, hIndex),
						 backgroundColor: autoContentBgColor(cIndex, hIndex),
						 minHeight: (minHeight[1] || minHeight[0]) + 'rpx',
						 borderBottom: cIndex === contents.length - 1 ? '1px solid' + borderColor : 'none',
						 borderRight: hIndex === headers.length - 1 || (!showVertBorder && hIndex !== 0) ? 'none' : '1px solid' + borderColor,
						 zIndex: hIndex === 0 ? 20 : 0,
						 left: enableCheck ? checkColWidth + 'rpx' : 0,
						 overflow: 'hidden',
						 textOverflow:'ellipsis',
						 whiteSpace: 'nowrap',
						 alignItems:'center',
						 
						 position: hIndex === 0 && firstLineFixed ? 'sticky' : 'static'}">
						<!-- switch -->
						<view v-if="hIndex == 3 || hIndex == 4 || hIndex == 5 || hIndex == 6 || hIndex == 7 " :style="{
							  padding: padding[0] + 'rpx ' + (padding[1] || padding[0]) + 'rpx',
							  display: 'flex',
							  width: '100%',
							  height: '100%',
							  flexDirection:'row',
							  alignItems:'center',
							  justifyContent:'center'
						 }" >
				
							 <switch v-if="hIndex == 3"  :checked="content.is_comment?true:false" :data-id="content.id" :data-hIndex="hIndex" @change="switchChange"  />
							 <switch v-if="hIndex == 4"  :checked="content.is_topic?true:false" :data-id="content.id" :data-hIndex="hIndex" @change="switchChange"  />
							 <switch v-if="hIndex == 5" :checked="content.is_back?true:false" :data-id="content.id" :data-hIndex="hIndex" @change="switchChange"  />
							 <switch v-if="hIndex == 6"  :checked="content.is_count?true:false" :data-id="content.id" :data-hIndex="hIndex" @change="switchChange"  />
							 <switch v-if="hIndex == 7"  :checked="content.is_over?true:false" :data-id="content.id" :data-hIndex="hIndex" @change="switchChange"  />
							
						 </view>
						 <view v-else-if="hIndex ==9">{{content.type==0?'征稿':'定投'}}</view>
						 <view v-else  @click="handleEdit" style="height: 100%;display: flex;flex-direction: row;align-items: center;justify-content: center;width: 100%;height: 100%; overflow:hidden; text-overflow:-o-ellipsis-lastline; text-overflow:ellipsis; display:-webkit-box; -webkit-line-clamp:2; line-clamp:2; -webkit-box-orient:vertical;line-height: 76rpx;"
						 :data-hindex="hIndex"
						 :data-id="content.id"
						 :data-name="content.name" 
						 :data-source="content.source" 
						 :data-type="content.type" 
						 :data-etime="content.etime" 
						 :data-money="content.money" 
						 :data-is_comment="content.is_comment" 
						 :data-is_topic="content.is_topic" 
						 :data-is_back="content.is_back" 
						 :data-is_over="content.is_over" 
						 :data-is_count="content.is_count" 
						 :data-reward="content.reward" 
						 >
						 <!-- #ffd264 -->
							<view :style="hIndex == 0 && (content.is_comment == 0 || content.is_topic == 0 || content.is_back == 0) ? 'background: #f9eca8;':''">{{autoContentItem(cIndex, hIndex)}}</view>
						 </view>
					</view>
						 
						 <!-- {{autoContentItem(cIndex, hIndex)}} -->
				</view>
				<view v-if="computedCol.length !== 0" class="wyb-table-content-line" :style="{
					position: bottomComputedFixed ? 'sticky' : 'static',
					bottom: 0,
					zIndex: 25,
					width:width,
					borderTop: '1px solid' + borderColor}">
					<view class="wyb-table-content-item" style="background: #f1f1f1;text-align: center; justify-content: center; font-size: 15px; text-decoration: auto; color: #3e3e3e;min-height: 35px; border-bottom: 1px solid#e1e1e1; border-right: 1px solid#e1e1e1; z-index: 20; left: 0; position: sticky;" :style="{width: (header.width || defaultColWidth) + 'rpx',}">总计</view>
					<view class="wyb-table-content-item" style=" text-align: left;  font-size: 15px; text-decoration: auto; color: #3e3e3e; min-height: 35px;   z-index: 0; left: 0; position: static;background: #F8F8F8;" :style="{minWidth: finallyWidth + 'rpx',justifyContent: isLandScape?'center':'flex-start',paddingLeft:isLandScape?'0':'40rpx'}">发布作品<text style="margin-left: 15rpx;">{{total}} 条</text> , 已收　<text style="margin-left: 15rpx;">　{{countInfo.hasMoney?countInfo.hasMoney.toFixed(2):0}} 元　</text> , 待收<text style="margin-left: 15rpx;"> {{countInfo.waitMoney.toFixed(2)}} 元</text> , 总计 <text style="margin-left: 15rpx;"> {{countInfo.totalMoney.toFixed(2)}} 元</text></view>
					
				</view>
				
				<!-- 统计 -->
				<view v-if="computedCol.length == 0" class="wyb-table-content-line" :style="{
					position: bottomComputedFixed ? 'sticky' : 'static',
					bottom: 0,
					zIndex: 25,
					width:width,
					borderTop: '1px solid' + borderColor}">
					<!-- <view class="wyb-table-content-item" v-if="enableCheck" :style="{
						 minWidth: checkColWidth + 'rpx',
						 maxWidth: checkColWidth + 'rpx',
						 textAlign: textAlign,
						 justifyContent: textAlign === 'center' ? textAlign : (textAlign === 'left' ? 'flex-start' : 'flex-end'),
						 fontSize: (fontSize[1] || fontSize[0]) + 'rpx',
						 minHeight: (minHeight[1] || minHeight[0]) + 'rpx',
						 padding: padding[0] + 'rpx ' + (padding[1] || padding[0]) + 'rpx',
						 borderBottom: '1px solid' + borderColor,
						 borderRight: '1px solid' + borderColor,
						 zIndex: 25,
						 color: contentFtColor,
						 backgroundColor: checkerCellBgColor,
						 left: 0,
						 position: 'sticky'}"></view> -->
				
					
			
					<view class="wyb-table-content-item" v-for="(header, index) in headers" :key="index"
					:style="{
						 minWidth: (header.width || defaultColWidth) + 'rpx',
						 maxWidth: (header.width || defaultColWidth) + 'rpx',
						 textAlign: textAlign,
						 justifyContent: textAlign === 'center' ? textAlign : (textAlign === 'left' ? 'flex-start' : 'flex-end'),
						 fontSize: (fontSize[1] || fontSize[0]) + 'rpx',
						 color: contentFtColor,
						 minHeight: (minHeight[1] || minHeight[0]) + 'rpx',
						 backgroundColor: index === 0 ? firstColBgColor : contentBgColor,
						 borderRight: index >0 ? 'none' : '1px solid' + borderColor,
						 zIndex: index === 0 ? 20 : 0,
						 left: enableCheck ? checkColWidth + 'rpx' : 0,
						 overflow:'visible',
				
						 whiteSpace:'nowrap',
						 position: index === 0 && firstLineFixed ? 'sticky' : 'static'}">
							<view style="background: red;position: relative;" v-if="index == 0">
								{{autoBottomComputedItem(index)}}
							</view>
							<view style="background: red;position: relative;width: 100%;max-width: 9999999999rpx;" v-if="index == 1">
								<view style="background: green;white-space:nowrap;left: 30rpx;position: absolute;" > 发布作品:100条,收益1000元 </view>
							</view>
					</view>
				</view>
			</view>
		</view>
		<!-- <drag-button-follow>+</drag-button-follow> -->
		<div  v-if="!isLandScape" :style.sync="style" className="drag-button" class="drag-button" :class="isLandScape?'hAddBtnStyle':'sAddBtnStyle'"  :className="isLandScape?'hAddBtnStyle':'sAddBtnStyle'" @click="btnClick"> + </div>
		
		<!-- 模态框 -->
		<unik-modal ref="unikModal" :modalTitle="modalTitle" @confirmModal="confirmModal" @cancelModal="cancelModal" >
			<view class="model-box">
				<view class="m-single-item" >
					<view class="left" style="flex: 0.3;">歌名</view>
					<view class="right"><input type="text" v-model="name" placeholder="请输入歌名" placeholder-style="font-size:12px;" style="border-bottom: 1px solid #F1F1F1;flex: 3.5;"></view>
				</view>
				<view style="display: flex;flex-direction: row;width: 100%;">
					<view class="m-single-item"  >
						<view class="left">来源</view>
						<view class="right">
							<view class="example-body">
								<combox-search  emptyTips="无数据" @input="onInput"  :candidates="sourceList" placeholder="请选择" @getValue="getValue($event)" v-model="source" :value="source"></combox-search>
							</view>
								
						</view>
					</view>
					<view class="m-single-item" >
						<view class="left">类型</view>
						<view class="right" style="width: 100%;">
							<picker @change="bindChangeType" :value="tindex" :range="typeArray" style="width: 100%;border-bottom: 1px solid #F1F1F1;height: 85rpx;line-height: 85rpx;">
								<view style="padding-left: 10rpx;font-size: 14px;">{{typeArray[tindex]}}</view>
							</picker>
							
						</view>
					</view>
				</view>
				<view style="display: flex;flex-direction: row;width: 100%;">
					<view class="m-single-item" >
						<view class="left">日期</view>
						<view class="right">
							 <picker mode="date" :value="etime" :start="startDate" :end="endDate"  @change="bindDateChange" style="width: 100%;border-bottom: 1px solid #F1F1F1;height: 85rpx;line-height: 85rpx;">
								<view style="padding-left: 10rpx;font-size: 14px;">{{etime}}</view>
							</picker>
						</view>
					</view>
					<view class="m-single-item" >
						<view class="left">价格</view>
						<view class="right"><input v-model="money" type="digit" placeholder-style="font-size:12px;" placeholder="预计/实收金额" style="border-bottom: 1px solid #F1F1F1;"></view>
					</view>
				</view>
				<view style="display: flex;flex-direction: row;width: 100%;">
					<view class="m-single-item" style="width: 50%;" >
						<view class="left">评论</view>
						<view class="right"><switch v-model="is_comment" :checked="is_comment?true:false" @change="changeComment" /></view>
					</view>
					<view class="m-single-item" style="width: 50%;" >
						<view class="left">话题</view>
						<view class="right"><switch  v-model="is_topic" :checked="is_topic?true:false" @change="changeTopic" /></view>
					</view>
				</view>
				<view style="display: flex;flex-direction: row;width: 100%;">
					<view class="m-single-item" >
						<view class="left">回填</view>
						<view class="right"><switch v-model="is_back" :checked="is_back?true:false" @change="changeBack" /></view>
					</view>
					<view class="m-single-item" >
						<view class="left">统计</view>
						<view class="right"><switch v-model="is_count" :checked="is_count?true:false" @change="changeCount" /></view>
					</view>
				</view>
				<view style="display: flex;flex-direction: row;width: 100%;">
					<view class="m-single-item" >
						<view class="left">收款</view>
						<view class="right"><switch v-model="is_over" :checked="is_over?true:false" @change="changeOver" /></view>
					</view>
					<view class="m-single-item" >
					
					</view>
				</view>
				
				<view class="m-single-item" >
					<view class="left">点赞奖励</view>
					<view class="right"><input v-model="reward" type="text" placeholder-style="font-size:12px;"  placeholder="请输入点赞奖励" style="border-bottom: 1px solid #F1F1F1;width: 96%;"></view>
				</view>
			</view>
		</unik-modal>
	</view>
</template>

<script>
	import Pinyin from './js/characterToPinyin.js'
	import {isEqual} from './js/objEqual.js'
	// import DragButtonFollow from '@components/drag-button-follow/drag-button-follow.vue'
	import DragButtonFollow from '../drag-button-follow/drag-button-follow.vue'
	import unikModal from '../unik-modal/unik-modal.vue'
	import comboxSearch from "@/components/cuihai-combox/cuihai-combox.vue"
	import uniSection from "@/components/uni-section.vue"
	export default {
		data() {
			return { 
				bottomComputed: [],
				colorList: [],
				bgColorList: [],
				contentsSort: this.contents.slice(),
				oContentsSort: [],
				sortWay: 0,
				sortKeys: [],
				sortActiveKey: '',
				sortIsNumbers: [],
				checkAll: false,
				checkList: [],
				onload: true,
				event: {
					checkType: this.enableCheck,
					data: []
				},
				chars: '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ',
				finallyWidth:0,
				isLandScape:false,
				sAddBtnStyle: '',	// 竖屏添加按钮样式
				hAddBtnStyle: '',	// 横屏添加按钮样式
				id:0,
				name:'',
				source:'',
				is_comment:0,
				is_topic:0,
				is_back:0,
				is_count:0,
				is_over:0,
				money:'',
				type:'',
				reward:'',
				
				typeArray:['征稿','保底'],		// 类型数组
				tindex:0,						// 类型数组下标，默认第一个
				etime:'2021-01-01',
				sourceList: ['楚韵','网益','谷千','噼里啪啦','星斗推','豆姐','北忆','宇格'],
				countInfo:{totalMoney:0.00,waitMoney:0.00,hasMoney:0.00},	// 统计信息
				modalTitle:'添加',
				
				totalPage:1,			// 总页数
				page:1,					// 当前页 
				total:0,
			}
		},
		components: {
			unikModal,
			comboxSearch,
			uniSection,
		},
		computed: {
			startDate() {
				return this.getDate('start');
			},
			endDate() {
				return this.getDate('end');
			},
			loadingColor() {
				let color = this.loaderColor.slice()
				let rgbList = this.hexToRgb(color)
				let top = 'rgba(' + rgbList[0] + ',' + rgbList[1] + ',' + rgbList[2] + ', 0.3)'
				let bottom = 'rgba(' + rgbList[0] + ',' + rgbList[1] + ',' + rgbList[2] + ', 0.3)'
				let right = 'rgba(' + rgbList[0] + ',' + rgbList[1] + ',' + rgbList[2] + ', 0.3)'
				let left = 'rgb(' + rgbList[0] + ',' + rgbList[1] + ',' + rgbList[2] + ')'
				return {
					top,
					bottom,
					right,
					left
				}
			},
			contentLineKey() {
				return function(content, cIndex) {
					return this.randomString(32, this.chars)
				} 
			},
			contentItemKey() {
				return function(header, hIndex) {
					return this.randomString(16, this.chars)
				}
			},
			autoContentItem() {
				return function(cIndex, hIndex) {
					let content = this.contentsSort[cIndex]
					let header = this.headers[hIndex]
					let result = ''
					if (content[header.key] || content[header.key] === 0) {
						result = content[header.key]
						if (this.urlCol.length !== 0) {
							for (let i in this.urlCol) {
								let item = this.urlCol[i]
								if (header.key === item.key) {
									// 该单元格为链接
									result = content[header.key][0]
								}
							}
						}
						if (this.formatCol.length !== 0) {
							this.formatCol.forEach(item => {
								if (header.key === item.key) {
									let needRplace = new RegExp(`\#${item['key']}\#`, 'mg')
									result = item.template.replace(needRplace, result)
								}
							})
						}
						
					} else {
						result = this.emptyString
					}
					return result
				}
			},
			autoBottomComputedItem() {
				return function(index) {
					let bottomComputed = {}
					let needComputed = []
					this.computedCol.forEach(key => {
						let computedColData = []
						this.contentsSort.forEach(content => {
							computedColData.push(content[key] || '0')
						})
						needComputed.push(computedColData)
					})
					needComputed.forEach((item, index) => {
						let total = 0
						item.forEach(num => {
							total += parseFloat(num)
						})
						bottomComputed[this.computedCol[index]] = total
					})
					let header = this.headers[index]
					let result = this.computedCol.includes(header.key) ? 
						bottomComputed[header.key] : (index === 0 ? '总计' : this.emptyString)
					if (this.formatCol.length !== 0) {
						this.formatCol.forEach(item => {
							if (item.bottomComputedFormat) {
								if (header.key === item.key) {
									let needRplace = new RegExp(`\#${item['key']}\#`, 'mg')
									result = item.template.replace(needRplace, bottomComputed[item.key])
								}
							}
						})
					}
					if(result == '-'){
						result = ''
					}
					// console.log(result)
					return result
				}
			},
			autoTextDecoration() {
				return function(cIndex, hIndex) {
					let result = 'auto'
					let content = this.contentsSort[cIndex]
					let header = this.headers[hIndex]
					if (this.urlCol.length !== 0) {
						for (let i in this.urlCol) {
							let item = this.urlCol[i]
							if (header.key === item.key) {
								// 该单元格为链接
								if (content[header.key]) {
									result = 'underline'
								}
							}
						}
					}
					return result
				}
			},
			autoContentBgColor() {
				return function(cIndex, hIndex) {
					let result = this.contentBgColor
					let content = this.contentsSort[cIndex]
					let header = this.headers[hIndex]
					let keys = []
					// 先判断是不是首列，设置基础样式
					if (hIndex === 0) {
						result = this.firstColBgColor
					}
					// 再判断条件格式传没传值，设置条件样式
					if (this.valueFormat.length !== 0) {
						this.valueFormat.forEach(item => {
							keys.push(item.key)
						})
						if (keys.includes(header.key)) {
							// 该列开启了条件格式
							let key = header.key
							let type = this.valueFormat[keys.indexOf(key)].type
							let style = this.valueFormat[keys.indexOf(key)].style
							let range = this.valueFormat[keys.indexOf(key)].range || ''
							switch(type) {
								case 'bigger':
									if (parseFloat(content[key]) > range) {
										if (style.bgColor) result = style.bgColor
									}
									break
								case 'smaller':
									if (parseFloat(content[key]) < range) {
										if (style.bgColor) result = style.bgColor
									}
									break
								case 'equal':
									let val
									if (typeof range === 'number') val = parseFloat(content[key])
									else val = content[key]
									if (val === range) {
										if (style.bgColor) result = style.bgColor
									}
									break
								case 'range':
									if (parseFloat(content[key]) > range[0] && parseFloat(content[key]) < range[1]){
										if (style.bgColor) result = style.bgColor
									}
									break
								case 'average-bigger':
									let average = this.getAverage(key)
									if (parseFloat(content[key]) > average) {
										if (style.bgColor) result = style.bgColor
									}
									break
								case 'average-smaller':
									average = this.getAverage(key)
									if (parseFloat(content[key]) < average) {
										if (style.bgColor) result = style.bgColor
									}
									break
								case 'average-equal':
									average = this.getAverage(key)
									if (parseFloat(content[key]) === average) {
										if (style.bgColor) result = style.bgColor
									}
									break
							}
						}
					}
					return result
				}
			},
			autoContentColor() {
				return function(cIndex, hIndex) {
					let result = this.contentFtColor
					let content = this.contentsSort[cIndex]
					let header = this.headers[hIndex]
					let keys = []
					// 先判断是不是链接，设置基础样式
					if (this.urlCol.length !== 0) {
						for (let i in this.urlCol) {
							let item = this.urlCol[i]
							if (header.key === item.key) {
								// 该单元格为链接
								if (content[header.key]) {
									result = this.linkColor
								}
							}
						}
					}
					// 再判断条件格式传没传值，设置条件样式
					if (this.valueFormat.length !== 0) {
						this.valueFormat.forEach(item => {
							keys.push(item.key)
						})
						if (keys.includes(header.key)) {
							// 该列开启了条件格式
							let key = header.key
							let type = this.valueFormat[keys.indexOf(key)].type
							let style = this.valueFormat[keys.indexOf(key)].style
							let range = this.valueFormat[keys.indexOf(key)].range || ''
							switch(type) {
								case 'bigger':
									if (parseFloat(content[key]) > range) {
										if (style.color) result = style.color
									}
									break
								case 'smaller':
									if (parseFloat(content[key]) < range) {
										if (style.color) result = style.color
									}
									break
								case 'equal':
									let val
									if (typeof range === 'number') val = parseFloat(content[key])
									else val = content[key]
									if (val === range) {
										if (style.color) result = style.color
									}
									break
								case 'range':
									if (parseFloat(content[key]) > range[0] && parseFloat(content[key]) < range[1]){
										if (style.color) result = style.color
									}
									break
								case 'average-bigger':
									let average = this.getAverage(key)
									if (parseFloat(content[key]) > average) {
										if (style.color) result = style.color
									}
									break
								case 'average-smaller':
									average = this.getAverage(key)
									if (parseFloat(content[key]) < average) {
										if (style.color) result = style.color
									}
									break
								case 'average-equal':
									average = this.getAverage(key)
									if (parseFloat(content[key]) === average) {
										if (style.color) result = style.color
									}
									break
							}
						}
					}
					return result
				}
			},
			autoSortShow() {
				return function(hIndex) {
					let result = false
					let header = this.headers[hIndex]
					let keys = []
					// 判断排序是否传值
					if (this.sortCol.length !== 0 && this.sortKeys.length === 0) {
						this.sortCol.forEach(item => {
							keys.push(item.key)
						})
						this.sortKeys = keys
						if (keys.includes(header.key)) {
							result = true
						}
					} else if (this.sortCol.length !== 0) {
						if (this.sortKeys.includes(header.key)) {
							result = true
						}
					}
					return result
				}
			},
			screenWidth() {
				return `${uni.getSystemInfoSync()['screenWidth']}px`
			}
		},
		props: {
			headers: {
				type: Array,
				default() {
					return [{
						key: 'name',
						label: '姓名'
					}]
				}
			},
			contents: {
				type: Array,
				default() {
					return [{
						name: '张三'
					}, {
						name: '李四'
					}]
				}
			},
			emptyString: {
				type: String,
				default: '-'
			},
			width: {
				type: String,
				default: `${uni.getSystemInfoSync().screenWidth}px`
			},
			height: {
				type: String,
				default: 'auto'
			},
			fontSize: {
				type: Array,
				default() {
					return [30]
				}
			},
			defaultColWidth: {
				type: Number,
				default: 165
			},
			headerWeight: {
				type: Boolean,
				default: true
			},
			minHeight: {
				type: Array,
				default() {
					return [70]
				}
			},
			headerBgColor: {
				type: String,
				default: '#f1f1f1'
			},
			contentBgColor: {
				type: String,
				default: '#fff'
			},
			headerFtColor: {
				type: String,
				default: '#3e3e3e'
			},
			contentFtColor: {
				type: String,
				default: '#3e3e3e'
			},
			linkColor: {
				type: String,
				default: '#0024c8'
			},
			firstColBgColor: {
				type: String,
				default: '#f1f1f1'
			},
			firstLineFixed: {
				type: Boolean,
				default: false
			},
			textAlign: {
				type: String,
				default: 'center'
			},
			padding: {
				type: Array,
				default() {
					return [5, 10]
				}
			},
			borderColor: {
				type: String,
				default: '#e1e1e1'
			},
			urlCol: {
				type: Array,
				default() {
					return []
				}
			},
			computedCol: {
				type: Array,
				default() {
					return []
				}
			},
			bottomComputedFixed: {
				type: Boolean,
				default: true
			},
			valueFormat: {
				type: Array,
				default() {
					return []
				}
			},
			formatCol: {
				type: Array,
				default() {
					return []
				}
			},
			showLeftAndRightBorder: {
				type: Boolean,
				default: false
			},
			showVertBorder: {
				type: Boolean,
				default: true
			},
			sortCol: {
				type: Array,
				default() {
					return []
				}
			},
			sortWays: {
				type: Array,
				default() {
					return ['none', 'asc', 'inv']
				}
			},
			loading: {
				type: Boolean,
				default: false
			},
			loaderSize: {
				type: [String, Number],
				default: 50
			},
			loaderColor: {
				type: String,
				default: '#a3a3a3'
			},
			loaderBgColor: {
				type: String,
				default: '#f8f8f8'
			},
			enableCheck: {
				type: String,
				default: ''
			},
			checkColWidth: {
				type: [String, Number],
				default: '80'
			},
			checkerColor: {
				type: String,
				default: '#3e3e3e'
			},
			checkerBorderColor: {
				type: String,
				default: '#d3d3d3'
			},
			checkerBgColor: {
				type: String,
				default: 'rgba(0, 0, 0, 0)'
			},
			checkerBoxBgColor: {
				type: String,
				default: 'rgba(0, 0, 0, 0)'
			},
			checkerCellBgColor: {
				type: String,
				default: '#f1f1f1'
			}
		},
		watch: {
			headers(val) {
				// this.$refs.unikModal.show()
				var that = this
				uni.getSystemInfo({
					success: function(res) {  
						console.log(res.windowWidth,that.width)
						var isLandScape = (res.windowWidth > res.windowHeight)?true:false
						that.isLandScape = isLandScape
						var finallyWidth = 0
						for(var i=0;i<val.length;i++){
							finallyWidth += val[i].width!=undefined? parseInt(val[i].width):165
						}
						if(isLandScape){
							finallyWidth = finallyWidth - 565
						}else{
							finallyWidth = finallyWidth - 170
						}
						that.finallyWidth = finallyWidth
						// console.log(finallyWidth)
					}  
				})
				
				this.$forceUpdate()
			},
			contents(val) {
				this.contentsSort = val.slice()
				if (this.onload) {
					this.contentsSort.forEach(item => {
						this.$set(item, 'checked', false)
					})
					this.oContentsSort = this.contentsSort.slice()
					this.onload = false
				}
				this.$forceUpdate()
			}
		},
		mounted() {
			this.contentsSort.forEach(item => {
				this.$set(item, 'checked', false)
			})
			this.oContentsSort = this.contentsSort.slice()
			if (this.sortCol.length !== 0) {
				this.sortActiveKey = this.sortCol[0].key
				uni.setStorageSync('lastSortActiveKey', this.sortActiveKey)
				this.doSort(this.sortCol[0].key, this.sortWays[this.sortWay], this.sortCol[0].isNumber)
			}
			
			var date = new Date();
			var year = date.getFullYear().toString(); //获取完整的年份(4位)
			var month = (date.getMonth()+1).toString(); //获取当前月份(0-11,0代表1月)
			if(month.length<2){
				month = '0' + month
			}
			
			var day = date.getDate(); //获取当前日(1-31)
			this.etime = year + '-' +  month +'-' + day
			
			// console.log(this.contentsSort)
			this.getMusicList({page:this.page})
			
			
			
			var that=this;
			uni.$on('onReachBottom', function(data) {
				console.log('onReachBottom');
				if(that.page >= that.totalPage){
					return ;	// 没有了
				}
				// setTimeout(function () {
				   that.getMusicList({ page: that.page + 1 });
				// }, 50)
				
			});
			
			
			uni.$on('onPageScroll', function(data) {
				console.log('onPageScroll');
			});
		},
		onLoad() {
		},
		onPageScroll(){
			console.log(33)
		},
	
		methods: {
			// 编辑
			handleEdit(e){
				// console.log(e.currentTarget.dataset.hindex)
				var hindex = e.currentTarget.dataset.hindex
				if(hindex !=0){
					return;
				}
				
				this.id = e.currentTarget.dataset.id
				this.name = e.currentTarget.dataset.name
				this.source = e.currentTarget.dataset.source
				this.type = e.currentTarget.dataset.type
				this.tindex = e.currentTarget.dataset.type
				this.money = e.currentTarget.dataset.money
				this.is_comment = e.currentTarget.dataset.is_comment
				this.is_topic = e.currentTarget.dataset.is_topic
				this.is_back = e.currentTarget.dataset.is_back
				this.is_count = e.currentTarget.dataset.is_count
				this.is_over = e.currentTarget.dataset.is_over
				this.reward = e.currentTarget.dataset.reward
				this.modalTitle = '编辑'
				this.$refs.unikModal.show()
				console.log(this.name,this.source,this.etime,this.is_comment,this.is_topic,this.is_back,this.is_count,this.money,this.type,this.reward)
			},
			
			// 获取列表
			getMusicList(e={}){
				console.log(e)
				uni.request({
				    url: 'https://gaogeqiang.peterz.top/video/dy/getList.php', 
					method:'GET',
					data:e,
				    success: (res) => {
						this.page = res.data.data.pageData.current_page;			// 当前页
						this.totalPage = res.data.data.pageData.last_page;		// 总页数
						this.total = res.data.data.count.count 
						
						// console.log(this.page,this.totalPage)
						if(this.page == 1){	// 不同列表  不合并,覆盖
							this.contentsSort = res.data.data.list
							// console.log(res.data.data)
						}else{	// 相同列表 分页,合并数组
							var tempList = res.data.data.list				// 临时列表
							this.contentsSort = this.contentsSort.concat(tempList);	// 将新查询的数组放到末尾
						}
						// console.log(res.data,this.contentsSort)
						// this.contentsSort = res.data.data.list
						this.sourceList = res.data.data.sourceList
						this.oContentsSort = JSON.parse(JSON.stringify(res.data.data.list)) 
						this.countInfo = res.data.data.count
				    }
				});
			},
			
			
			changeComment(e){
				this.is_comment = e.detail.value?1:0
			},
			changeTopic(e){
				this.is_topic = e.detail.value?1:0
			},
			changeBack(e){
				this.is_back = e.detail.value?1:0
			},
			changeCount(e){
				this.is_count = e.detail.value?1:0
			},
			changeOver(e){
				this.is_over = e.detail.value?1:0
			},
			
			// 监听输入框的值
			onInput(e){
				console.log(e)
				this.source=e;
			},
			
			// 获取来源下拉框的值
			getValue(e){
				this.source=e;
				console.log(e);
			},
		
			// 添加歌曲
			btnClick(){
				this.modalTitle = '添加'
				this.id = 0
				this.name = ''
				this.source = ''
				this.money = ''
				this.type = 0
				this.is_comment = 1
				this.is_topic = 1
				this.is_back = 1
				this.is_count = 1
				this.is_over = 0
				this.reward = ''
				this.$refs.unikModal.show()
				console.log(88)
			},
			//修改日期
			bindDateChange(e){
				this.etime = e.target.value
			},
			getDate(type) {
				const date = new Date();
				let year = date.getFullYear();
				let month = date.getMonth() + 1;
				let day = date.getDate();
	
				if (type === 'start') {
					year = year - 60;
				} else if (type === 'end') {
					year = year + 2;
				}
				month = month > 9 ? month : '0' + month;
				day = day > 9 ? day : '0' + day;
				return `${year}-${month}-${day}`;
			},
			
			// 修改类型
			bindChangeType(e){
				this.tindex = e.detail.value
			},
			
			confirmModal () {
				console.log(this.name,this.source,this.etime,this.is_comment,this.is_topic,this.is_back,this.is_count,this.money,this.type,this.reward)
				// console.log("您点击了提交");
				uni.request({
				    url: 'https://gaogeqiang.peterz.top/video/dy/addMusic.php', 
					method:'GET',
					data:{
						id:this.id,
						name:this.name,
						source:this.source,
						etime:this.etime,
						is_comment:this.is_comment,
						is_topic:this.is_topic,
						is_back:this.is_back,
						is_count:this.is_count,
						is_over:this.is_over,
						money:this.money,
						type:this.tindex,
						reward:this.reward,
					},
				    success: (res) => {
						console.log(res)
						// return;
						if(res.data.code == 400){
							return uni.showToast({
							    title: res.data.msg,
								icon:'none',
							    duration: 2000
							});
						}else{
							uni.showToast({
								title: "操作成功",
								'icon':'none'
							})
							this.getMusicList({page:this.page})
							this.$refs.unikModal.closeModal()
							this.id = 0
						}
				    }
				});
				
				
			},
			cancelModal() {
				console.log("您点击了关闭");
			},
		
			// 修改switch
			switchChange(e){
				var index = e.currentTarget.dataset.hindex
				var id = e.currentTarget.dataset.id
				if(index == 3){
					var key = "is_comment"
				}else if(index == 4){
					var key = "is_topic"
				}else if(index == 5){
					var key = "is_back"
				}else if(index == 6){
					var key = "is_count"
				}else if(index == 7){
					var key = "is_over"
				}
				uni.showLoading({
				    title: '修改中~',
				});
				uni.request({
				    url: 'https://gaogeqiang.peterz.top/video/dy/switch.php', 
					method:'GET',
					data:{
						id:id,
						status:e.detail.value?1:0,
						key:key
					},
				    success: (res) => {
						if(res.data.code == 400){
							return uni.showToast({
							    title: res.data.msg,
								icon:'none',
							    duration: 2000
							});
						}else{
							uni.showToast({
								title: "修改成功",
								'icon':'none'
							})
							this.getMusicList({page:this.page})
						}
						uni.hideLoading();
				    }
				});
				console.log(e.detail.value,index,id)
			},
			
			
			doSort(key, type, isNumber) {
				let arr = this.contentsSort
				if (type === 'asc') {
					// 升序
					if (isNumber) {
						arr.sort((a, b) => {
							if(key == 'money'){
								return (parseFloat(a[key].toString()) || 0) -
									(parseFloat(b[key].toString()) || 0)
							}else{
								return (parseFloat(a[key].toString().replace(/[^0-9]/ig, "")) || 0) -
									(parseFloat(b[key].toString().replace(/[^0-9]/ig, "")) || 0)
							}
						})
					} else {
						arr.sort((a, b) => {
							let A = Pinyin.getSpell(a[key].charAt(0), function(charactor, spell) {
								return spell[1]
							}).charAt(0).charCodeAt()
							let B = Pinyin.getSpell(b[key].charAt(0), function(charactor, spell) {
								return spell[1]
							}).charAt(0).charCodeAt()
							return A - B
						})
					}
					
				} else if (type === 'inv') {
					// 倒序
					if (isNumber) {
						arr.sort((a, b) => {
							if(key == 'money'){
								return (parseFloat(b[key].toString()) || 0) -
									(parseFloat(a[key].toString()) || 0)
							}else{
								return (parseFloat(b[key].toString().replace(/[^0-9]/ig, "")) || 0) -
									(parseFloat(a[key].toString().replace(/[^0-9]/ig, "")) || 0)
							}
						})
					} else {
						arr.sort((a, b) => {
							let A = Pinyin.getSpell(a[key].charAt(0), function(charactor, spell) {
								return spell[1]
							}).charAt(0).charCodeAt()
							let B = Pinyin.getSpell(b[key].charAt(0), function(charactor, spell) {
								return spell[1]
							}).charAt(0).charCodeAt()
							return B - A
						})
					} 
				} else {
					// console.log(896)
					// this.contentsSort = this.oContentsSort.slice()
					this.contentsSort = this.oContentsSort
				}
				if (this.enableCheck) {
					this.event.data.forEach(item => {
						this.contentsSort.forEach((content, index) => {
							if (isEqual(item.lineData, content)) {
								item.index = index
							}
						})
					})
				}
				this.$forceUpdate()
			},
			initBottomComputed() {
				let result = {}
				let needComputed = []
				this.computedCol.forEach(key => {
					let computedColData = []
					this.contentsSort.forEach(content => {
						computedColData.push(content[key] || '0')
					})
					needComputed.push(computedColData)
				})
				needComputed.forEach((item, index) => {
					let total = 0
					item.forEach(num => {
						total += parseFloat(num)
					})
					result[this.computedCol[index]] = total
				})
				this.bottomComputed = result
			},
			onHeaderItemTap(index) {
				let header = this.headers[index]
				const lastSortActiveKey = uni.getStorageSync('lastSortActiveKey') || ''
				if (this.sortCol.length !== 0) {
					if (this.sortKeys.includes(header.key)) {
						// 当前列开启了排序
						this.sortActiveKey = header.key
						uni.setStorageSync('lastSortActiveKey', this.sortActiveKey)
						if (this.sortWay < 2 && lastSortActiveKey === this.sortActiveKey) {
							this.sortWay++
						} else if (lastSortActiveKey !== this.sortActiveKey) {
							this.sortWay = 1
						} else if (this.sortWay >= 2) {
							this.sortWay = 0
						}
						let isNumber = this.sortCol[this.sortKeys.indexOf(header.key)].isNumber
						this.doSort(header.key, this.sortWays[this.sortWay], isNumber)
					}
				}
			},
			onContentItemTap(cIndex, hIndex) {
				let event = {}
				let content = this.contentsSort[cIndex]
				let header = this.headers[hIndex]
				let keys = []
				
				if (this.urlCol.length !== 0) {
					for (let i in this.urlCol) {
						let item = this.urlCol[i]
						keys.push(item.key)
					}
				}
				
				if (content[header.key]) {
					if (keys.includes(header.key)) {
						// 该单元格为链接
						switch(this.urlCol[keys.indexOf(header.key)].type) {
							case 'route':
								let url = content[header.key][1]
								if (content[header.key][2]) {
									url = `${url}?`
									Object.keys(content[header.key][2]).forEach(key => {
										url += `&${key}=${content[header['key']][2][key]}`
									})
								}
								uni.navigateTo({url})
								break
							case 'http':
								this.openURL(content[header.key][1])
								break
						}
					} else {
						event = {
							content: content[header.key],
							contentIndex: cIndex,
							header: header.label,
							headerIndex: hIndex,
							key: header.key,
							lineData: content
						}
						this.$emit('onCellClick', event)
					}
					
				} else {
					event = {
						content: '',
						contentIndex: cIndex,
						header: header.label,
						headerIndex: hIndex,
						key: header.key,
						lineData: content
					}
					if (keys.includes(header.key)) {
						// 该单元格为链接
						event['isLink'] = true
					}
					this.$emit('onCellClick', event)
				}
				
			},
			onCheckAllTap() {
				if (this.enableCheck === 'multiple') {
					let checkList = []
					this.contentsSort.forEach(item => {
						checkList.push(item.checked)
					})
					this.checkList = checkList
					if (!this.checkAll) {
						this.checkAll = true
						this.contentsSort.forEach(item => {
							item.checked = true
						})
						this.event.data = []
						this.contentsSort.forEach((content, index) => {
							this.event.data.push({
								index,
								lineData: content
							})
						})
						
					} else {
						this.checkAll = false
						this.event.data = []
						this.contentsSort.forEach(item => {
							item.checked = false
						})
					}
					this.$emit('onCheck', this.event)
				}
			},
			onCheckItemTap(cIndex) {
				let content = this.contentsSort[cIndex]
				if (this.enableCheck === 'single') {
					this.contentsSort.forEach((item, index) => {
						if (cIndex === index) {
							item.checked = !item.checked
						} else {
							item.checked = false
						}
					})
				} else if (this.enableCheck === 'multiple') {
					this.contentsSort[cIndex]['checked'] = !this.contentsSort[cIndex]['checked']
				}
				if (this.contentsSort[cIndex]['checked']) {
					if (this.enableCheck === 'single') {
						this.event.data = []
					}
					this.event.data.push({
						index: cIndex,
						lineData: this.contentsSort[cIndex]
					})
				} else {
					this.event.data.forEach(item => {
						if (item.index === cIndex) this.event.data.splice(this.event.data.indexOf(item), 1)
					})
					if (this.event.data.length === 0) {
						this.checkAll = false
					}
				}
				this.$forceUpdate()
				this.$emit('onCheck', this.event)
			},
			openURL(href) {
				// #ifdef APP-PLUS
				plus.runtime.openURL(href)
				// #endif
				// #ifdef H5
				window.open(href)
				// #endif
				// #ifdef MP
				uni.setClipboardData({
					data: href,
					success() {
						uni.showToast({
							title: '网址已复制，请在手机浏览器里粘贴该网址',
							icon: 'none'
						})
					}
				})
				// #endif
			},
			getAverage(key) {
				let numList = []
				this.contentsSort.forEach(content => {
					numList.push(parseFloat(content[key]) || 0)
				})
				return numList.reduce((a, b) => a + b) / numList.length
			},
			getTotal(key) {
				let numList = []
				this.contentsSort.forEach(content => {
					numList.push(parseFloat(content[key]) || 0)
				})
				return numList.reduce((a, b) => a + b)
			},
			RGBChange(color, level, type) {
				// 判断颜色类型
				let r = 0,
					g = 0,
					b = 0,
					hasAlpha = false,
					alpha = 1
				if (color.indexOf('#') !== -1) {
					// hex转rgb
					if (color.length === 4) {
						let arr = color.split('')
						color = '#' + arr[1] + arr[1] + arr[2] + arr[2] + arr[3] + arr[3]
					}
					let color16List = [color.substring(1, 3), color.substring(3, 5), color.substring(5, 7)]
					r = parseInt(color16List[0], 16)
					g = parseInt(color16List[1], 16)
					b = parseInt(color16List[2], 16)
			
				} else {
					hasAlpha = color.indexOf('a') !== -1
					let root = color.slice()
					let idx = root.indexOf('(') + 1
					root = root.substring(idx)
					let firstDotIdx = root.indexOf(',')
					r = parseFloat(root.substring(0, firstDotIdx))
					root = root.substring(firstDotIdx + 1)
					let secondDotIdx = root.indexOf(',')
					g = parseFloat(root.substring(0, secondDotIdx))
					root = root.substring(secondDotIdx + 1)
					if (hasAlpha) {
						let thirdDotIdx = root.indexOf(',')
						b = parseFloat(root.substring(0, thirdDotIdx))
						alpha = parseFloat(root.substring(thirdDotIdx + 1))
					} else {
						b = parseFloat(root)
					}
				}
			
				let rgbc = [r, g, b]
				// 减淡或加深
				for (var i = 0; i < 3; i++)
					type === 'light' ? rgbc[i] = Math.floor((255 - rgbc[i]) * level + rgbc[i]) : rgbc[i] = Math.floor(rgbc[i] * (1 -
						level))
			
				if (hasAlpha) {
					return `rgba(${rgbc[0]}, ${rgbc[1]}, ${rgbc[2]}, ${alpha})`
				} else {
					return `rgb(${rgbc[0]}, ${rgbc[1]}, ${rgbc[2]})`
				}
			},
			hexToRgb(color) {
				if (color.length === 4) {
					let arr = color.split('')
					color = '#' + arr[1] + arr[1] + arr[2] + arr[2] + arr[3] + arr[3]
				}
				let color16List = [color.substring(1, 3), color.substring(3, 5), color.substring(5, 7)]
				let r = parseInt(color16List[0], 16)
				let g = parseInt(color16List[1], 16)
				let b = parseInt(color16List[2], 16)
				return [r, g, b]
			},
			randomString(length, chars) {
			    var result = ''
			    for (var i = length; i > 0; --i) result += chars[Math.floor(Math.random() * chars.length)]
			    return result
			}
		}
	}
</script>

<style>
	@import './css/iconfont.css';
	@import './css/loader.css';
	.ios-header-bug {
		height: 0;
		width: 1px;
		opacity: 0;
	}
	
	.wyb-table-scroll-view {
		overflow: scroll;
		-webkit-overflow-scrolling: touch;
	}
	
	.wyb-table-scroll-view::-webkit-scrollbar {
		display: none;
		/* #ifdef MP-WEIXIN */
		width: 0;
		height: 0;
		/* #endif */
	}
	
	.wyb-table-loading-box {
		display: flex;
		align-items: center;
		justify-content: center;
		z-index: 500;
	}
	
	.wyb-table-header {
		position: sticky;
		top: 0;
		display: grid;
		grid-auto-flow: column;
		width: max-content;
		z-index: 25;
	}
	
	.wyb-table-header-item {
		flex: 1;
		display: flex;
		align-items: center;
		box-sizing: border-box;
		position: relative;
	}
	
	.wyb-table-header-icon {
		display: flex;
		flex-direction: column;
	}
	
	.wyb-table-content-line {
		display: grid;
		grid-auto-flow: column;
		width: max-content;
		position: relative;
	}
	
	.wyb-table-content-item {
		display: flex;
		flex-direction: row;
		align-items: center;
		box-sizing: border-box;
	}
	.wyb-table-content-item text{
		font-weight: bold;
		margin-right: 6rpx;
	}
	.wyb-table-content-item text:nth-child(1){
		color: #00baff;
	}
	.wyb-table-content-item text:nth-child(2){
		color: #04be02;
	}
	.wyb-table-content-item text:nth-child(3){
		color: orange;
	}
	.wyb-table-content-item text:nth-child(4){
		color: #c20c0c;
	}
	
	.wyb-table-checkbox {
		border-radius: 3px;
		display: flex;
		align-items: center;
		justify-content: center;
		position: relative;
	}
	
	.icon-check {
		width: 100%;
		height: 100%;
		position: absolute;
		border-radius: 0;
		border-radius: 3px;
		font-weight: bold;
		box-sizing: border-box;
		transform: scale(1.1);
	}
	switch{transform:scale(0.6)}
	
	.hAddBtnStyle{
		background: #FFFFFF;border: 0.5px solid #EEEEEE;box-shadow: 0 5rpx 10rpx 0 rgba(0,0,0,0.08);width: 50rpx;height: 50rpx;line-height:50rpx;font-size: 30px;color: #999;position: fixed;right: 20rpx;bottom: 28rpx;border-radius: 100%;display: flex;align-items: center;justify-content: center;font-weight:normal;display:flex;flex-direction:row;align-items:center;
	}
	.sAddBtnStyle{
		background: #FFFFFF;border: 0.5px solid #EEEEEE;box-shadow: 0 5rpx 10rpx 0 rgba(0,0,0,0.08);width: 100rpx;height: 100rpx;font-size: 30px;color: #999;position: fixed;right: 40rpx;bottom: 188rpx;border-radius: 100%;display: flex;align-items: center;justify-content: center;
	}
	
	/* 模态框 */
	.model-box{
		width: 100%; display: flex;flex-direction: column;align-items: center;
	}
	.m-single-item{
		display: flex;flex-direction: row;width: 100%;padding:15rpx 0 0 0;
	}
	.m-single-item .left{
		flex: 0.7;text-align: left;font-size: 30rpx;color:rgb(0,0,0);font-weight: bold;height:85rpx;line-height:85rpx;
	}
	.m-single-item .right{
		flex: 2.0;text-align: left;font-size: 30rpx;color: rgb(51,51,51);font-size:28rpx ;justify-content: flex-start;display: flex;flex-direction: row;align-items: center;text-align: left;
	}
	.m-single-item input{height:85rpx;line-height:85rpx;padding: 0 10rpx;}
	.model-box switch{transform:scale(0.7);position: relative;top: 5rpx;}

</style>
