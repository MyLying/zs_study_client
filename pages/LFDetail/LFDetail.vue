<template>
	<view>
		<view class="view_Test">
			<uni-card>
				<text>---展示功能占位---</text>
			</uni-card>
		</view>
		<hr>
		{{LFList}}
	</view>
</template>

<script>
	export default {
		data() {
			return {
				LFList:{}
			};
		},
		onLoad: function(options) {
			this.LFList = JSON.parse(options.data)
			console.log(this.LFList);
		},
	}
</script>

<style lang="less">

</style>
