<template>
	<view>
		<view class="input-area">
			<input class="uni-input" placeholder="请输入暗号" :password="showPassword" />
			<view class="hid-btn uni-icon uni-icon-eye" :class="[!showPassword ? 'uni-active' : '']" @click="changePassword"></view>
		</view>
		<view class="btn-area">
			<button type="primary" class="sure-go">GO!</button>
		</view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				showPassword: true,
			};
		},
		methods: {
			changePassword: function() {
				this.showPassword = !this.showPassword;
			},
		},
	}
</script>

<style lang="scss">
	.input-area {
		margin-top: 9rem;
		position: relative;
		input{
			font-size: 1rem;
			text-align: center;
			padding-right: 1rem;
		}
		.hid-btn{
			position: absolute;
			right: 0.5rem;
			top:0.1rem;
		}
	}
	.btn-area{
		padding: 1rem;
		button{
			width: 100%;
		}
	}
</style>
