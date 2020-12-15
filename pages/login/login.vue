<template>	
	<view class="content">
		<view class="avatorWrapper">
			<view class="avator">
				<image class="img" src="../../static/sunlike.png" mode="widthFix"></image>
			</view>		
		</view>
		<view class="zai-title">系统登录</view>		
		<view class="zai-form">
			<picker name="Comp" :range="complist" :value="compIndex" range-key="NAME" @change="onPickerChange" >
				<view class="picker zai-input">
					{{complist[compIndex].COMPNO}}-{{complist[compIndex].NAME}}						
				</view>
			</picker>
			<input class="zai-input" v-model="userno" placeholder-class placeholder="请输入账号" />
			<input class="zai-input" :password="true" v-model="password" placeholder-class password placeholder="请输入密码"/>
			<button class="zai-btn" @tap="login">登录</button>

			<view class="otherlogins">
				<view class="cu-item " style="display:flex;flex-direction: column;" @click="wxLogin">
					<image class="img" style="width: 125rpx;height: 125rpx;" src="../../static/wxlogoNew.png" mode="widthFix"></image>
					<text class="text-gray">微信登录</text>
				</view>
			</view>
		</view>

			
			
			

	</view>

</template>

<script>
	var api = require('@/common/api.js')
	export default {
		onLoad(e) {
			console.log("e.token:"+e.token);			
			if (e.token) {
				uni.showLoading()				
				uni.setStorageSync('upload', 1)
				uni.setStorageSync('login', 1)
				uni.setStorageSync('token', e.token)
				uni.showToast({
					duration: 1000,
					title: '登录成功'
				});
				setTimeout((e => {
					uni.hideLoading()
					uni.switchTab({
						url: '../user/user'
						//url: '../index/index'
					});
				}), 1000);
			}
			else
			{
				this.loadPicker();
				if(e.userno)
				{
					this.userno=e.userno;
				}
				var pre_user=uni.getStorageSync('pre_user');
				if(pre_user)
				{
					this.userno= pre_user;
				}
				if(e.password)
				{
					this.password=e.password;
				}
			}

		},
		data() {
			return {
				loading: false,
				pre_compno:"",
				userno: "",
				password: "",
				compIndex:0,
				complist:[],
				compno:""
			};
		},
		methods: {
			loadPicker: function() {
				api.get({
					url: 'User/GetCompList',
					data: {
						device_type: api.DeviceType
					},
					success: data => {
						console.log(data);
						if(data.Status == 'success'){
							this.complist=data.Data;
							this.pre_compno=uni.getStorageSync('pre_compno');
							console.log(this.pre_compno);
							var pre_compindex=this.complist.findIndex(c=>{return c.COMPNO==this.pre_compno});				
							if(pre_compindex>=0)
							{
								this.compIndex=pre_compindex;
							}
						}
					}
				});
			},
			onPickerChange(e) {
				this.compIndex = e.detail.value
			},
			qqlogin() {
				uni.navigateTo({
					url: '../qqlogin/qqlogin',
					success: res => {},
					fail: () => {},
					complete: () => {}
				});
			},
			login() {
				this.loading = true;
				if (this.userno == '') {
					uni.showToast({
						icon: 'none',
						title: '请输入系统账号'
					});
					this.loading = false;
					return;
				}
				// if (this.password == '') {
				// 	uni.showToast({
				// 		icon: 'none',
				// 		title: '请输入密码'
				// 	});
				// 	this.loading = false;
				// 	return;
				// }
				api.post({
					url: 'user/login',
					data: {
						compno:this.complist[this.compIndex].COMPNO,
						userno: this.userno,
						password: this.password,						
						device_type: api.DeviceType
					},
					success: data => {
						console.log(data);
						if (data.code == 1) {
							this.loading = false;
							uni.showToast({
								duration: 500,
								icon: 'success',
								title: data.msg
							});
							//强制页面重载，跳转到首页
							uni.reLaunch({
								url: '../index/index'
								//url: '../grid/grid'
							});
							uni.setStorageSync('upload', 1)
							uni.setStorageSync('login', 1)
							uni.setStorageSync('token', data.token)
							uni.setStorageSync('user', data.user)
							uni.setStorageSync('pre_user', data.user.userno)
							uni.setStorageSync('pre_compno', data.user.compno)
							
							setTimeout((e => {
								uni.navigateBack();
							}), 500);
						}
						else {
							this.loading = false;
							uni.showToast({
								duration: 1500,
								icon: 'none',
								title: data.ErrorMsg
							});
						}
					}
				})
			},
			reg() {
				uni.navigateTo({
					url: '../register/register'
				});
			},
			forget() {
				uni.showToast({
					duration: 1500,
					icon: 'none',
					title: '请联系系统管理员'
				});
			},
			//小程序登录
			wxLogin() {
				if (this.userno == '') {
					uni.showToast({
						icon: 'none',
						title: '请输入系统账号'
					});
					this.loading = false;
					return;
				}
				uni.login({
					provider: "weixin",
					success: loginRes => {
						console.log('login 成功：');
						console.log(loginRes);
						if (loginRes.code) {
							uni.getUserInfo({
								provider: 'weixin',
								withCredentials: true,
								success: res => {
									console.log('getUserInfo 成功：');
									console.log(res);
									var nickName = res.userInfo.nickName; //昵称
									var avatarUrl = res.userInfo.avatarUrl; //头像
									 api.post({
										url: 'user/LoginWx',
										data: {
											compno:this.complist[this.compIndex].COMPNO,
											userno: this.userno,
											code: loginRes.code,						
											device_type: api.DeviceType
										},
										success: data => {
											console.log(data)
											if (data.code == 1) {
												uni.showToast({
													title: '登录成功!',
													icon: 'success',
													duration: 500
												});
												//强制页面重载，跳转到首页
												uni.reLaunch({
													url: '../index/index'
												});
												try {
													data.user.username=nickName;
													data.user.avatar=avatarUrl;
													
													uni.setStorageSync('upload', 1)
													uni.setStorageSync('login', 1)
													uni.setStorageSync('token', data.token)
													uni.setStorageSync('user', data.user)
													uni.setStorageSync('pre_user', data.user.userno)
													uni.setStorageSync('pre_compno', data.user.compno)
													
												} catch (e) {}
												setTimeout(function() {
													uni.navigateBack()
												}, 500)
											}
											else
											{
												uni.showToast({
													duration: 1500,
													icon: 'none',
													title: data.ErrorMsg
												});
											}
										}
									});
								},
								fail: (res) => {
									console.log('getUserInfo 失败：res=');
									console.log(res);
									//获取用户信息失败，前往授权页面
									uni.navigateTo({url: './login_wx'});
								}
							})
						}
						else
						{
							uni.showToast({
								duration: 1500,
								icon: 'none',
								title: '微信登录失败！'
							});
						}
					}
				})
			}
		}
	}
