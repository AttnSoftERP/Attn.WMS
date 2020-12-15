<template>
	<view>		
		<view class="margin-top-sm" id="search">			
		</view>
		<scroll-view id="scroll" scroll-y = "true" :style="{height:scrollHeight}">
			<view class="cu-list menu card-menu margin-top-sm">
				<view v-for="(item,key) in list" :key="key" class="cu-item">
					<view class="content padding-tb-sm">
						<view>客户名称：{{item.NAME}}</view>
						<view class="text-gray ">							 
							 联系人：{{item.CNT_MAN1}}							 
						</view>
						<view class="text-gray ">
							 电话：{{item.TEL1}}
						</view>
					</view>
					<view class="action">						
						<!-- <template v-if="item.status==0"> -->
						<view class="cu-tag round bg-green">正常合作</view>
						<!-- </template> -->
<!-- 						<template v-else>
						<view class="cu-tag round bg-orange">暂停合作</view>
						</template>	 -->					
					</view>
				</view>
			</view>
		</scroll-view>

		
	</view>
</template>

<script>
	var api = require('@/common/api.js');	
	export default {
		data() {
			return {								
				scrollHeight:'',
				list:[]
			}
		},		
		onReady() {
			let _this = this;
			let segmented = uni.createSelectorQuery().select("#search");
			let sysinfo = uni.getSystemInfoSync();
			let Height = sysinfo.windowHeight;
			segmented.boundingClientRect(data=>{
				console.log(data);
				let sH = (Height - data.top - 46).toFixed();
				_this.scrollHeight = sH+'px';
				console.log(_this.scrollHeight);
			}).exec();
		},
		onLoad(){
			api.post({
				url: 'Cust/GetList',
				data: {
					device_type: api.DeviceType
				},
				success: data => {
					console.log(data);
					if(data.Status == 'success') {
						this.list = data.Data;												
					}
				}
			});			
		},
		methods: {
			
		}		
	}
</script>

<style>	
</style>
