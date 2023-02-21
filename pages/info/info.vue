<template>
	<view class="content">
		<view class="view_Form" style="text-align: center;">
			<uni-card>
				<uni-forms ref="form" :rules="rules" :modelValue="UserInfo" label-position="left">
					<uni-forms-item label="学号" name="UserInfo_Account">
						<uni-easyinput type="text" v-model="UserInfo.UserInfo_Account" placeholder="学号不可为空!" />
					</uni-forms-item>
					<uni-forms-item label="姓名" name="UserInfo_Name">
						<uni-easyinput type="text" v-model="UserInfo.UserInfo_Name" placeholder="姓名不可为空!" />
					</uni-forms-item>
					<uni-forms-item label="班级" name="UserInfo_Class">
						<uni-easyinput type="text" v-model="UserInfo.UserInfo_Class" placeholder="无需输入!" />
					</uni-forms-item>
					<uni-forms-item label="手机号" name="UserInfo_TelNumber">
						<uni-easyinput type="text" v-model="UserInfo.UserInfo_TelNumber" placeholder="手机号码不可为空!" />
					</uni-forms-item>
					<uni-forms-item label="身份证" name="UserInfo_IdentityCard">
						<uni-easyinput type="text" v-model="UserInfo.UserInfo_IdentityCard" @blur="getSex()"
							placeholder="身份证号码不可为空!" />
					</uni-forms-item>
					<uni-forms-item label="QQ号码" name="UserInfo_QQNumber">
						<uni-easyinput type="text" v-model="UserInfo.UserInfo_QQNumber" placeholder="QQ号可为空!" />
					</uni-forms-item>
					<uni-forms-item label="微信号" name="UserInfo_WeChatNumber">
						<uni-easyinput type="text" v-model="UserInfo.UserInfo_WeChatNumber" placeholder="微信号可为空!" />
					</uni-forms-item>
					<uni-forms-item label="电子邮箱" name="UserInfo_MailNumber">
						<uni-easyinput type="text" v-model="UserInfo.UserInfo_MailNumber" placeholder="电子邮箱可为空!" />
					</uni-forms-item>
				</uni-forms>
				<button @click="submitForm">提交信息</button>
			</uni-card>
		</view>

		<view class="view_Test" style="text-align: center;">
			<uni-card>
				<text>---其他功能占位---</text>
			</uni-card>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 设置本地信息绑定input
				UserInfo: {
					UserInfo_IdentityCard: "",
					UserInfo_TelNumber: "",
					UserInfo_ID: 0,
					UserInfo_Grade: 0,
					UserInfo_QQNumber: "",
					UserInfo_MailNumber: "",
					UserInfo_WeChatNumber: "",
				},
				rules: {
					// 对email字段进行必填验证
					UserInfo_TelNumber: {
						rules: [{
								// 校验UserInfo_TelNumber不为空
								required: true,
								errorMessage: '请输入正确的手机号码！',
							},
							{
								pattern: "^1(3\\d|4[5-9]|5[0-35-9]|6[567]|7[0-8]|8\\d|9[0-35-9])\\d{8}$",
								errorMessage: '手机号码格式有误！',
							}
						]
					},
					UserInfo_IdentityCard: {
						rules: [{
								// 校验UserInfo_TelNumber不为空
								required: true,
								errorMessage: '请输入正确的身份证号码！',
							},
							{
								pattern: "^[1-9]\\d{5}(18|19|20)\\d{2}((0[1-9])|(1[0-2]))(([0-2][1-9])|10|20|30|31)\\d{3}[0-9Xx]$",
								errorMessage: '身份证号码格式有误！',
							}
						]
					}
				}
			};
		},
		onShow: function() {
			const _that = this
			uni.getStorage({
				key: 'hasLogin',
				success: function(res) {
					if (res.data) {
						// console.log(res.data);
					}
				}
			});

			uni.getStorage({
				key: 'UserInfo',
				success: function(res) {
					// console.log(res);
					_that.UserInfo.UserInfo_Name = res.data.User_Name
					_that.UserInfo.UserInfo_Class = res.data.User_Class
					_that.UserInfo.UserInfo_Account = res.data.User_Account
					// console.log(_that.UserInfo);
				}
			});
		},
		methods: {
			getSex: function() {
				// console.log(this.UserInfo.UserInfo_IdentityCard)


			},

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
					console.log("TOMY");
					uni.switchTab({
						url: "/pages/my/my",
					})
				}
			},

			submitForm: function() {
				const _that = this
				if (this.UserInfo.UserInfo_IdentityCard) {
					let sex = this.UserInfo.UserInfo_IdentityCard.substr(-2, 1)
					if (sex % 2 == 0) {
						this.UserInfo.UserInfo_Sex = "女"
					} else {
						this.UserInfo.UserInfo_Sex = "男"
					}
					// console.log(this.UserInfo.UserInfo_Sex);
					// console.log(this.UserInfo);
				}
				this.$refs.form.validate().then(res => {
					// console.log('表单数据信息：', res);
					// console.log(this.UserInfo);
					console.log(_that.UserInfo);
					uni.request({
						url: "http://127.0.0.1:3007/API_Lying_DB/postUserInfo",
						method: "POST",
						header: {
							'content-type': 'application/x-www-form-urlencoded'
						},
						data: _that.UserInfo,
						timeout: 6000,
						success: (res) => {
							console.log(res.data);
						},
						fail: (err) => {
							console.log(err);
						}
					})

				}).catch(err => {
					console.log('表单错误信息：', err);
				})

				// 完善,POST成功后
				// if (true) {
				// setStorage设置本地数据并跳转MY页
				// this.setStorage(true, this.UserInfo)
				// }
			}
		}
	}
</script>

<style lang="less">

</style>
