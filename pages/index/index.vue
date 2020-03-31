<template>
	<view class="content">
		<view class="clock-area">
			<clock v-bind:todo-list="todoList"></clock>
		</view>
		<view class="clock-list">
			<todoList v-bind:todo-list="todoList" @switch-change="switchChange"></todoList>
		</view>
		<view class="clock-navigator-add" @click="addClock()">+</view>
	</view>
</template>

<script>
	import clock from '../../components/clock/index.vue'
	import todoList from '../../components/todoList/index.vue'
	export default {
		data() {
			return {
				todoList: [],
				hash: (new Date()).getTime()
			}
		},
		onShow() {
			let preClockData = uni.getStorageSync('clockListData');
			this.todoList = preClockData || [];
		},
		methods: {
			switchChange: function(id) {
				// 查找
				let index = -1;
				let changeItem = null;
				this.todoList.forEach((v, i) => {
					if (v.id === id) {
						index = i;
						changeItem = v;
						return false;
					}
				});
				changeItem.isNo = !changeItem.isNo;
				this.todoList.splice(index, 1, changeItem);
				uni.setStorageSync('clockListData', this.todoList);
			},
			addClock: function() {
				/**
				 * 此处有两种方法进行传值
				 * 1、使用地址加参数的方式进行
				 * 2、使用storage的方式进行
				 */
				// 本次使用storage方式进行， 设置storage为空值表示新建
				try {
					uni.setStorageSync('clockDetailData', null);
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
			}
		},
		components: {
			clock,
			todoList
		},
		computed: {

		}
	}
</script>

<style>
	.content {
		position: relative;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: flex-start;
		padding: 15rpx 15rpx 0 15rpx;
		overflow-y: scroll;
	}

	.clock-list {
		width: 90%;
		margin-top: 30rpx;
	}

	.clock-navigator-add {
		position: fixed;
		bottom: 50rpx;
		font-size: 50rpx;
		width: 80rpx;
		height: 80rpx;
		text-align: center;
		background-color: #808080;
		color: #F8F8F8;
	}
</style>
