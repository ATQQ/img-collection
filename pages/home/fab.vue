<template>
	<view>
		<!-- 图片分类展示 -->
		<uni-collapse>
			<uni-collapse-item :show-animation="true" v-for="(type,index) in types" :key="index" :title="type.name">
				<view class="img-container">
					<view class="img-item" v-for="(img,i) in imageList" :key="i">
						<image :src="img" :data-src="img" mode="aspectFit" @tap="previewImage"></image>
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
		data() {
			return {
				imageList: ["../../static/logo.png", "../../static/logo.png", "../../static/logo.png", "../../static/logo.png"],
				types: [{
					id: -1,
					name: '默认'
				}, {
					id: 0,
					name: '旅游'
				}],
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
					urls: this.imageList
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
</style>
