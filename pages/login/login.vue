<template>
	<view class="content">
		<uni-card>
			<uni-easyinput type="" v-model="User_Account" placeholder="请输入学号"></uni-easyinput>
			<uni-easyinput type="password" v-model="User_Password" placeholder="请输入密码"></uni-easyinput>
			<button @click="submitForm">登录</button>
		</uni-card>
		<uni-popup ref="popup" type="message">
			<uni-popup-message type="success" message="成功消息" :duration="2000"></uni-popup-message>
		</uni-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				User_Account: "",
				User_Password: "",
			}
		},
		methods: {
			setStorage: function(bool, obj) {
				uni.setStorage({
					key: 'hasLogin',
					data: bool,
					success: function() {
						console.log('sethasLogin-YES');
					}
				});

				uni.setStorage({
					key: 'UserInfo',
					data: obj,
					success: function() {
						console.log('setUserInfo-YES');
					}
				});


				if (bool) {
					console.log("TOMAIN");
					uni.switchTab({
						url: "/pages/main/main",
					})
				} else {
					console.log("TOINFO");
					uni.redirectTo({
						// 前往信息页面
						url: "/pages/info/info",
					})
				}
			},

submitForm: function() {
	let param = 'User_Account=' + this.User_Account + '&User_Password=' + this.User_Password + ''
	uni.request({
		url: "http://127.0.0.1:3007/API_Lying_DB/login",
		method: "POST",
		header: {
			'content-type': 'application/x-www-form-urlencoded'
		},
		data: param,
		timeout: 6000,
		success: (res) => {
			// console.log(res);
			console.log(res.data);
			const status = res.data.status
			if (status === 200) {
				this.setStorage(true, res.data.data[0])
			} else if (status === 103) {
				// console.log(res.data);
				this.setStorage(false, res.data.data[0])
			}
		},
		fail: (err) => {
			console.log(err);
		}
	})
},

		}
	}
</script>

<style>
	.content {
		font-size: 36rpx;
	}
</style>
