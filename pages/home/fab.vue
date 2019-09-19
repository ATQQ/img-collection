<template>
	<view>
		<!-- 图片分类展示 -->
		<view class="top-area">
			<van-tabs sticky animated :active="active" @click="changeType" color="#1989fa">
				<van-tab v-for="(category,index) in typeList" :title="category.name" :key="index"></van-tab>
			</van-tabs>
		</view>
		
		<!-- 空内容提示 -->
		<view class="empty-text" v-if="dataImgsKeys.length===0">什么也没有嘞</view>
		<!-- 图片显示区域  -->
		<view class="img-area" v-for="(date,index) in dataImgsKeys" :key="index">
			<view class="title">{{date}}</view>
			<view class="img-container">
				<view class="img-item" v-for="(img,i) in dataImgs[date]" :key="i">
					<image :src="img.name" :data-src="img.name" mode="aspectFill" @tap="previewImage"></image>
				</view>
			</view>
		</view>


		<!-- 上传图片入口 -->
		<uni-fab ref="fab" :pattern="pattern" :content="content" horizontal="right" vertical="bottom" direction="vertical"
		 @trigger="trigger" />

	</view>
</template>

<script>
	import uniFab from '@/components/uni-fab/uni-fab.vue'
	export default {
		components: {
			uniFab
		},
		onPullDownRefresh: function() {
			this.loadTypeList();
			this.loadImageList();
			setTimeout(function() {
				uni.stopPullDownRefresh();
			}, 500);
		},
		onLoad: function() {
			this.typeimages = {
				'abc': [{
					name: "../../static/logo.png"
				}, {
					name: "../../static/logo.png"
				}]
			}

			this.loadTypeList();
			this.loadImageList();
		},
		data() {
			return {
				obj: {
					name: "小明",
					age: 18
				},
				active: 0, //默认选中第一个分类
				typeList: [],
				imageList: [],
				orignImageList: [],
				dataImgs: {},
				dataImgsKeys: [],
				pattern: {
					color: '#7A7E83',
					backgroundColor: '#fff',
					selectedColor: '#007AFF',
					buttonColor: '#007AFF'
				},
				content: [{
					iconPath: '/static/image.png',
					selectedIconPath: '/static/image.png',
					text: '图片',
					active: false
				}, {
					iconPath: '/static/type.png',
					selectedIconPath: '/static/type.png',
					text: '分类',
					active: false
				}]
			}
		},
		methods: {

			/**
			 * 分类切换设备数据
			 * @param {Event} e
			 */
			changeType: function(e) {
				const {
					index
				} = e.detail;
				this.active=index;
				this.loadSelectType(index);
			},
			loadSelectType:function(index){
				if (this.typeList[index].id === 0) {
					this.imageList = this.orignImageList;
				} else {
					this.imageList = this.orignImageList.filter(v => {
						return v.types_id === this.typeList[index].id;
					})
				}
				
				// 取得所有日期
				const obj = {};
				this.imageList.forEach(v => {
					const key = (new Date(v.create_date)).Format("yyyy-MM-dd");
					if (obj.hasOwnProperty(key)) {
						obj[key].push(v);
					} else {
						obj[key] = [];
						obj[key].push(v);
					}
				
				});
				this.dataImgs = obj;
				this.dataImgsKeys = Object.keys(obj);
			},
			loadImageList: function() {
				const token_id = getApp().globalData.userInfo.id;
				uni.request({
					url: `${getApp().globalData.baseUrl}images/image`,
					method: "GET",
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						token_id
					},
					success: (res) => {
						const {
							code
						} = res.data;
						const {
							data
						} = res.data;
						if (code === 200) {
							this.orignImageList = data.map(v => {
								v.name = `${getApp().globalData.baseUrl}file/image?name=${v.name}&token_id=${token_id}`
								return v;
							})
							this.imageList = this.orignImageList;
							this.loadSelectType(this.active);
						}

					}
				})
			},
			loadTypeList: function() {
				uni.request({
					url: `${getApp().globalData.baseUrl}types/type`,
					method: "GET",
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						token_id: getApp().globalData.userInfo.id
					},
					success: (res) => {
						const {
							code
						} = res.data;
						if (code === 200) {
							this.typeList = [{
								id: 0,
								name: "全部"
							}, {
								id: -1,
								name: '默认'
							}].concat(res.data.data);
						}
					}
				})
			},
			trigger(e) {
				const {
					text
				} = this.content[e.index];
				if (text === '图片') {
					uni.navigateTo({
						url: '../uploadImg/uploadImg'
					})
				} else if (text === '分类') {
					uni.navigateTo({
						url: '../type/type'
					})
				}

			},
			previewImage: function(e) {
				const current = e.target.dataset.src
				uni.previewImage({
					current: current,
					urls: this.imageList.map(v => v.name)
				})
			}
		}
	}
</script>

<style scoped lang="scss">
	@import "./home.scss"
</style>
