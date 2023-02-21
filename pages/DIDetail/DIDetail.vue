<template>
	<view>
		<view class="view_Test">
			<uni-card>
				<text>---展示功能占位---</text>
			</uni-card>
		</view>
		<hr>
		{{DIList}}
	</view>
</template>

<script>
	export default {
		data() {
			return {
				DIList: {}
			};
		},
		onLoad: function(options) {
			this.DIList = JSON.parse(options.data)
			console.log(this.DIList);
		},
	}
</script>

<style lang="less">

</style>
