<template>
	<view class="content">
		<view class="view_SwiperDot">
			<uni-swiper-dot :info="info" :current="current" field="" mode="default">
				<swiper class="swiper-box" @change="change">
					<swiper-item v-for="(item ,index) in info" :key="index"
						style="background-image: url('https://qzonestyle.gtimg.cn/qzone/qzactStatics/imgs/20171122191603_896cd9.jpg');background-position: bottom center; background-size: cover;">
						<view class="swiper-item">
							{{item.content}}
						</view>
					</swiper-item>
				</swiper>
			</uni-swiper-dot>
		</view>

		<view class="view_NoticeBar">
			<uni-notice-bar showIcon="true" scrollable="true" single="true"
				text="为了给同学们提供良好的的环境及氛围,请勿在自习室内细语,离开时请随身带走座位上的垃圾!!!">
			</uni-notice-bar>
		</view>

		<view class="view_Card">
			<uni-card class="card" title="" extra="">
				<uni-collapse>
					<uni-collapse-item :open="false" title="失物招领">
						<uni-list border-full>
							<uni-list-item v-for="(item,index) in LFList" :title="item.LostFound_Content"
								:note="item.LostFound_Date" showArrow
								thumb="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/460d46d0-4fcc-11eb-8ff1-d5dcf8779628.png"
								thumb-size="lg" rightText="详情" link @click="jump(item,'LFDetail')" />
						</uni-list>
					</uni-collapse-item>
				</uni-collapse>
			</uni-card>

			<uni-card class="card" title="" extra="">
				<uni-collapse>
					<uni-collapse-item :open="false" title="学院通知">
						<uni-list>
							<uni-list-item v-for="(item,index) in NList" :title="item.Notice_Title"
								:note="item.Notice_Content" showArrow rightText="详情" link
								@click="jump(item,'NDetail')" />
						</uni-list>

					</uni-collapse-item>
				</uni-collapse>
			</uni-card>

			<uni-card class="card" title="" extra="">
				<uni-collapse>
					<uni-collapse-item :open="false" title="值班信息">
						<uni-list>
							<uni-list-item v-for="(item,index) in DIList"
								:title="item.RoomDuty_Room+'···'+item.RoomDuty_Date" :note="item.RoomDuty_College"
								showArrow :rightText="item.RoomDuty_Name" link @click="jump(item,'DIDetail')" />
						</uni-list>
					</uni-collapse-item>
				</uni-collapse>
			</uni-card>

			<uni-card class="card" title="" extra="">
				<uni-collapse>
					<uni-collapse-item :open="false" title="自习室">
						<uni-list>
							<uni-list-item v-for="(item,index) in RIList" :title="item.Room_Room"
								:note="item.Room_Adress" rightText="item.Room_AllNumber" showArrow link
								@click="jump(item,'RIDetail')" />
						</uni-list>
					</uni-collapse-item>
				</uni-collapse>
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
				scrollTop: 0,

				info: [{
					url: 'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/094a9dc0-50c0-11eb-b680-7980c8a877b8.jpg',
					content: '内容 A'
				}, {
					url: "https://img-cdn-aliyun.dcloud.net.cn/dev/img/ext/logo-d.png",
					content: '内容B'
				}, {
					url: "https://qzonestyle.gtimg.cn/qzone/qzactStatics/imgs/20171122191603_896cd9.jpg",
					content: '内容C'
				}, {
					url: "https://qzonestyle.gtimg.cn/qzone/qzactStatics/imgs/20171122191603_896cd9.jpg",
					content: '内容D'
				}, ],

				current: 0,


				// 失物招领
				LFList: [],
				// 通知信息
				NList: [],
				DIList: [],
				// 值班信息
				RIList: []
				// 自习室信息



			}
		},
		onLoad: function() {
			this.getRoom()
		},
		onShow: function() {
			if (this.LFList[0] && this.NList && this.DIList && this.RIList) {
				console.log("本地已存储MAIN数据！");
			} else {
				this.getRoom()
			}
		},
		methods: {
			messageToggle(type, string) {
				this.msgType = type
				// this.messageText = `您未登陆，无法获取预约信息，请登录！`
				this.messageText = string
				this.$refs.message.open()
			},
			jump: function(data, url) {
				const _that = this
				uni.getStorage({
					key: 'hasLogin',
					success: function(res) {
						console.log(res.data);
						if (res.data) {
							const string = JSON.stringify(data)
							uni.navigateTo({
								url: '/pages/' + url + '/' + url + '?data=' + string
							})
							// console.log(data, url);
						} else {
							_that.messageToggle("error", "您未登录，无法查看!")
						}
					}
				});
			},

			change: function(e) {
				this.current = e.detail.current;
			},





getRoom: function() {
	const _that = this
	uni.request({
		url: "http://127.0.0.1:3007/API_Lying_DB/getMain",
		method: "GET",
		timeout: 6000,
		success: (res) => {
			// console.log("res:", res.data.data[0]);
			_that.LFList = res.data.data[0]
			_that.NList = res.data.data[1]
			_that.DIList = res.data.data[2]
			_that.RIList = res.data.data[3]
			// console.log("LFList:", _that.LFList);
			// console.log("NList:", _that.NList);
			// console.log("DIList:", _that.DIList);
			// console.log("RIList:", _that.RIList);
		},
		fail: (err) => {
			console.log("err:", err);
		}
	})
},

		}
	}
</script>

<style lang="less">
	.content {
		font-size: 36rpx;

		.view_SwiperDot {}

		.view_NoticeBar {}

		.view_Card {
			.card {
				// height: 320rpx;
			}
		}

		.view_Test {
			text-align: center;

		}
	}
</style>
