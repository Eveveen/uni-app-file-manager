<template>
	<view class="directory-container">
		<page-header :hasBack="true" centerText="目录" rightText="确定" :handleClickRight="handleConfirm"></page-header>
		<view class="header-wrapper">
			<view class="header-content">
				<view class="path">路径：{{path}}/</view>
				<view @click="toPrePath">上一级</view>
			</view>
		</view>
		<view class="directory-body">
			<uni-list>
				<uni-list-item v-for="item in fileList" :key="'file-'+item.fullPath+'-index'" :title="item.name" :clickable="true"
				 :thumb="item.isFile?'/static/svg/file.svg':'/static/svg/folder.svg'" @click="getNextFile($event, item)">
					<view slot="right" v-if="item.isFile" class="checkbox">
						<image v-if="!item.isAdded" :src="item.checked?'/static/svg/checked.svg':'/static/svg/unchecked.svg'"></image>
						<view v-else>已添加</view>
					</view>
				</uni-list-item>
			</uni-list>
		</view>
		<u-footer rightText="确认" :clickRight="handleConfirm">
			<view slot="left" class="left-footer" @click="handleCheckedAll">
				<image :src="checkedAll ?'/static/svg/checked.svg':'/static/svg/unchecked.svg'" class="checked-img"></image>
				<view>全选 </view>
			</view>
			<view slot="center" class="check-count">已选中 {{checkedFiles.length}} 条</view>
		</u-footer>
	</view>
</template>

<script>
	var File = plus.android.importClass("java.io.File");
	export default {
		data() {
			return {
				fileList: [],
				path: '', // 当前文件夹路径
				rootPath: '', // 本机的根路径
				checkedAll: false, // 是否全选
				checkedFiles: [], // 已选中的文件
			}
		},
		onLoad() {
			this.showLoading();
			this.loadDir()
		},
		methods: {
			/**
			 * 加载目录
			 */
			loadDir() {
				let environment = plus.android.importClass("android.os.Environment");
				// 判断SD卡是否插入(涉及到SDK的读取，最好先判断SDK是否插入)
				// environment.getExternalStorageState() === environment.MEDIA_MOUNTED
				// 获得sd卡根目录
				let path = environment.getExternalStorageDirectory().getAbsolutePath();
				this.rootPath = path;
				let lastPath = uni.getStorageSync('lastPath');
				// 如果上次打开过目录，则使用上次的路径
				if(lastPath) {
					path = lastPath;
				}
				this.getFiles({
					fullPath: path,
					isFile: false
				});
			},
			/**
			 * 获取下一级目录文件
			 * @param {Object} e
			 * @param {Object} file
			 */
			getNextFile(e, file) {
				this.showLoading();
				this.checkedAll = false;
				if (file.isFile) {
					uni.hideLoading()
					this.$set(file, 'checked', !file.checked)
					let checkedAll = true;
					this.fileList && this.fileList.forEach(item => {
						if (!item.checked && item.isFile) {
							checkedAll = false;
						}
					});
					this.checkedAll = checkedAll;
					this.getCheckedFiles();
				} else {
					this.getFiles(file)
				}
			},
			/**
			 * 获取文件夹内容
			 * @param {Object} file
			 */
			getFiles(file) {
				if (!file.isFile) {
					let dirs = [],
						files = [];
					let directory = new File(file.fullPath);
					let arr = directory.listFiles();
					this.path = file.fullPath;
					arr.forEach(item => {
						if (!item.isHidden()) {
							if (item.isDirectory()) {
								dirs.push({
									name: item.getName(),
									fullPath: item.getPath(),
									isFile: false,
								})
							} else {
								// 文件  
								files.push({
									name: item.getName(),
									fullPath: item.getPath(),
									isFile: true, // 是否是文件
									isAdded: this.isAdded(item.getPath()) // 是否已经添加到文件列表页
								})
							}
						}

					})
					this.fileList = [...dirs, ...files]
				}
				uni.hideLoading()
			},
			/**
			 * 跳转到上一级目录
			 */
			toPrePath() {
				this.showLoading();
				this.checkedAll = false;
				if (this.path !== this.rootPath) {
					let index = this.path.lastIndexOf('/')
					let prePath = this.path.substr(0, index);
					this.getFiles({
						fullPath: prePath,
						isFile: false
					})
				}
			},
			/**
			 * 判断当前文件是否已经被添加
			 * @param {Object} path
			 */
			isAdded(path) {
				let listFiles = uni.getStorageSync('listFiles') || [];
				if (listFiles.findIndex(item => item.fullPath === path) != -1) {
					return true;
				}
				return false;
			},
			/**
			 * 获取已选中的文件
			 */
			getCheckedFiles() {
				this.checkedFiles = this.fileList.filter(item => item.checked === true) || [];
			},
			/**
			 * 确认选择文件
			 */
			handleConfirm() {
				let listFiles = uni.getStorageSync('listFiles');
				uni.setStorageSync('lastPath', this.path)
				uni.setStorageSync('listFiles', [...listFiles, ...this.checkedFiles]);
				uni.navigateTo({
					url: '/pages/index/index'
				})
			},
			/**
			 * 取消选择
			 */
			handleCancel() {
				uni.navigateTo({
					url: '/pages/index/index'
				})
			},
			/**
			 * 全选
			 */
			handleCheckedAll() {
				this.checkedAll = !this.checkedAll;
				// 全选
				if (this.checkedAll) {
					this.fileList.forEach(item => {
						item.isFile && !item.isAdded && (item.checked = true);
					})
				}
				// 取消全选
				else {
					this.fileList.forEach(item => {
						item.checked = false;
					})
				}
				this.getCheckedFiles()
			},
			showLoading = () => {
				uni.showLoading({
					title: '加载中...'
				})
			}
		},

	}
</script>

<style lang="scss" scoped>
	.directory-container {
		.header-wrapper {
			margin-top: var(--status-bar-height);
			position: fixed;
			width: 100%;
			z-index: 999;
			background-color: #FFFFFF;
			
			.header-content {
				display: flex;
				justify-content: space-between;
				align-items: center;
				padding: 16rpx 30rpx;
				
				.path {
					width: 80%;
					overflow: hidden;
					text-overflow: ellipsis;
					white-space: nowrap;
				}
			}
		}

		.directory-body {
			margin-top: calc(var(--status-bar-height) + 32rpx + 24px);
			margin-bottom: 44px;
			.checkbox {
				// width: 150rpx;
				// height: 150rpx;
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

		.left-footer {
			display: flex;
			align-items: center;
			flex: 1;

			.checked-img {
				width: 36rpx;
				height: 36rpx;
				margin-right: 16rpx;
			}
		}

		.check-count {
			flex: 1;
		}
		
		/deep/ .footer .footer-wrapper .right {
			justify-content: flex-end;
		}
	}
</style>
