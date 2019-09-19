<template>
	<view>
		<view class="input-area">
			<input v-model="token" class="uni-input" placeholder="请输入暗号" :password="showPassword" />
			<view class="hid-btn uni-icon uni-icon-eye" :class="[!showPassword ? 'uni-active' : '']" @click="changePassword"></view>
		</view>
		<view class="btn-area">
			<button type="primary" class="sure-go" :loading="loading" @tap="sureGo">GO!</button>
		</view>
		<view class="remember">
			<switch style="transform:scale(0.9)" :checked="checked" @change="rememberToken" />
			记住密码?
		</view>
		
		<view class="sugar" @tap="showEgg">
			<view :style="{opacity:egg?1:0}">
				<input v-model="base" class="uni-input" placeholder="穿透地址" :disabled="!egg"/>
				<view class="btn-area">
					<button type="primary" class="sure-go" @tap="changeBaseUrl">确定</button>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				loading:false,
				showPassword: true,
				token: '',
				checked:false,
				egg:false,
				base:"",
				clickTimes:0
			};
		},
		onLoad:function(){
			this.token=uni.getStorageSync("local_token");
			this.checked=!!uni.getStorageSync("checked");
			getApp().globalData.baseUrl=uni.getStorageSync('baseUrl')||getApp().globalData.baseUrl
			this.base=getApp().globalData.baseUrl;
		},
		methods: {
			changeBaseUrl:function(){
				getApp().globalData.baseUrl=this.base;
				this.egg=false;
				this.clickTimes=0;
				uni.setStorageSync('baseUrl',this.base);
			},
			showEgg:function(){
				this.clickTimes++;
				if(this.clickTimes===6){
					this.egg=!this.egg;
					this.clickTimes=0;
				}
			},
			rememberToken:function(e){
				const {value}=e.detail;
				this.checked=value;
			},
			getUserInfo: function() {
				uni.request({
					url: `${getApp().globalData.baseUrl}user/user`,
					method: "GET",
					header: {
						'token': this.token
					},
					success: (res) => {
						return new Promise((resolve, reject) => {
							const {
								code
							} = res.data;
							if (code === 200) {
								getApp().globalData.userInfo = res.data.userInfo;
								resolve();
							} else {
								uni.showToast({
									title: '令牌不正确'
								});
								reject();
							}
						})
					}
				})
			},
			changePassword: function() {
				this.showPassword = !this.showPassword;
			},
			sureGo: function() {
				this.loading=!this.loading;
				if(this.checked){
					uni.setStorageSync("local_token",this.token);
					uni.setStorageSync("checked",this.checked);
				}else{
					uni.clearStorageSync();
				}
				
				uni.request({
					url: `${getApp().globalData.baseUrl}user/login`,
					method: "POST",
					data: {
						token: this.token
					},
					success: async (res) => {
						const {
							code
						} = res.data;
						if (code === 200) {
							this.loading=!this.loading;
							uni.showToast({
								title: "暗号正确"
							})
							await this.getUserInfo();

							setTimeout(function() {
								uni.redirectTo({
									url: '../home/fab'
								});
							}, 1000);

						} else {
							uni.showToast({
								icon: 'none',
								title: "暗号不正确"
							})
							this.loading=!this.loading;
						}
					},
					fail: (err) => {
						console.error(err);
						this.loading=!this.loading;
					}
				})
			}
		},
	}
</script>

<style lang="scss">
	@import "./index.scss"
</style>
