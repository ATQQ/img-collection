<script>
	export default {
		globalData: {
			baseUrl: "http://yk6x8t.natappfree.cc/api/",
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
	/*每个页面公共css */
	@import './common/uni.css';
</style>
