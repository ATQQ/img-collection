<script>
	export default {
		globalData: {
			baseUrl: "http://sugar.nat300.top/api/",
			userInfo:{
				id:1,
				token:"abcdefg"
			}
		},
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	@import "./wxcomponents/vant/common/index.wxss";
	/*每个页面公共css */
	@import './common/uni.css';
</style>
