<template>
	<view class="mask">
		<view class="input-content">
			<!-- 按钮组 -->
			<view class="buttons">
				<view class="cancel" @click="cancel">取消</view>
				<view class="comfirm" @click="confirm">确定</view>
			</view>
			<view class="input-clock">
				<input maxlength="10" class="input-clock-name" :value="privateName" @input="nameChange" />
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
			name: String
		},
		data() {
			return {
				privateName: this.name + ''
			}
		},
		methods: {
			cancel() {
				this.$emit('toggle-input', false);
			},
			confirm() {
				this.$emit('input-confirm', this.privateName);
			},
			nameChange: function(e) {
				this.privateName = e.detail.value;
			}
		}
	}
</script>

<style>
	.mask {
		position: fixed;
		width: 100%;
		height: 100%;
		bottom: 0;
		left: 0;
		background: rgba(0, 0, 0, 0.1);
		z-index: 1000;
		transition: all 0.3s;
	}

	.input-content {
		position: absolute;
		width: 100%;
		height: 20%;
		left: 0;
		bottom: 0;
		background: #fff;
		overflow: hidden;
		transition: all 0.3s;
	}

	.buttons {
		display: flex;
		justify-content: space-between;
		color: red;
		/* margin-bottom:40rpx; */
		padding: 20rpx 40rpx;
		font-family: 'Microsoft YaHei';
		font-size: 36rpx;
		border-bottom: 1rpx solid #f5f5f5;
	}

	.cancel {
		color: #888;
	}

	.comfirm {
		color: #007aff;
	}

	.input-clock {
		text-align: center;
	}

	.input-clock-name {
		position: absolute;
		display: inline-block;
		width: 80%;
		height: 50rpx;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		border-bottom: 1px solid rgba(192, 192, 192, .8);
		text-align: center;
		font-size: 30rpx;
	}
</style>
