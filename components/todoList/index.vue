<template>
	<view class="todo-list-content">
		<view class="todo-list-item" v-if="todoList.length > 0" v-for="item in todoList" v-bind:key="item.id">
			<view @tap="editClock(item)" @longpress="deleteClock(item)" class="item-info">
				<text>{{item.time | timeStrFilter}}</text>
				<view class="item-info-desc">
					<text>{{item.name}},</text>
					<text class="item-info-desc-repeat">{{item.repeat | repeatFormat}}</text>
				</view>
			</view>
			<view class="item-switch">
				<switch :checked="item.isNo" @change="$emit('switch-change',item.id)"></switch>
			</view>
		</view>
		<view class="todo-list-item" v-if="todoList.length === 0">您未创建任何闹钟</view>
	</view>
</template>

<script>
	export default {
		props: {
			todoList: Array
		},
		data: function() {
			return {

			}
		},
		methods: {
			editClock: function(item) {
				try {
					uni.setStorageSync('clockDetailData', item);
					// 跳转
					uni.navigateTo({
						url: '../clockDetail/index'
					})
				} catch (e) {
					uni.showToast({
						title: '出现了错误，请稍后再试！',
						mask: true,
						duration: 2000
					});
				}
			},
			deleteClock: function(item) {
				uni.showModal({
					content: '确认删除吗？',
					showCancel: true,
					confirmColor: '#ff0000',
					success: () => {
						// 找到元素
						const preClockData = uni.getStorageSync('clockListData');
						// 按id筛选
						let exsitIndex = -1;
						preClockData.forEach((v, i) => {
							if (v.id === item.id) {
								exsitIndex = i;
								return false;
							}
						});
						// 修改
						preClockData.splice(exsitIndex, 1);
						this.todoList.splice(exsitIndex, 1);
						uni.setStorageSync('clockListData', preClockData);
						// 更新数据
					},
					fail: () => {

					}
				})
			}
		},
		filters: {
			repeatFormat: function(value) {
				const timeMap = {
					1: '周一',
					2: '周二',
					3: '周三',
					4: '周四',
					5: '周五',
					6: '周六',
					7: '周日'
				}
				return value.length === 7 ? '每天' : JSON.stringify(value) === JSON.stringify([1, 2, 3, 4, 5]) ? '周一至周五' :
					value.length === 0 ? '不重复' : value.map(v => {
						return timeMap[v]
					}).join(' ');
			},
			timeStrFilter: function(value) {
				const hour = value.split(':')[0] - 0;
				const minute = value.split(':')[1] - 0;
				const hourStr = hour < 10 ? '0' + hour : hour + '';
				const minuteStr = minute < 10 ? '0' + minute : minute + '';
				return hourStr + ':' + minuteStr;
			}
		}
	}
</script>

<style>
	.todo-list-content {
		width: 100%;
		display: flex;
		flex-direction: column;
		justify-content: flex-start;
		align-items: center;
	}

	.todo-list-item {
		width: 100%;
		height: 150rpx;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		align-items: center;
		border-bottom: 1rpx solid rgba(192, 192, 192, .3);
	}

	.item-info {
		width: 60%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: flex-start;
	}

	.item-info>text {
		font-size: 30rpx;
	}

	.item-info-desc>text {
		font-size: 25rpx;
	}

	.item-info-desc {
		display: flex;
		flex-direction: row;
		justify-content: flex-start;
		align-items: center;
		text-align: left;
	}

	.item-info-desc-repeat {
		margin-left: 20rpx;
	}
</style>
