<template>
	<view class="container">
		<page-header centerText="文件列表" imgSrc="/static/svg/plus.svg" :handleClickRight="toDirectory"></page-header>
		<view class="index-body">
			<uni-list>
				<uni-list-item v-for="item in fileList" :key="'file-'+item.fullPath+'-index'" :title="item.name" :clickable="true"
				 :thumb="item.isFile?'/static/svg/file.svg':'/static/svg/folder.svg'" @click="toDetail($event, item)">
				 <view slot="right" class="right-icon" @click.stop="removeFile($event, item)">
					 <image src="/static/svg/delete.svg"></image>
				 </view>
				 </uni-list-item>
			</uni-list>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				fileList: []
			}
		},
		onLoad() {
			let listFiles = 
			this.fileList = uni.getStorageSync('listFiles') || [];
		},
		methods: {
			/**
			 * 跳转到目录列表页面
			 */
			toDirectory() {
				uni.navigateTo({
					url:'/pages/directory/directory'
				})
			},
			toDetail(e, item) {
				this.read(item.fullPath, this.toNestMap)
			},
			toNestMap(params) {
				uni.navigateTo({
					url:`/pages/detail/detail?params=${JSON.stringify(params)}`
				})
			},
			/**
			 * 读取文件内容
			 * @param {Object} filePath
			 */
			read(filePath, cb) {
				// 请求本地系统文件对象 plus.io.PRIVATE_WWW：应用运行资源目录常量
				plus.io.requestFileSystem(plus.io.PRIVATE_WWW, function(fobject) {
					fobject.root.getFile(filePath, {
						create: false
					}, function(fileEntry) {
						fileEntry.file(function(file) {
							var fileReader = new plus.io.FileReader();
							fileReader.readAsText(file, 'utf-8');
							fileReader.onloadend = function(evt) {
								console.log(evt.target.result)
								cb(evt.target.result)
							}
						});
					});
			
				});
			},
			removeFile(e, file) {
				let index = this.fileList.findIndex(item=>item.fullPath === file.fullPath);
				this.fileList.splice(index, 1)
				uni.setStorageSync('listFiles', this.fileList)
			}
		}
	}
</script>

<style lang="scss" scoped>
	.container {
		.header {
			// height: 50rpx;
			display: flex;
			justify-content: space-between;
			align-items: center;
			padding: 12rpx 30rpx;
		}

		.index-body {
			margin-top: var(--status-bar-height);
			.right-icon {
				margin-right: 60rpx;
				display: flex;
				align-items: center;
				
				image {
					width: 36rpx;
					height: 36rpx;
				}
			}
			
			/deep/ .uni-list-item__container {
				// padding: 12rpx 30rpx;

				.uni-list-item__content-title {
					// font-size: 16rpx;
				}

				.uni-list--base {
					// width: 36rpx;
					// height: 36rpx;
				}
			}
		}
	}
</style>
