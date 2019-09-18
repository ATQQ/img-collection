<template>
	<view>
		<!-- 分类展示区域 -->
		<view class="types-area">
			<view class="type-item" v-for="(type,index) in typeList" :key="index">
				<text>{{type.name}}</text> <text @tap='deleteType(type.id)'>x</text>
			</view>
		</view>
		<!-- 分类增加区域 -->
		<view class="bottom-area">
			<view class="input-area">
				<input v-model="typeName" placeholder="戳我(*^▽^*)输入分类名称" />
			</view>
			<view class="btn-area">
				<button type="primary" @tap="sureAddType">确认添加</button>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				typeList: [{
					id: -1,
					name: '默认'
				}],
				typeName: ''
			};
		},
		methods: {
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
			/**
			 * 删除指定分类
			 * @param {Number} type_id 分类id
			 */
			deleteType: function(type_id) {
				if (type_id === -1) {
					return;
				}
				uni.showModal({
					title: '提示',
					content: '确认删除这个分类吗',
					showCancel: true,
					cancelText: '取消',
					confirmText: '确认',
					success: res => {
						const {
							confirm
						} = res;
						if (confirm) {
							uni.request({
								url: `${getApp().globalData.baseUrl}types/type`,
								method: 'DELETE',
								data: {
									id: type_id
								},
								
								success: res => {
									const {
										code
									} = res.data;
									if (code === 200) {
										const typeIndex = this.typeList.findIndex(v => {
											return v.id === type_id;
										})
										this.typeList.splice(typeIndex, 1);
									}
								}
							});
						}
					}
				});

			},
			sureAddType: function() {
				if (!this.typeName.trim()) {
					return;
				}

				const findRes = this.typeList.find(v => {
					return v.name === this.typeName;
				})

				if (!!findRes) {
					uni.showToast({
						title: '该分类已经存在'
					});
					this.typeName = '';
					return;
				}
				uni.request({
					url: `${getApp().globalData.baseUrl}types/add`,
					method: 'POST',
					data: {
						name: this.typeName,
						token_id: getApp().globalData.userInfo.id
					},
					success: res => {
						const {
							code
						} = res.data;
						if (code === 200) {
							this.typeList.push(res.data.data);
							this.typeName = "";
						} else {
							this.typeName = "";
							uni.showToast({
								title: '该分类已存在'
							});
						}
					}
				});
			}
		},
		onLoad: function() {
			this.loadTypeList();
		}
	}
</script>

<style lang="scss">
	@import "./type.scss";
</style>
