<template>
	<view class="header-container han">
		<view class="status_bar">
			<!-- 这里是状态栏 -->
		</view>
		<view class="header">
			<view class="header_content">
				<view class="left_content">
					<view v-if="!hasBack">{{ leftText }}</view>
					<image src="/static/svg/left.png" class="left_img" v-if="hasBack" @click="backToPage"></image>
				</view>
				<view class="center_content">
					<view class="header_text" :style="showLetter">
						{{ centerText }}
					</view>
				</view>
				<view class="right_content">
					<view class="right_text" :style="showRightText && 'display:inline' || imgSrc && 'display:none'" @click="handleClickRight">
						{{ rightText }}
					</view>
					<view class="right_text" :style="!imgSrc1 && !imgSrc && 'display:none'">
						<image :src="imgSrc1" class="img_size" style="margin-right: 15px;" :style="!imgSrc1 && 'display:none'" @click="handleClickRight1"></image>
						<image :src="imgSrc" class="img_size" style="margin-right: 6px;" :style="!imgSrc && 'display:none'" @click="handleClickRight"></image>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
			leftText: {
				type: String,
				default: () => {
					return '';
				}
			},
			centerText: {
				type: String,
				default: () => {
					return '';
				}
			},
			showRightText: {
				type: Boolean,
				default: () => {
					return false;
				}
			},
			rightText: {
				type: String,
				default: () => {
					return '';
				}
			},
			imgSrc: {
				type: String,
				default: () => {
					return '';
				}
			},
			imgSrc1: {
				type: String,
				default: () => {
					return '';
				}
			},
			backUrl: {
				type: String,
				default: () => {
					return '';
				}
			},
			hasBack: {
				type: Boolean,
				default: false
			},
			isShowIcon: {
				type: Boolean,
				default: true
			},
			handleClickRight: {
				type: Function,
				default: () => {}
			},
			handleClickRight1: {
				type: Function,
				default: () => {}
			},
		},
		data() {
			return {
				showIcon: false,
				showLetter: true
			}
		},
		created() {
			this.handleShowIcon();
			const system_info = uni.getStorageSync('system_info');
			if (system_info.language === 'en') {
				this.showLetter = false;
			} else {
				this.showLetter = true;
			}
		},
		methods: {
			backToPage() {
				uni.showTabBar({
					animation:true
				})
				if (!this.backUrl) {
					uni.navigateBack({});
				} else {
					uni.switchTab({
						url: this.backUrl,
					});
				}
			},
			handleShowIcon() {
				if (!this.hasBack && this.leftText === '' && this.isShowIcon) {
					this.showIcon = true;
				}
			}
		},
	};
</script>

<style lang="scss" scoped>
	.status_bar {
		height: var(--status-bar-height);
		width: 100%;
		background-color: $bar-color;
		position: fixed;
		top: 0;
		z-index: 998;
	}

	.header-container {
		display: block;
		box-sizing: border-box;
		height: 44px;
	}

	.header {
		position: fixed;
		left: 0;
		top: 0;
		width: 100%;
		z-index: 998;
		padding-top: calc(var(--status-bar-height));

		.header_content {
			height: 44px;
			display: flex;
			align-items: center;
			justify-content: space-between;
			background-color: $bar-color;
			flex-wrap: nowrap;
		}

		.left_content {
			flex: 1;
			height: 100%;
			display: flex;
			align-items: center;
		}

		.center_content {
			flex: 1.2;
		}

		.right_content {
			flex: 1;
			text-align: right;
			margin-right: 10px;
			display: flex;
			justify-content: flex-end;
			align-items: center;
		}

		.header_text {
			color: #ffffff;
			height: 100%;
			display: flex;
			align-items: center;
			justify-content: center;
			font-weight: 700;
			font-size: 16px;
		}

		.right_text {
			color: #ffffff;
			font-size: 30rpx;
			// margin-right: 30rpx;
			// margin-left: -80rpx;
		}

		.left_img {
			// margin-left: 20rpx;
			// margin-right: -100rpx;
			width: 40rpx;
			height: 40rpx;
			margin-left: 10px;
		}

		.app_img {
			width: 28px;
			height: 28px;
			margin-left: 10px;
			border-radius: 5px;
		}

		.img_size {
			width: 25px;
			height: 25px;
		}
	}
</style>
