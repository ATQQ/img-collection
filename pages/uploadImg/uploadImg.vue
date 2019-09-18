<template>
	<view>
		<view class="uni-common-mt">
			<form>
				<view class="uni-list">
					<view class="uni-list-cell">
						<view class="uni-list-cell-left">
							<view class="uni-label">分类</view>
						</view>
						<view class="uni-list-cell-right">
							<picker :range="listType" @change="listTypeChange" :value="listTypeInedx" mode="selector">
								<view class="uni-input">{{listType[listTypeInedx]}}</view>
							</picker>
						</view>
					</view>
					<view class="uni-list-cell">
						<view class="uni-list-cell-left">
							<view class="uni-label">来源</view>
						</view>
						<view class="uni-list-cell-right">
							<picker :range="sourceType" @change="sourceTypeChange" :value="sourceTypeIndex" mode="selector">
								<view class="uni-input">{{sourceType[sourceTypeIndex]}}</view>
							</picker>
						</view>
					</view>

					<view class="uni-list-cell">
						<view class="uni-list-cell-left">
							<view class="uni-label">质量</view>
						</view>
						<view class="uni-list-cell-right">
							<picker :range="sizeType" @change="sizeTypeChange" :value="sizeTypeIndex" mode="selector">
								<view class="uni-input">{{sizeType[sizeTypeIndex]}}</view>
							</picker>
						</view>
					</view>

					<view class="uni-list-cell">
						<view class="uni-list-cell-left">
							<view class="uni-label">限制</view>
						</view>
						<view class="uni-list-cell-right">
							<view class="uni-input">{{count}}</view>
						</view>
					</view>
				</view>

				<!-- 添加图片区域 -->
				<view class="uni-list list-pd">
					<view class="uni-list-cell cell-pd">
						<view class="uni-uploader">
							<view class="uni-uploader-head">
								<view class="uni-uploader-title">点击可预览选好的图片</view>
								<view class="uni-uploader-info">{{imageList.length}}/9</view>
							</view>
							<view class="uni-uploader-body">
								<view class="uni-uploader__files">
									<block v-for="(image,index) in imageList" :key="index">
										<view class="uni-uploader__file">
											<image class="uni-uploader__img" :src="image" :data-src="image" @tap="previewImage"></image>
										</view>
									</block>
									<view class="uni-uploader__input-box">
										<view class="uni-uploader__input" @tap="chooseImage"></view>
									</view>
								</view>
							</view>
						</view>
					</view>
				</view>
			
			<!-- 上传按钮 -->
			<view class="btn-container">
				<button type="primary" :loading="uploadStatus" @tap="sureUpload">{{uploadBtnText}}</button>
			</view>
			
			</form>
		</view>
	</view>
</template>
<script>
	var sourceType = [
		['camera'],
		['album'],
		['camera', 'album']
	]
	var sizeType = [
		['compressed'],
		['original'],
		['compressed', 'original']
	]
	export default {
		data() {
			return {
				uploadBtnText:"开始上传",
				uploadStatus:false,
				uploadCount:0,
				listTypeInedx: 0,
				listType: ['默认', '旅游', '国庆', '中秋'],
				typeList:[],
				title: '图片上传',
				imageList: [],
				sourceTypeIndex: 2,
				sourceType: ['拍照', '相册', '拍照或相册'],
				sizeTypeIndex: 2,
				sizeType: ['压缩', '原图', '压缩或原图'],
				count: 9
			}
		},
		onUnload() {
			this.imageList = [],
				this.sourceTypeIndex = 2,
				this.sourceType = ['拍照', '相册', '拍照或相册'],
				this.sizeTypeIndex = 2,
				this.sizeType = ['压缩', '原图', '压缩或原图'],
				this.count=9;
				console.log("onUnload uploadImage page");
		},
		onLoad:function(){
			this.loadTypeList();
		},
		methods: {
			sureUpload:async function(){
				this.uploadStatus=true;
				this.uploadBtnText=`味儿咯..味儿咯..已完成${this.uploadCount}`;
				this.imageList.forEach(async (filePath,index)=>{
					const res=await this.uploadFile(filePath);
					this.uploadBtnText=`味儿咯..味儿咯..已完成${++this.uploadCount}`;
					console.log(this.uploadCount,this.imageList.length);
					if(this.uploadCount===this.imageList.length){
						this.uploadBtnText=`开始上传`;
						this.uploadStatus=false;
						this.imageList=[];
						uni.showToast({
							title:"么么哒",
							duration:2000
						})
					}
				})
				
			},
			uploadFile:function(filePath){
				return new Promise(resolve=>{
					uni.uploadFile({
						url:`${getApp().globalData.baseUrl}file/upload`,
						formData:{
							token_id:getApp().globalData.userInfo.id,
							types_id:this.typeList[this.listTypeInedx].id
						},
						filePath:filePath,
						name:"file",
						success: (res) => {
							resolve(res);
						}
					})
				})
			}
			,
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
							this.listType=this.typeList.map(v=>{
								return v.name
							})
						}
					}
				})
			},
			sourceTypeChange: function(e) {
				this.sourceTypeIndex = e.target.value
			},
			listTypeChange: function(e) {
				this.listTypeInedx = e.target.value;
				console.log(e.target.value);
			},
			sizeTypeChange: function(e) {
				this.sizeTypeIndex = e.target.value
			},
			chooseImage: async function() {
				if (this.imageList.length === 9) {
					let isContinue = await this.isFullImg();
					console.log("是否继续?", isContinue);
					if (!isContinue) {
						return;
					}
				}
				uni.chooseImage({
					sourceType: sourceType[this.sourceTypeIndex],
					sizeType: sizeType[this.sizeTypeIndex],
					count: this.imageList.length + this.count > 9 ? 9 - this.imageList.length : this.count,
					success: (res) => {
						this.imageList = Array.from(new Set(this.imageList.concat(res.tempFilePaths)));
					}
				})
				
				
			},
			isFullImg: function() {
				return new Promise((res) => {
					uni.showModal({
						content: "已经有9张图片了,是否清空现有图片？",
						success: (e) => {
							if (e.confirm) {
								this.imageList = [];
								res(true);
							} else {
								res(false)
							}
						},
						fail: () => {
							res(false)
						}
					})
				})
			},
			previewImage: function(e) {
				var current = e.target.dataset.src
				uni.previewImage({
					current: current,
					urls: this.imageList
				})
			}
		}
	}
</script>

<style lang="scss">
	.cell-pd {
		padding: 22upx 30upx;
	}

	.list-pd {
		margin-top: 50upx;
	}
	.btn-container{
		padding: 1rem;
	}
</style>
