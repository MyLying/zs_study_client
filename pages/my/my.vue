<template>
	<view class="content">

		<view class="view_AvatarImg">
			<image class="avatarimg" mode="aspectFill" src="@/static/avatar/circle1.jpg"></image>
		</view>

		<view class="view_InfoList">
			<uni-card title="" thumbnail="" extra="" note="">
				<uni-list class="infolist">
					<uni-list-item :title="UserInfo.UserInfo_Name"></uni-list-item>
					<uni-list-item :title="UserInfo.UserInfo_Account"></uni-list-item>
					<uni-list-item :title="UserInfo.UserInfo_TelNumber"></uni-list-item>
					<uni-list-item :title="UserInfo.UserInfo_Class"></uni-list-item>
				</uni-list>
			</uni-card>
		</view>

		<view class="view_Btn">
			<uni-card v-show="hasLogin" title="" thumbnail="" extra="" note="">
				<uni-button @click="showQR()">门禁</uni-button>
			</uni-card>

			<uni-card title="" thumbnail="" extra="" note="">
				<uni-button v-show="hasLogin" @click="goQuit()">退出</uni-button>
				<uni-button v-show="!hasLogin" @click="goLogin()">点击登录</uni-button>
			</uni-card>

		</view>

		<view class="view_Test">

		</view>


	</view>
</template>

<script>
	export default {
		data() {
			return {
				hasLogin: false,
				UserInfo: {
					UserInfo_Name: "",
					UserInfo_Account: "",
					UserInfo_Class: "",
					UserInfo_TelNumber: ""
				}
			};
		},
		onShow: function() {
			this.getStorage()
		},
		onLoad: function(options) {
			this.getStorage()
			const UserRoomList = JSON.parse(options.data)
			console.log(UserRoomList);
		},
		methods: {

			getStorage: function() {
				let _that = this
				uni.getStorage({
					key: 'hasLogin',
					success: function(res) {
						if (res.data) {
							_that.hasLogin = res.data
						}
					}
				});

				uni.getStorage({
					key: 'UserInfo',
					success: function(res) {
						_that.UserInfo = res.data
					}
				});
			},

			goLogin: function() {
				uni.navigateTo({
					url: '/pages/login/login'
				});
			},

			goQuit: function() {
				this.hasLogin = false
				this.UserInfo = {}
				uni.setStorage({
					key: 'hasLogin',
					data: false,
					success: function() {
						console.log('empty-hasLogin-YES');
					}
				});
				uni.setStorage({
					key: 'daysList',
					data: [],
					success: function() {
						console.log('empty-daysList-YES');
					}
				});
				uni.setStorage({
					key: 'UserInfo',
					data: {
						UserInfo_Account: "",
						UserInfo_Class: "",
						UserInfo_ID: 0,
						UserInfo_IdentityCard: "",
						UserInfo_MailNumber: "",
						UserInfo_Name: "",
						UserInfo_QQNumber: "",
						UserInfo_Sex: "",
						UserInfo_TelNumber: "",
						UserInfo_WechatNumber: ""
					},
					success: function() {
						console.log('empty-UserInfo-YES');
					}
				});
			},

		}
	}
</script>

<style lang="less">
	.content {
		font-size: 36rpx;

		.view_AvatarImg {
			height: 160px;

			.avatarimg {
				width: 40%;
				height: 100%;
				margin-left: 30%;
			}


		}

		.view_InfoList {

			.infolist {
				text-align: center;
				font-size: 36rpx;
			}
		}

		.view_Btn {}

		.view_Test {
			text-align: center; //待删除

			.showQR {
				width: 400rpx;
				height: 400rpx;
				background-color: aqua;
				text-align: center; //待删除
				line-height: 400rpx; //待删除
			}
		}
	}
</style>
