<template>
	<view class="mask">
		<view class="content1">
			<!-- 按钮组 -->
			<view class="buttons">
				<view class="cancel" @click="cancel">取消</view>
				<view class="comfirm" @click="confirm">确定</view>
			</view>
			<scroll-view :scroll-y="true" class="check-box-group">
				<checkbox-group>
					<view v-for="(item, index) in weeks" :key="index" :data-index="index">
						<label class="uni-list-cell">
							<checkbox-group class="week-item-checkbox">
								<text>{{item.index | weekStrFilter}}</text>
								<checkbox :value="item.index + ''" v-bind:checked="item.checked" @tap="selectChange(item.index)" />
							</checkbox-group>
						</label>
					</view>
				</checkbox-group>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	export default {
		name: 'uniMultipleSelector',
		props: {
			selectedRepeat: Array
		},
		data() {
			const weeks = [];
			for (let i = 1; i <= 7; i++) {
				weeks.push({
					index: i,
					checked: this.selectedRepeat.indexOf(i) > -1
				})
			}
			return {
				weeks
			};
		},
		mounted() {

		},
		computed: {
			// 隔离变量，防止修改原数据, 并与weeks结合，生成一个新的数组
			privateSelected: function() {
				return this.weeks.map(v => {
					v.checked = this.selectedRepeat.indexOf(v.index) > -1;
					return v;
				});
			}
		},
		methods: {
			//勾选或取消勾选触发事件，拿到已选数据的id
			selectChange(index) {
				this.privateSelected.forEach(v => {
					if (v.index === index) {
						v.checked = !v.checked;
					}
				});
			},
			//取消选择时，不应该保留此次已选或取消选择的数据，应还原为上一次的数据
			cancel() {
				this.$emit('toggle-repeat', false);
			},
			confirm() {
				this.$emit('repeat-confirm', this.privateSelected.filter(v => {
					return v.checked
				}).map(v => {
					return v.index;
				}));
			}
		},
		filters: {
			weekStrFilter: function(value) {
				const timeMap = {
					1: '周一',
					2: '周二',
					3: '周三',
					4: '周四',
					5: '周五',
					6: '周六',
					7: '周日'
				}
				return timeMap[value];
			}
		}
	};
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

	.content1 {
		background: #fff;
		height: 40%;
		position: absolute;
		overflow: hidden;
		left: 0;
		bottom: 0;
		width: 100%;
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

	.check-box-group {
		height: 85%;
	}

	.uni-list-cell {
		display: flex;
		padding: 10rpx 40rpx;
		align-items: center;
	}

	.week-item-checkbox {
		width: 100%;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		align-items: center;
	}
</style>
