<template>
	<view>
		<view class="view_Test">
			<uni-card :title="'今日：'+jt">
				<uni-list>
					<uni-list-item v-for="(item,index) in ForList[0]" :note="item.Number+'-'+RIList.Room_AllNumber"
						:title="item.Time" rightText="预约" link @click="booking(jt,item.Time)">
					</uni-list-item>
				</uni-list>
			</uni-card>
			<uni-card :title="'明日：'+mt">
				<uni-list>
					<uni-list-item v-for="(item,index) in ForList[1]" :note="item.Number+'-'+RIList.Room_AllNumber"
						:title="item.Time" rightText="预约" link @click="booking(mt,item.Time)">
					</uni-list-item>
				</uni-list>
			</uni-card>
		</view>

		<view class="view_Test">
			<uni-card>
				<text>---其他功能占位---</text>
			</uni-card>
		</view>

		<view class="view_Message">
			<!-- 提示信息弹窗 -->
			<uni-popup ref="message" type="message">
				<uni-popup-message :type="msgType" :message="messageText" :duration="2000"></uni-popup-message>
			</uni-popup>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				msgType: 'wran',
				messageText: '这是一条成功提示',
				RIList: {},
				jt: "",
				mt: "",
				ForList: [
					[{
						Time: "上午",
						Number: 0
					}, {
						Time: "下午",
						Number: 0
					}, {
						Time: "晚上",
						Number: 0
					}],
					[{
						Time: "上午",
						Number: 0
					}, {
						Time: "下午",
						Number: 0
					}, {
						Time: "晚上",
						Number: 0
					}]
				],
				UserInfo: {}

			};
		},
		onLoad: function(options) {
			this.RIList = JSON.parse(options.data)
			this.getNowDate()
			this.getUserRoom(this.RIList.Room_Room)
			this.getUserInfo()
		},
		methods: {
			messageToggle(type, string) {
				this.msgType = type
				// this.messageText = `您未登陆，无法获取预约信息，请登录！`
				this.messageText = string
				this.$refs.message.open()
			},

			getUserInfo: function() {
				const _that = this
				uni.getStorage({
					key: 'UserInfo',
					success: function(res) {
						// console.log(res.data);
						_that.UserInfo = res.data
						// console.log(_that.UserInfo);
					}
				});
			},

			getNowDate: function() {
				const nowTime = new Date()
				if ((nowTime.getMonth() + 1) < 10) {
					this.jt = nowTime.getFullYear().toString() + "-0" + (nowTime.getMonth() + 1)
						.toString() + "-" + nowTime.getDate().toString()
					this.mt = nowTime.getFullYear().toString() + "-0" + (nowTime.getMonth() + 1)
						.toString() + "-" + (nowTime.getDate() + 1).toString()
				} else {
					this.jt = nowTime.getFullYear().toString() + "-" + (nowTime.getMonth() + 1)
						.toString() + "-" + nowTime.getDate().toString()
					this.mt = nowTime.getFullYear().toString() + "-0" + (nowTime.getMonth() + 1)
						.toString() + "-" + (nowTime.getDate() + 1).toString()
				}
			},

booking: function(day, time) {
	const _that = this
	const obj = {
		"UserRoom_Account": this.UserInfo.UserInfo_Account,
		"UserRoom_Room": this.RIList.Room_Room,
		"UserRoom_Date": day,
		"UserRoom_Time": time
	}

	uni.request({
		url: "http://127.0.0.1:3007/API_Lying_DB/booking",
		method: "POST",
		data: obj,
		header: {
			'content-type': 'application/x-www-form-urlencoded'
		},
		timeout: 6000,
		success: (res) => {
			console.log("res:", res.data);
			switch (res.data.status) {
				case 401:
					console.log("401")
					_that.messageToggle("error",
						"查询失败！")
					break;
				case 402:
					console.log("402")
					_that.messageToggle("error",
						"查询失败！")
					break;
				case 101:
					console.log("101")
					_that.messageToggle("warn",
						"预约失败，已经预约过了，您分身乏术喔！")
					break;
				case 102:
					console.log("102")
					_that.messageToggle("wron",
						"预约失败!")
					break;
				case 202:
					console.log("202")
					uni.request({
						url: "http://127.0.0.1:3007/API_Lying_DB/getUserRoom",
						method: "POST",
						data: 'UserRoom_Account=' + _that.UserInfo.UserInfo_Account,
						header: {
							'content-type': 'application/x-www-form-urlencoded'
						},
						timeout: 6000,
						success: (res) => {
							if (res.data.status === 200) {
								uni.setStorage({
									key: 'UserRoom',
									data: res.data.data,
									success: function() {
										console.log('setUserRoom-YES');
										_that.getUserRoom(_that.RIList
											.Room_Room)
										_that.messageToggle("success",
											"预约成功!")
									}
								});
							}
						},
					})

					break;
				default:
					console.log("默认")
			}


		},
		fail: (err) => {
			console.log("err", err);
		}
	})
},

			getUserRoom: function(e) {
				const _that = this
				for (let i = 0; i < 2; i++) {
					for (let j = 0; j < 3; j++) {
						_that.ForList[i][j].Number = 0
					}
				}
				uni.request({
					url: "http://127.0.0.1:3007/API_Lying_DB/getRIDetail",
					method: "POST",
					data: 'UserRoom_Room=' + e,
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					timeout: 6000,
					success: (res) => {
						// console.log("res:", res.data.data);
						let list = res.data.data
						for (let i in list) {
							if (list[i].UserRoom_Date == _that.jt) {
								if (list[i].UserRoom_Time ==
									"上午") {
									_that.ForList[0][0].Number++
								} else if (list[i].UserRoom_Time ==
									"下午") {
									_that.ForList[0][1].Number++
								} else if (list[i].UserRoom_Time ==
									"晚上") {
									_that.ForList[0][2].Number++

								}
							} else if (list[i].UserRoom_Date == _that.mt) {
								if (list[i].UserRoom_Time ==
									"上午") {
									_that.ForList[1][0].Number++
								} else if (list[i].UserRoom_Time ==
									"下午") {
									_that.ForList[1][1].Number++
								} else if (list[i].UserRoom_Time ==
									"晚上") {
									_that.ForList[1][2].Number++
								}
							}

						}

						// console.log(_that.ForList);
					},
					fail: (err) => {
						console.log("err", err);
					}
				})
			},
		}
	}
</script>

<style lang="less">

</style>
