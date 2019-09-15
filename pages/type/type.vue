<template>
	<view>
		<!-- 分类展示区域 -->
		<view class="types-area">
			<view class="type-item" v-for="i in 20">
				<text>名称</text> <text>x</text>
			</view>
		</view>
		<!-- 分类增加区域 -->
		<view class="bottom-area">
			<view class="input-area">
				<input class="" placeholder="戳我(*^▽^*)输入分类名称" />
			</view>
			<view class="btn-area">
				<button type="primary">确认添加</button>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			};
		}
	}
</script>

<style lang="scss">
	@import "./type.scss";
</style>
