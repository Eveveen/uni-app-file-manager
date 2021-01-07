<template>
	<view class="footer">
		<view class="footer-wrapper">
			<slot name="left">
				<view class="left" @click="clickLeft">
					{{leftText}}
				</view>
			</slot>
			<slot name="center"></slot>
			<slot name="right">
				<view class="right" @click="clickRight">
					<view :class="isRightBtn&&'right-btn'">{{rightText}}</view>
				</view>
			</slot>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
			leftText: {
				type: String,
				default: ''
			},
			rightText: {
				type: String,
				default: ''
			},
			clickLeft: {
				type: Function,
				default: () => {}
			},
			clickRight: {
				type: Function,
				default: () => {}
			},
			isRightBtn: {
				type: Boolean,
				default: false
			}
		},
		data() {
			return {

			};
		}
	}
</script>

<style lang="scss" scoped>
	.footer {
		position: fixed;
		bottom: 0;
		background-color: #F5F5F5;
		width: 100%;
		box-shadow: 0px 0px 5px #CCCCCC;

		.footer-wrapper {
			display: flex;
			line-height: 30px;
			// font-size: 16px;
			justify-content: space-around;
			padding: 10px 18px;
			letter-spacing: 2px;

			.left {
				flex: 1;
				text-align: center;
			}

			.right {
				color: #1271e8;
				flex: 1;
				text-align: center;
				display: flex;
				justify-content: center;
			}

			.right-btn {
				background: #1271e8;
				color: #fff;
				padding: 10rpx;
				border-radius: 20px;
			}
		}
	}
</style>