</script>

<style scoped>
	.content{
		width: 100vw;
		height: 100vh;
	}
	.zai-logo{
		width: 100%;
		width: 100%;
		height: 310upx;
	}
	.zai-title{
		display: flexbox;
		font-size: 68upx;
		color: #000000;
		text-align: center;
		width: 100%;
	}
	.avatorWrapper{
		height: 20vh;
		width: 100vw;
		display: flex;
		justify-content: center;
		align-items: flex-end;
	}
	.avator{
		width: 200upx;
		height: 200upx;
		overflow: hidden;
	}
	.avator .img{
		width: 100%
	}
		
	.zai-form{
		padding: 0 100upx;
		margin-top: 20px;
	}

	.zai-input{
		/* background: #e2f5fc; */
		background: #FFFFFF;
		
		margin-top: 30upx;
		border-radius: 80upx;
		padding: 20upx 40upx;
		font-size: 36upx;
		height: 36px;
	}
	.input-placeholder, .zai-input{
		color: #94afce;		
	}
	.zai-label{
		padding: 60upx 0;
		text-align: center;
		font-size: 30upx;
		color: #a7b6d0;
	}
	.zai-btn{
		background: #66ccff;
		margin-top: 30upx;
		color: #fff;
		border: 0;
		border-radius: 100upx;
		font-size: 36upx;
	}
	.zai-btn:after{
		border: 0;
	}
	/*按钮点击效果*/
	.zai-btn.button-hover{
		transform: translate(1upx, 1upx);
	}
	.otherlogins {
		margin-top:100upx ;
		display: flex;
		justify-content: center;
		align-items: flex-end;
		//flex-direction: row;
		//flex-wrap: wrap;
	}
</style>
