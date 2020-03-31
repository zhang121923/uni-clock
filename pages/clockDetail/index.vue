<template>
	<view class="clock-add-content">
		<view class="time-selector">
			<picker-view :indicator-style="indicatorStyle" :value="time" @change="bindChange">
				<picker-view-column>
					<view class="item" v-for="(item,index) in hours" :key="index">
						<text class="item-number">{{item | timeStrFilter}}</text>
					</view>
				</picker-view-column>
				<picker-view-column>
					<view class="item" v-for="(item,index) in minutes" :key="index">
						<text class="item-number">{{item | timeStrFilter}}</text>
					</view>
				</picker-view-column>
			</picker-view>
		</view>
		<view class="input-area">
			<view class="uni-form-item uni-input">
				<view class="item-title">闹钟名</view>
				<view @click="toggleInput(true)">
					<text class="item-value">{{clockDetailData.name}}</text>>
				</view>
			</view>
			<view class="uni-form-item uni-column">
				<view class="item-title">重复</view>
				<view @click="toggleRepeat(true)">
					<text class="item-value">{{clockDetailData.repeat | repeatFormat}}</text>>
				</view>
			</view>
			<view class="uni-form-item uni-column">
				<view class="item-title">铃声</view>
				<view @click="selectClockMusic()">
					<text class="item-value">{{clockDetailData.music | musicNameFilter}}</text>>
				</view>
			</view>
			<uniMultipleSelector v-if="showRepeatSelect" :selectedRepeat="clockDetailData.repeat" @repeat-confirm="repeatChange"
			 @toggle-repeat="toggleRepeat"></uniMultipleSelector>
			<customInput v-if="showNameInput" :name="clockDetailData.name" @toggle-input="toggleInput" @input-confirm="setClockName"></customInput>
			<tki-file-manager ref="filemanager" @result="setClockMusic"></tki-file-manager>
		</view>
		<view class="submit-btn">
			<button form-type="submit" @tap="confirmClock()">确认</button>
		</view>
	</view>
</template>

<script>
	import uniMultipleSelector from '../../components/multipleSelector/index.vue';
	import customInput from '../../components/customInput/index.vue';
	import tkiFileManager from '../../components/tki-file-manager/tki-file-manager.vue'
	export default {
		components: {
			uniMultipleSelector,
			customInput,
			tkiFileManager
		},
		created: function() {
			// 加载内存中的闹钟详情值，如果无该值就是新建
			let value = uni.getStorageSync('clockDetailData');
			if (value !== null) {
				uni.setNavigationBarTitle({
					title: '编辑闹钟'
				});
			}
			const date = new Date();
			this.clockDetailData = value || {
				name: '闹钟',
				time: `${date.getHours()}:${date.getMinutes()}`,
				repeat: [],
				music: '仅震动',
				isNo: true
			};
		},
		data: function() {
			const clockDetailData = {};
			const hours = [];
			const minutes = [];
			const weeks = [];
			for (let i = 0; i <= 23; i++) {
				hours.push(i)
			}
			for (let i = 0; i <= 59; i++) {
				minutes.push(i)
			}
			for (let i = 0; i <= 6; i++) {
				weeks.push(i)
			}
			return {
				hours,
				minutes,
				weeks,
				clockDetailData,
				indicatorStyle: `height: ${Math.round(uni.getSystemInfoSync().screenWidth/(750/100))}px;`,
				showRepeatSelect: false,
				showNameInput: false
			}
		},
		methods: {
			bindChange: function(e) {
				const val = e.detail.value;
				this.hour = this.hours[val[0]];
				this.minute = this.minutes[val[1]];
				this.clockDetailData = Object.assign({}, this.clockDetailData, {
					time: this.hour + ':' + this.minute
				});
			},
			repeatChange: function(value) {
				this.clockDetailData = Object.assign({}, this.clockDetailData, {
					repeat: value
				});
				this.toggleRepeat(false);
			},
			toggleRepeat: function(value) {
				this.showRepeatSelect = value;
			},
			toggleInput: function(value) {
				this.showNameInput = value;
			},
			setClockName: function(value) {
				this.clockDetailData = Object.assign({}, this.clockDetailData, {
					name: value
				});
				this.toggleInput(false);
			},
			selectClockMusic: function() {
				this.$refs.filemanager._openFile();
			},
			setClockMusic: function(e) {
				this.clockDetailData = Object.assign({}, this.clockDetailData, {
					music: e + ''
				});
			},
			confirmClock: function() {
				let preClockData = uni.getStorageSync('clockListData');
				if (preClockData) {
					// 按名字筛选
					let exsitIndex = -1;
					preClockData.forEach((v, i) => {
						if (v.name === this.clockDetailData.name) {
							exsitIndex = i;
							return false;
						}
					});
					if (exsitIndex === -1) {
						// 新增
						preClockData.push(Object.assign({}, this.clockDetailData, {
							id: preClockData.length + 1
						}))
					} else {
						if (!this.clockDetailData.id) {
							uni.showToast({
								title: '已存在该名称的闹钟，请更换闹钟名！',
								icon: 'none'
							})
							return false;
						}
						// 修改
						preClockData.splice(exsitIndex, 1, Object.assign({}, this.clockDetailData));
					}
					uni.setStorageSync('clockListData', preClockData);
				} else {
					uni.setStorageSync('clockListData', [Object.assign({}, this.clockDetailData, {
						id: 1
					})]);
				}
				// 将内存数据清除
				uni.setStorageSync('clockDetailData', null);
				// 跳转
				uni.navigateBack();
			}
		},
		computed: {
			time: function() {
				return this.clockDetailData.time.split(':').map(v => {
					return v - 0;
				});
			}
		},
		filters: {
			timeStrFilter: function(value) {
				return value < 10 ? `0${value}` : value + '';
			},
			repeatFormat: function(value) {
				value = value || [];
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
			musicNameFilter: function(value) {
				value = value || '';
				if (value === '仅震动') {
					return value;
				}
				const name = value.substring(value.lastIndexOf('/') + 1, value.lastIndexOf('.'));
				return name.length > 10 ? name.substr(0, 10) + '...' : name;
			}
		}
	}
</script>

<style>
	.clock-add-content {
		display: flex;
		flex-direction: column;
		justify-content: flex-start;
		align-items: center;
		width: 100%;
		height: 100%;
	}

	.time-selector {
		width: 70%;
		height: 500rpx;
	}

	uni-picker-view {
		width: 100%;
		height: 100%;
	}

	.uni-form-item {
		height: 50rpx;
		margin-top: 20rpx;
		padding-top: 10rpx;
		padding-bottom: 10rpx;
		border-bottom: 1px solid rgba(192, 192, 192, .3);
	}

	.item {
		position: relative;
	}

	.item-number {
		position: absolute;
		left: 50%;
		top: 50%;
		transform: translate(-50%, -50%);
	}

	.input-area {
		width: 90%;
		padding-left: 10rpx;
		padding-right: 10rpx;
	}

	.uni-form-item {
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		align-items: center;
	}

	.item-value {
		font-size: 30rpx;
		font-weight: 300;
	}

	.submit-btn {
		position: absolute;
		width: 100%;
		bottom: 0;
	}
</style>
