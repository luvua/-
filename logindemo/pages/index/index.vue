<template>
	<view class="content">
		<image class="logo" src="/static/logo.png"></image>
		<view class="text-area">
			<text class="title">{{title}}</text>
		</view>

		<view class='content'>
			<view>申请获取以下权限</view>
			<text>获得你的公开信息(昵称，头像等)</text>
		</view>

		<button class="" open-type="getUserInfo" @click="wxLogin">微信登录</button>
	</view>
</template>

<script>
	var _self;
	export default {

		methods: {
			wxLogin: function(options) {
				_self = this;
				// #ifdef APP-PLUS
				uni.login({
					success: (res) => {
						console.log(JSON.stringify(res))
						// 第三方登陆
						// res 对象格式
						//{"code":"***",
						//"authResult":{
						//"openid":"***",
						//"scope":"snsapi_userinfo",
						//"refresh_token":"**",
						//"code":"****",
						//"unionid":"***",
						//"access_token":"***",
						//"expires_in":7200
						//},
						//"errMsg":"login:ok"}

						uni.getUserInfo({
							success: (info) => {
								console.log(JSON.stringify(info))
								// info 对象格式
								// {"errMsg":"getUserInfo:ok",
								// "rawData":"...
								// "userInfo":{
								//"openId":"***",
								//"nickName":"***",
								//"gender":1,
								// "city":"Xi'an",
								// "province":"Shaanxi",
								// "country":"China",
								// "avatarUrl":"头像",
								// "unionId":"oU5Yyt_agt547zWyA0v0eLfFPqxo"
								//},"signature":""}
								// 与服务器交互将数据提交到服务端数据库
								uni.request({
									url: _self.apiServer + 'member&m=login',
									method: 'POST',
									header: {
										'content-type': "application/x-www-form-urlencoded"
									},
									data: {
										openid: info.userInfo.openId,
										name: info.userInfo.nickName,
										face: info.userInfo.avatarUrl,
									},
									success: res => {
										console.log(JSON.stringify(res))
										res = res.data;
										if (res.status == 'ok') {
											uni.showToast({
												title: '登陆成功',
												mask: false,
												duration: 1500
											});


											// 判断跳转方式
											if (options.backtype == '1') {
												uni.redirectTo({
													url: options.backpage
												});
											} else {
												uni.switchTab({
													url: options.backpage
												});
											}
										} else {

											uni.showToast({
												title: res.data
											});
										}
									},
									fail: () => {
										console.log('登陆失败')
									},
									complete: () => {}
								});

							},
							fail: () => {
								uni.showToast({
									title: "微信登录授权失败"
								});
							}
						})
					},
					fail: () => {
						uni.showToast({
							title: "微信登录授权失败"
						});
					}
				})
				// #endif
				console.log(options)
			}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200upx;
		width: 200upx;
		margin-top: 200upx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50upx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36upx;
		color: #8f8f94;
	}
</style>
