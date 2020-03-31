<template>
	<view class="clock-area-content">
		<view class="clock-area-show">
			<view class="clock-area-outer">
				<view class="clock-pin" v-for="item in 6" v-bind:key="`pin-hour-${item}`" :style="{transform: `rotate(${item * 30}deg)`}"></view>
				<view class="clock-pin-sec" v-for="item in 30" v-bind:key="`pin-min-${item}`" :style="{transform: `rotate(${item * 6}deg)`}"></view>
			</view>
			<view class="clock-area-inner">
				<view class="clock-area-inner-block">
					<view class="clock-pin-num" v-for="item in 12" v-bind:key="item" :style="{transform: `rotate(${item * 30}deg)`}">
						<text class="clock-pin-num-font">{{item}}</text>
					</view>
				</view>
				<view class="clock-area-inner-needle">
					<view class="clock-area-inner-needle-hour" :style="{transform: `rotate(${hourRotate}deg)`}">
						<view class="needle-hour-1 needle-white-content">
							<view class="needle-white"></view>
						</view>
						<view class="needle-hour-2"></view>
					</view>
					<view class="clock-area-inner-needle-min" :style="{transform: `rotate(${minRotate}deg)`}">
						<view class="needle-min-1 needle-white-content">
							<view class="needle-white"></view>
						</view>
						<view class="needle-min-2"></view>
					</view>
					<view class="clock-area-inner-needle-sec" :style="{transform: `rotate(${secRotate}deg)`}">
						<view class="needle-sec-1"></view>
						<view class="needle-sec-2"></view>
					</view>
				</view>
				<view class="clock-area-inner-mini-rotate"></view>
			</view>
		</view>
		<view class="clock-area-desc">
			<text>{{timeDistance}}</text>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
			todoList: Array
		},
		data() {
			return {
				date: new Date()
			}
		},
		onLoad() {

		},
		created: function() {
			setInterval(this.getNewDate, 1000);
		},
		methods: {
			getNewDate: function() {
				this.date = new Date();
			}
		},
		computed: {
			hourRotate: function() {
				return this.date.getHours() * 30 + this.date.getMinutes() * 0.5;
			},
			minRotate: function() {
				return this.date.getMinutes() * 6 + this.date.getSeconds() * 0.1;
			},
			secRotate: function() {
				return this.date.getSeconds() * 6;
			},
			timeDistance: function() {
				if (this.todoList.length < 1) {
					return '无闹钟';
				}
				const DAYTIME = 24 * 60 * 60 * 1000;
				const HOURTIME = 60 * 60 * 1000;
				const MINUTETIME = 60 * 1000;
				// todoList 排序
				const filterList = this.todoList.filter(v => {
					return v.isNo;
				});
				if (filterList.length < 1) {
					return '所有闹钟已关闭';
				}
				// 当前时间
				const timeObj = {
					year: this.date.getFullYear(),
					month: this.date.getMonth() + 1,
					date: this.date.getDate(), // 月的第几天
					day: this.date.getDay() === 0 ? 7 : this.date.getDay() // 周的第几天 0为周天
				};
				// 由于repeat是排序的，所以将计算所有开启状态的repeat[0]的时间
				// 根据当前时间的day与repeat[0]对比， 如果repeat[0]大于就是本周，如果小于就是下周, 若[0]不存在，则就是明天
				const allTime = filterList.map(v => {
					// 过滤掉repeat中比这个day小的值
					const repeatTemp = v.repeat.filter(vv => {
						return vv > timeObj.day;
					});
					const distanceDay = v.repeat[0] ? (repeatTemp[0] ? (repeatTemp[0] > timeObj.day ? repeatTemp[0] - timeObj.day : (7 - timeObj.day) +
						repeatTemp[0]) : 7 - v.repeat[0]) : 1;
					const hour = parseInt(v.time.split(':')[0]);
					const minute = parseInt(v.time.split(':')[1]);
					// 净时间
					const pureTime = new Date(`${timeObj.year}/${timeObj.month}/${timeObj.date} 00:00:00`);
					const result = new Date(pureTime.getTime() + (distanceDay * DAYTIME) + (hour * HOURTIME) + (minute *
						MINUTETIME));
					return result;
				})
				// 排序，获取第一个最近的时间
				const _this = this;
				const nearTime = allTime.sort((v1, v2) => {
					return v1.getTime() - v2.getTime();
				})[0];

				if (nearTime === undefined) {
					return false;
				}

				// 时间差
				const timeDistance = nearTime.getTime() - this.date.getTime();
				const distanceDay = timeDistance > DAYTIME ? Math.floor(timeDistance / DAYTIME) : 0;
				const distanceHour = Math.floor((timeDistance - distanceDay * DAYTIME) / HOURTIME);
				const distanceMinute = Math.floor((timeDistance - (distanceDay * DAYTIME) - (distanceHour * HOURTIME)) /
					MINUTETIME);
				if (distanceDay === 0) {
					return distanceHour + '小时' + distanceMinute + '分钟后响铃';
				}
				return distanceDay + '天' + distanceHour + '小时' + distanceMinute + '分钟后响铃';
			}
		}
	}
