<template>
	<view>
		<view class="swiper">
			<view class="page-section swiper">
				<view class="page-section-spacing">
					<swiper class="swiper" :indicator-dots="indicatorDots" :autoplay="autoplay" :interval="interval" :duration="duration">
						<swiper-item v-for="item in banner" :key="item.id">
							<image @tap='bannerDetail(item.url)' :src="item.image"  class="swiper-item" />
						</swiper-item>
					</swiper>
				</view>
			</view>
		</view>		
		<zy-grid :grid-list=gridList :show-tip="true" :col="3"></zy-grid>
<!-- 		<view class="uni-list">
			<view class="uni-list-cell" hover-class="uni-list-cell-hover" v-for="(value,key) in listData" :key="key" @tap='showDetail(value)'>
				<view class="uni-list-cell-navigate uni-navigate-right uni-media-list ">
					<view class="uni-media-list-logo">
						<image :src="value.image_url ? value.image_url : '../../static/logo.png'"></image>
					</view>
					<view class="uni-media-list-body">
						<view class="uni-media-list-text-top uni-ellipsis">{{value.title}}</view>
						<view class="uni-media-list-text-bottom uni-ellipsis">发表于：{{value.datetime}}</view>
					</view>
				</view>
			</view>
		</view> -->
	</view>
</template>
<script>
	var api = require('@/common/api.js'),
		page = 1,
		reachbottom = true
	import permision from "@/common/permission.js"	
	import zyGrid from '@/components/zy-grid/zy-grid.vue'
	import {
		friendlyDate
	} from '@/common/util.js'
	export default {
		data() {
			return {
				banner: [],
				listData: [],
				indicatorDots: true,
				autoplay: true,
				interval: 3000,
				duration: 500,
				keyword: "",
				title: 'scanCode',
				result: '',
				loading: false,
				username: "",
				password: "",
				gridList: [	//格子数据列表
					{
						name: '出库->销货',
						imgUrl: '../../static/zy-grid/grid-04.svg',
						tips: ''
					},
					{
						name: '制令->缴库',
						imgUrl: '../../static/zy-grid/grid-07.svg',
						tips: ''
					},
					{
						name: '客户列表',
						imgUrl: '../../static/zy-grid/grid-08.svg',
						tips: ''
					}
					//,
					// {
					// 	name: '单据查找',
					// 	imgUrl: '../../static/zy-grid/grid-10.svg',
					// 	tips: ''
					// },
					// {
					// 	name: '扫码缴库',
					// 	imgUrl: '../../static/zy-grid/grid-02.svg',
					// 	tips: ''
					// },
					// {
					// 	name: '扫码出库',
					// 	imgUrl: '../../static/zy-grid/grid-01.svg',
					// 	tips: ''
					// }									
				]
			}
		},
		components: {
			zyGrid
		},
		onLoad() {
			this.loadBanner()			
		},
		onReady() {			
			
		},
		//下拉刷新
		onPullDownRefresh() {
			page = 1
			reachbottom = true
			this.listData = []
			this.loadBanner()			
			uni.stopPullDownRefresh();
		},
		// 加载更多
		onReachBottom: function() {
			//if (reachbottom) {
				//this.getList();
			//}
		},
		methods: {
			/**
			 * 加载幻灯片
			 */
			loadBanner: function() {
				var items=[
					{id:1,url:"../../static/title1.jpg",image:"../../static/title1.jpg"},
					{id:2,url:"../../static/title1.jpg",image:"../../static/title1.jpg"}
				];
				this.banner =items;
				
/* 				api.get({
					url: 'home/slides/1',
					data: {},
					success: data => {
						//console.log(data);
						if (data.code == 1) {
							this.banner = data.data[0].items;							
						}
						this.getList();
					}
				}); */
			},
			
			bannerDetail(detail) {
				// uni.navigateTo({
				// 	url: '/pages/article/article?id=' + detail
				// });
			}
		}
	}
</script>

<style>
	.swiper {
		height: 350upx;
	}

	.swiper-item {
		display: block;
		height: 350upx;
		width: 100%;
	}

	.uni-media-list-logo {
		width: 100upx;
		height: 100upx;
	}
	.scan-result {
		min-height: 50upx;
		line-height: 50upx;
	}
	.logbt {
		background: none;
		border: none !important;
		position: fixed;
		display: inline;
		margin-left: -76upx
	}

	.logbt:after {
		border: none !important;
	}

	image {
		width: 100upx;
		height: 100upx;
	}

	.landing {
		height: 84upx;
		line-height: 84upx;
		border-radius: 44upx;
		font-size: 32upx;
		background: linear-gradient(left, #86B5F4, #4790EF);
	}

	.login-btn {
		padding: 10upx 20upx;
		margin-top: 350upx;
	}

	.login-function {
		overflow: auto;
		padding: 20upx 20upx 30upx 20upx;
	}

	.login-forget {
		float: left;
		font-size: 26upx;
		color: #999;
	}

	.login-register {
		color: #666;
		float: right;
		font-size: 26upx;

	}

	.login-input input {
		background: #F2F5F6;
		font-size: 28upx;
		padding: 10upx 25upx;
		height: 62upx;
		line-height: 62upx;
		border-radius: 8upx;
	}

	.login-margin-b {
		margin-bottom: 25upx;
	}

	.login-input {
		padding: 10upx 20upx;
	}

	.login-head {
		font-size: 34upx;
		text-align: center;
		padding: 25upx 10upx 55upx 10upx;
	}

	.login-card {
		background: #fff;
		border-radius: 12upx;
		padding: 10upx 25upx;
		box-shadow: 0 6upx 18upx rgba(0, 0, 0, 0.12);
		position: relative;
		margin-top: 120upx;
	}

	.login-bg {
		height: 260upx;
		padding: 25upx;
		background: linear-gradient(#86B5F4, #4790EF);
	}
</style>
