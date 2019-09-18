<template>
	<view>
		<!-- 图片分类展示 -->
		<uni-collapse>
			<uni-collapse-item :show-animation="true" v-for="(type,index) in typeList" :key="index" :title="type.name">
				<view class="img-container">
					<text v-if="typeimages[type.id].length===0" class="empty-text">什么也没有哟</text>
					
					<view class="img-item" v-for="(img,i) in typeimages[type.id]" :key="i">
						<image :src="img.name" :data-src="img.name" mode="aspectFit" @tap="previewImage"></image>
					</view>
				</view>
				<view class="padding-block"></view>
			</uni-collapse-item>
		</uni-collapse>
		<!-- 上传图片入口 -->
		<uni-fab ref="fab" :pattern="pattern" :content="content" :horizontal="horizontal" :vertical="vertical" :direction="direction"
		 @trigger="trigger" />
	</view>
</template>

<script>
	import uniFab from '@/components/uni-fab/uni-fab.vue'
	import uniCollapse from '@/components/uni-collapse/uni-collapse.vue'
	import uniCollapseItem from '@/components/uni-collapse-item/uni-collapse-item.vue'

	export default {
		components: {
			uniCollapse,
			uniCollapseItem,
			uniFab
		},
		onPullDownRefresh: function() {
			this.loadTypeList();
			this.loadImageList();
			setTimeout(function() {
				uni.stopPullDownRefresh();
			}, 1000);
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
				typeList: [],
				imageList: [],
				typeimages: new Map(),
				horizontal: 'right',
				vertical: 'bottom',
				direction: 'vertical',
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
							this.imageList = data.map(v => {
								v.name = `${getApp().globalData.baseUrl}file/image?name=${v.name}&token_id=${token_id}`
								return v;
							})
							
							this.typeList.forEach(v1=>{
								this.typeimages[v1.id]=this.imageList.filter(v2=>{
									return v2.types_id===v1.id;
								})
							})
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
	page {
		display: flex;
		flex-direction: column;
		box-sizing: border-box;
		background-color: #fff
	}

	view {
		font-size: 28upx;
		line-height: inherit
	}

	.img-container {
		display: flex;
		justify-content: space-around;
		flex-wrap: wrap;

		.img-item {
			width: 30vw;
			height: 30vw;
			margin-top: 0.5rem;

			image {
				width: 100%;
				height: 100%;
			}
		}
	}

	.padding-block {
		display: block;
		width: 100vw;
		height: 3rem;
	}
	
	.empty-text{
		color:rgba(0,0,0,0.6);
		text-align: center;
		padding-top:0.6rem;
	}
</style>
