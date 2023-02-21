<template>
	<view class="content" @touchstart="start" @touchend="end">
		<view class="view_Career">
			<uni-card class="cardOne" title="学习等级-待删除">
				{{gradeList[User_Grade].gradeName}}
				{{gradeList[User_Grade].gradeNumber}}
			</uni-card>

			<uni-card class="cardTwo" title="">
				<view class="cardTwo_ZXT" v-for="item in daysList">
					<text v-if="item.count===3">⚪</text>
					<text v-else="">·</text>
				</view><br>
				<view class="cardTwo_ZXT" v-for="item in daysList">
					<text v-if="item.count===2">⚪</text>
					<text v-else="">·</text>
				</view> <br>
				<view class="cardTwo_ZXT" style="" v-for="item in daysList">
					<text v-if="item.count===1">⚪</text>
					<text v-else="">·</text>
				</view> <br>
				<view class="cardTwo_ZXT" v-for="item in daysList">
					<text v-if="item.count===0">⚪</text>
					<text v-else="">·</text>
				</view> <br>
				<view class="cardTwo_ZXT" v-for="item in daysList">
					<text>{{item.type}}</text>
				</view>
			</uni-card>
		</view>

		<view class="view_History">
			<uni-card>
				<uni-collapse>
					<uni-collapse-item :open="true" title="今日自习预约">
						<!-- 无预约数据时显示 -->
						<!-- <uni-list-item v-if="!test()[1]" title="无预约" note="书山有路勤为径,学海无涯苦作舟!" rightText=""> -->
						<uni-list-item v-if="true" title="无预约" note="书山有路勤为径,学海无涯苦作舟!" rightText="">
						</uni-list-item>
						<!-- 有预约数据时显示 -->
						<!-- <uni-list-item v-if="item.UserRoom_Date==test()[0]" v-for="(item,index) in UserRoomList" -->
						<uni-list-item v-if="false" v-for="(item,index) in UserRoomList" :title="item.UserRoom_Room"
							:note="item.UserRoom_Room" :rightText="item.UserRoom_Date+'-'+item.UserRoom_Time">
						</uni-list-item>
					</uni-collapse-item>
				</uni-collapse>
			</uni-card>

			<uni-card>
				<uni-collapse>
					<uni-collapse-item :open="false" title="历史学习记录">
						<!-- <uni-list-item v-if="item.UserRoom_Date<test()[0]" v-for="(item,index) in UserRoomList" -->
						<uni-list-item v-if="true" v-for="(item,index) in UserRoomList" :title="item.UserRoom_Room"
							:note="item.UserRoom_Date" :rightText="item.UserRoom_Time">
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
			<uni-drawer class="Mask" ref="showLeft" mode="left" :width="320" @change="change($event,'showLeft')">
				<!-- 遮罩布局代码 -->
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
					<uni-card title="" thumbnail="" extra="" note="">
						<uni-button v-show="hasLogin" @click="goQuit()">退出</uni-button>
						<uni-button v-show="!hasLogin" @click="goLogin()">点击登录</uni-button>
					</uni-card>

				</view>

				<view class="view_Test">

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
				scrollTop: 0,
				startData: {
					clientX: 0,
					clientY: 0
				},

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

				daysList: [],

				UserRoomList: [],

				hasLogin: false,
				UserInfo: {
					UserInfo_Name: "",
					UserInfo_Account: "",
					UserInfo_Class: "",
					UserInfo_TelNumber: ""
				},

				msgType: 'wran',
				messageText: '这是一条成功提示',

				showLeft: false



			};
		},
		onPageScroll(res) {
			this.scrollTop = res.scrollTop; //距离页面顶部距离
		},
		onShow: function() {

		},
		onLoad: function() {

		},
		methods: {
			start: function(e) {
				this.startData.clientX = e.changedTouches[0].clientX
				this.startData.clientY = e.changedTouches[0].clientY
				// console.log(this.startData);
			},

			end: function(e) {
				const subX = e.changedTouches[0].clientX - this.startData.clientX
				const subY = e.changedTouches[0].clientY - this.startData.clientY
				if (subX < -150) {
					if (Math.abs(subY) < 100) {
						console.log("左滑执行");
						this.closeDrawer('showLeft')
					}
				} else if (subX > 150) {
					if (Math.abs(subY) < 100) {
						console.log("右滑执行")
						this.showDrawer('showLeft')
					}
				} else {
					if (subY < -200) {
						console.log("上滑执行");
					} else if (subY > 200) {
						if (this.scrollTop === 0 && this.showLeft === false) {
							console.log("下滑执行")
							// this.showQR()
						}
					}
				}

			},

			change(e, type) {
				console.log((type === 'showLeft' ? '左窗口' : '右窗口') + (e ? '打开' : '关闭'));
				this[type] = e
			},
			showDrawer(e) {
				this.$refs[e].open()
			},
			closeDrawer(e) {
				this.$refs[e].close()
			},





		},



	}
</script>

<style lang="less">

</style>
