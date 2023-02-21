<template>
	<view>
		<view class="view_Test">
			<uni-card>
				<text>---展示功能占位---</text>
			</uni-card>
		</view>
		<hr>
		{{NList}}
	</view>
</template>

<script>
	export default {
		data() {
			return {
				NList: {}
			};
		},
		onLoad: function(options) {
			this.NList = JSON.parse(options.data)
			console.log(this.NList);
		},
	}
</script>

<style lang="less">

</style>
