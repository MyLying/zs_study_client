<template>
	<view class="content" @touchstart="start" @touchend="end">
		<view class="view_Career">
			<uni-card class="cardOne" title="" style="height: 320rpx;">
				{{UserInfo.UserInfo_Name}}
				{{gradeList[User_Grade].gradeName}}
				<!-- {{gradeList[User_Grade].gradeNumber}} -->
			</uni-card>

			<uni-card class="cardTwo" title="">
				<view class="cardTwo_ZXT" v-for="item in dayList">
					<text v-if="item.count===3">⚪</text>
					<text v-else="">·</text>
				</view><br>
				<view class="cardTwo_ZXT" v-for="item in dayList">
					<text v-if="item.count===2">⚪</text>
					<text v-else="">·</text>
				</view> <br>
				<view class="cardTwo_ZXT" style="" v-for="item in dayList">
					<text v-if="item.count===1">⚪</text>
					<text v-else="">·</text>
				</view> <br>
				<view class="cardTwo_ZXT" v-for="item in dayList">
					<text v-if="item.count===0">⚪</text>
					<text v-else="">·</text>
				</view> <br>
				<view class="cardTwo_ZXT" v-for="item in dayList">
					<text>{{item.type}}</text>
				</view>
			</uni-card>
		</view>

		<view class="view_History">
			<uni-card>
				<uni-collapse>
					<uni-collapse-item :open="true" title="今日自习预约">
						<!-- 无预约数据时显示 -->
						<uni-list-item v-if="!test()[1]" title="无预约" note="书山有路勤为径,学海无涯苦作舟!" rightText="">
						</uni-list-item>
						<!-- 有预约数据时显示 -->
						<uni-list-item v-if="item.UserRoom_Date==test()[0]" v-for="(item,index) in UserRoomList"
							:title="item.UserRoom_Room" :note="item.Room_Adress"
							:rightText="item.UserRoom_Date+'-'+item.UserRoom_Time" @click="jump(item,'TRDetail')" link>
						</uni-list-item>
					</uni-collapse-item>
				</uni-collapse>
			</uni-card>

			<uni-card>
				<uni-collapse>
					<uni-collapse-item :open="false" title="历史学习记录">
						<uni-list-item v-if="item.UserRoom_Date<test()[0]" v-for="(item,index) in UserRoomList"
							:title="item.UserRoom_Room" :note="item.UserRoom_Date" :rightText="item.UserRoom_Time">
						</uni-list-item>
					</uni-collapse-item>
				</uni-collapse>
			</uni-card>
		</view>


		<view class="view_Test">
			<uni-card>
				<text>---其他功能占位---</text>
			</uni-card>
		</view>


		<view class="view_Mask">
			<uni-drawer class="Mask" ref="showLeft" mode="left" :width="340" @change="change($event,'showLeft')">
				<!-- 遮罩布局代码 -->

				<view class="view_InfoList">
					<uni-card title="" thumbnail="" extra="" note="">
						<uni-list class="infolist">
							<img class="infolistimg" src="@/static/logo.png"></img>
							<uni-list-item :title="UserInfo.UserInfo_Name"></uni-list-item>
							<uni-list-item :title="UserInfo.UserInfo_Account"></uni-list-item>
							<uni-list-item :title="UserInfo.UserInfo_TelNumber"></uni-list-item>
							<uni-list-item :title="UserInfo.UserInfo_Class"></uni-list-item>
						</uni-list>
					</uni-card>
				</view>

				<view class="view_Btn">
					<uni-card title="" thumbnail="" extra="" note="">
						<uni-button v-show="hasLogin" @click="goQuit()">退出</uni-button>
						<uni-button v-show="!hasLogin" @click="goLogin()">点击登录</uni-button>
					</uni-card>

				</view>

				<view class="view_Test">
					<uni-card>
						<text>---其他功能占位---</text>
					</uni-card>
				</view>

			</uni-drawer>
		</view>

		<view class="view_Message">
			<!-- 提示信息弹窗 -->
			<uni-popup ref="message" type="message">
				<uni-popup-message :type="msgType" :message="messageText" :duration="2000"></uni-popup-message>
			</uni-popup>
			<uni-popup ref="showQR" type="center">
				<view class="showQR">
					进出门禁二维码识别
				</view>
			</uni-popup>
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
				},

				showLeft: false,

				msgType: 'wran',
				messageText: '这是一条成功提示',

				gradeList: [{
					gradeName: "Lv.小白",
					gradeNumber: 100
				}, {
					gradeName: "Lv.伴读",
					gradeNumber: 200
				}, {
					gradeName: "Lv.童生",
					gradeNumber: 400
				}, {
					gradeName: "Lv.秀才",
					gradeNumber: 800
				}, {
					gradeName: "Lv.举人",
					gradeNumber: 1600
				}, {
					gradeName: "Lv.进士",
					gradeNumber: 3200
				}, {
					gradeName: "Lv.状元",
					gradeNumber: 99999999
				}],

				User_Grade: 0,

				UserRoomList: [],

				dayList: [],

				startData: {
					clientX: 0,
					clientY: 0
				},

				scrollTop: 0
			};
		},

		onPageScroll(res) {
			this.scrollTop = res.scrollTop; //距离页面顶部距离
		},
		onLoad: function() {
			this.getStorage()
		},
		onShow: function() {
			// console.log(this.dayList);
			this.getStorage()
			console.log("MAIN-SHOW");
			const _that = this
			uni.getStorage({
				key: 'dayList',
				success: function(res) {
					if (res.data.length > 0) {
						_that.dayList = res.data
					} else {
						_that.dayList = []
					}
				}
			})
			// 获取status状态,判断是否已经登录
	uni.getStorage({
		key: 'hasLogin',
		success: function(res) {
			if (res.data) {
				uni.getStorage({
					key: 'UserInfo',
					success: function(res) {
						// if (_that.UserRoomList.length) {
						// 	if (_that.UserRoomList[0].UserRoom_Account === res.data
						// 		.UserInfo_Account) {
						// 		console.log("本地已存储UserRoomList数据！");
						// 	} else {
						// 		console.log("账号不一致，发起UserRoomList数据请求！");
						// 		_that.getUserRoom(res.data.UserInfo_Account)
						// 	}
						// } else {
						console.log("本地无数据，发起UserRoomList数据请求！");
						_that.getUserRoom(res.data.UserInfo_Account)
						// }
					}
				});
			} else {
				console.log("您未登陆或未完善个人信息，无法获取预约信息，请登录或前往完善！");
				_that.UserRoomList = []
				_that.messageToggle('error', "您未登陆，无法获取预约信息，请登录！")
			}
		}
	});
		},
		methods: {
			jump: function(data, url) {
				const string = JSON.stringify(data)
				uni.navigateTo({
					url: '/pages/' + url + '/' + url + '?data=' + string
				})
				console.log(data, url);
			},

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
				uni.request({
					url: "http://127.0.0.1:3007/API_Lying_DB/quit",
					method: "POST",
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						'User_Account': this.UserInfo.UserInfo_Account
					},
					timeout: 6000,
					success: (res) => {
						console.log(res);
					},
					fail: (err) => {
						console.log(err);
					}
				})

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
					key: 'dayList',
					data: [],
					success: function() {
						console.log('empty-dayList-YES');
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


			showQR: function() {
				this.$refs.showQR.open()
			},

			showDrawer(e) {
				this.$refs[e].open()
			},
			// 关闭窗口
			closeDrawer(e) {
				this.$refs[e].close()
			},
			// 抽屉状态发生变化触发
			change(e, type) {
				console.log((type === 'showLeft' ? '左窗口' : '右窗口') + (e ? '打开' : '关闭'));
				this[type] = e
			},


			start: function(e) {
				this.startData.clientX = e.changedTouches[0].clientX
				this.startData.clientY = e.changedTouches[0].clientY
				// console.log(this.startData);
			},

			end: function(e) {
				const subX = e.changedTouches[0].clientX - this.startData.clientX
				const subY = e.changedTouches[0].clientY - this.startData.clientY
				if (subX < -130 && Math.abs(subY) < 130) {
					console.log("左滑执行");
					this.closeDrawer('showLeft')
				} else if (subX > 130 && Math.abs(subY) < 130) {
					console.log("右滑执行")
					this.showDrawer('showLeft')
				}

				if (subY < -130 && Math.abs(subX) < 130) {
					console.log("上滑执行");
				} else if (subY > 130) {
					if (subY > 500 && Math.abs(subX) < 130 && this.scrollTop === 0 && this.showLeft === false) {
						console.log("下滑执行")
						this.showQR()
					} else {
						this.messageToggle("warn", "使劲下拉开门!")
					}

				}
				// if () {} else if () {} else if () {} else if () {} else {}
			},

			test: function() {
				let nowdate = new Date();
				let string
				if (nowdate.getMonth() < 9) {
					string = nowdate.getFullYear() + "-0" + (nowdate.getMonth() + 1) + "-" + nowdate.getDate()
				} else {
					string = nowdate.getFullYear() + "-" + (nowdate.getMonth() + 1) + "-" + nowdate.getDate()
				}

				let count = 0
				for (let i in this.UserRoomList) {
					if (this.UserRoomList[i].UserRoom_Date == string) {
						count++
					}
				}
				return [string, count]
			},

			messageToggle(type, string) {
				this.msgType = type
				// this.messageText = `您未登陆，无法获取预约信息，请登录！`
				this.messageText = string
				this.$refs.message.open()
			},

getUserRoom: function(e) {
	const _that = this
	uni.request({
		url: "http://127.0.0.1:3007/API_Lying_DB/getUserRoom",
		method: "POST",
		data: 'UserRoom_Account=' + e,
		header: {
			'content-type': 'application/x-www-form-urlencoded'
		},
		timeout: 6000,
		success: (res) => {
			const status = res.data.status
			if (status === 200) {
				// console.log("服务器返回", res.data.data);
				_that.UserRoomList = res.data.data
				uni.setStorage({
					key: 'UserRoom',
					data: _that.UserRoomList,
					success: function() {
						console.log('setUserRoom-YES');
					}
				});
				// console.log(_that.UserRoomList);
				console.log('setUserRoomList-YES');
				_that.xxqs(res.data.data)
				// console.log("本地的数据", _that.UserRoomList);
			}
		},
		fail: (err) => {
			console.log(err, "err");
		}
	})
},

xxqs: function(list) {
	//有BUG
	const _that = this
	let timenow = new Date().getTime(),
		timeold,
		day
	let dayList = [{
		type: "",
		count: 0
	}, {
		type: "",
		count: 0
	}, {
		type: "",
		count: 0
	}, {
		type: "",
		count: 0
	}, {
		type: "",
		count: 0
	}, {
		type: "",
		count: 0
	}, {
		type: "",
		count: 0
	}]
	for (let item in list) {
		timeold = new Date(list[item].UserRoom_Date);
		day = (Math.abs(timenow - timeold.getTime()) / (1000 * 24 * 60 * 60)).toFixed(0)
		// console.log(timeold.getDate());
		// console.log(dayList[day].type);
		// dayList[day].type = timeold.getDate()
		if (day < 7) {
			// console.log(dayList);
			// console.log(day);
			dayList[day].count++
		}

	}
	for (let item in dayList) {
		dayList[item].type = Number(new Date().getDate()) - Number(item)
		// console.log(dayList[item].type,dayList[item].count);
	}
	// this.dayList = dayList.reverse();
	// this.dayList = dayList;
	uni.setStorage({
		key: 'dayList',
		data: dayList,
		success: function() {
			_that.dayList = dayList
			console.log('setdayList-YES');
		}
	});
	// console.log(this.dayList);
}
		},
	}
</script>

<style lang="less">
	.content {
		font-size: 36rpx;

		.view_Career {
			.cardOne {}

			.cardTwo {

				.cardTwo_ZXT {
					position: relative;
					width: 14.28%;
					float: right;
					text-align: center;
					// background-color: aqua;
				}
			}
		}

		.view_History {}

		.view_Test {}

		.view_Mask {

			// background-color: white;
			.Mask {
				background-color: white;





				.view_InfoList {



					.infolist {
						text-align: center;
						font-size: 36rpx;

						.infolistimg {
							position: relative;
							width: 40%;
							margin: 100rpx auto;
							object-fit: cover;
							// height: 100%;
						}
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
		}

		.view_Message {
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
