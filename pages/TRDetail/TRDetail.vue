<template>
	<view class="content">

		<view class="view_Test">
			<uni-card>
				{{TRDetailList}}
				


			</uni-card>

			<uni-card>
				<button @click="cancel()">取消预约</button>
			</uni-card>
		</view>

		<view class="view_Test">
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
				TRDetailList: {}
			};
		},
		onLoad: function(options) {
			this.TRDetailList = JSON.parse(options.data)
			console.log(this.TRDetailList);
		},
		methods: {


			cancel: function() {
				const obj = {
					"UserRoom_Account": this.TRDetailList.UserRoom_Account,
					"UserRoom_Room": this.TRDetailList.Room_Room,
					"UserRoom_Date": this.TRDetailList.UserRoom_Date,
					"UserRoom_Time": this.TRDetailList.UserRoom_Time
				}
				uni.request({
					url: "http://127.0.0.1:3007/API_Lying_DB/cancel",
					method: "POST",
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: obj,
					timeout: 6000,
					success: (res) => {
						console.log(res.data);

						uni.switchTab({
							url: "/pages/main/main",
						})
					},
					fail: (err) => {
						console.log(err);
					}
				})
			}
		}
	}
</script>

<style lang="less">
	.content {

		.view_Test {
			text-align: center;

		}
	}
</style>