</script>

<style>
	.clock-area-content {
		width: 500rpx;
		height: 500rpx;
		display: flex;
		flex-direction: column;
		justify-content: flex-start;
		align-items: center;
	}

	.clock-area-show {
		position: relative;
		width: 480rpx;
		height: 480rpx;
	}

	.clock-area-outer {
		position: absolute;
		background-color: #C0C0C0;
		width: 100%;
		height: 100%;
		border-radius: 50%;
	}

	.clock-pin {
		position: absolute;
		top: 0;
		left: calc(50% - 4rpx);
		width: 8rpx;
		height: 480rpx;
		background-color: #000000;
	}

	.clock-pin-sec {
		position: absolute;
		top: 0;
		left: calc(50% - 1rpx);
		width: 2rpx;
		height: 480rpx;
		background-color: #000000;
	}

	.clock-area-inner {
		position: absolute;
		top: calc((100% - 92%) / 2);
		left: calc((100% - 92%) / 2);
		z-index: 2;
		border-radius: 50%;
		width: 92%;
		height: 92%;
		background-color: #C0C0C0;
	}

	.clock-pin-num {
		position: absolute;
		top: 0;
		left: calc(50% - 10rpx);
		width: 20rpx;
		height: 100%;
		text-align: center;
	}

	.clock-pin-num-font {
		position: absolute;
		display: inline-block;
		top: 0;
		left: 0;
		width: 100%;
		font-size: 25rpx;
	}

	.clock-area-inner-needle-hour {
		position: absolute;
		top: calc(50% - 140rpx);
		left: calc(50% - 5rpx);
		transform: translateY(5rpx);
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		width: 10rpx;
		height: 280rpx;
		z-index: 3;
	}

	.needle-hour-1 {
		width: 100%;
		height: 145rpx;
		border-radius: 15rpx;
		background-color: #000000;
	}

	.needle-hour-2 {
		width: 100%;
		height: 135rpx;
		background-color: #C0C0C0;
		opacity: .1;
	}

	.clock-area-inner-needle-min {
		position: absolute;
		top: calc(50% - 180rpx);
		left: calc(50% - 5rpx);
		transform: translate(0, 5rpx);
		width: 10rpx;
		height: 360rpx;
		z-index: 4;
	}

	.needle-min-1 {
		width: 100%;
		height: 185rpx;
		border-radius: 15rpx;
		background-color: #000000;
	}

	.needle-min-2 {
		width: 100%;
		height: 175rpx;
		background-color: #C0C0C0;
		opacity: .1;
	}

	.clock-area-inner-needle-sec {
		position: absolute;
		top: calc(50% - 240rpx);
		left: calc(50% - 2.5rpx);
		/* transform: translate(0, 50rpx); */
		width: 5rpx;
		height: 480rpx;
		z-index: 4;
	}

	.needle-sec-1 {
		width: 100%;
		height: 260rpx;
		background-color: #ff0000;
	}

	.needle-sec-2 {
		width: 100%;
		height: 220rpx;
		background-color: #C0C0C0;
		opacity: .1;
	}

	.needle-white-content {
		position: relative;
	}

	.needle-white {
		position: absolute;
		left: 50%;
		transform: translateX(-50%);
		top: 2rpx;
		width: 50%;
		height: 40rpx;
		border-radius: 2rpx;
		background-color: #C0C0C0;
	}

	.clock-area-inner-mini-rotate {
		position: absolute;
		top: calc(50% - 2rpx);
		left: calc(50% - 2rpx);
		width: 4rpx;
		height: 4rpx;
		z-index: 5;
		background-color: #000000;
		border-radius: 50%;
	}

	.clock-area-desc {
		width: 100%;
		height: 20rpx;
		font-size: 30rpx;
		text-align: center;
	}
</style>
