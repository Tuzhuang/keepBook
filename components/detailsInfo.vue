<template>
	<view class="details">
		<view class="top-box">
			<view class="top-tit">
				<view class="status">
					<img class="rest-icon" :src="'../static/details-info/'+curState+'.png'" mode="widthFix">
				</view>
				<view class="operate">
					<img class="search-icon" src="../static/details-info/search.png" mode="widthFix">
					<img class="calendar-icon" src="../static/details-info/calendar.png" mode="widthFix">
				</view>
			</view>
			<view class="info-con">
				<view class="date-time" @click="openDateMonth">
					<p class="year">{{year}}年</p>
					<view class="month">
						<p>{{('0'+month).slice(-2)}}<span class="unit">月</span></p>
						<img class="down-icon" src="../static/details-info/down.png" mode="widthFix">
						<i class="line"></i>
					</view>
				</view>
				<view class="cost-info">
					<view class="income">
						<p class="tit">收入</p>
						<p class="amount" v-show="!isHideAmo">0.<span class="decimal">00</span></p>
						<p class="amount" v-show="isHideAmo">******</p>
					</view>
					<view class="expend">
						<p class="tit">支出</p>
						<p class="amount" v-show="!isHideAmo">147.<span class="decimal">68</span></p>
						<p class="amount" v-show="isHideAmo">******</p>
					</view>
				</view>
				<view @click="isHideAmo=!isHideAmo">
					<img class="eye-icon" v-show="!isHideAmo" src="../static/details-info/open_eye.png" mode="widthFix">
					<img class="eye-icon" v-show="isHideAmo" src="../static/details-info/close_eye.png" mode="widthFix">
				</view>
			</view>
			<view class="sub-menu">
				<view class="menu-item" v-for="(item,index) in  subMenuData" :key="index">
					<imgs class="icon" :line="'details-info/'+item.fill" :fill="'details-info/'+item.line"></imgs>
					<p class="title">{{item.name}}</p>
				</view>
			</view>
		</view>
		<!-- 单元格明细 -->
		<view class="group-detail">
			<view class="tit-con">
				<p class="day">07月27日<span class="week">星期三</span></p>
				<view class="right-con">
					<p>收入：34.59</p>
					<p>支出：6.65</p>
				</view>
			</view>
			<u-swipe-action v-for="(item,index) in amountInfos" :key="index">
				<u-swipe-action-item :options="item.options">
					<view class="swipe-action u-border-top u-border-bottom">
						<view class="swipe-action__content">
							<text class="swipe-action__content__text">{{item.desc}}</text>
						</view>
					</view>
				</u-swipe-action-item>
			</u-swipe-action>
		</view>


		<!-- 年月时间选择弹框 -->
		<u-datetime-picker :show="dateShow" v-model="curDateMonth" mode="year-month" :formatter="formatter"
			confirmColor="#333" @cancel="dateCancel" @confirm="dateConfirm" title="选择月份">
		</u-datetime-picker>
	</view>
</template>

<script>
	import imgs from '@/components/imgs.vue';
	export default {
		components: {
			imgs
		},
		data() {
			return {
				curState: 'work', // 'rest'
				dateShow: false,
				curDateMonth: new Date().getTime(),
				year: '',
				month: '',
				isHideAmo:false, // 是否隐藏金额
				subMenuData: [{
						fill: 'bill',
						line: 'bill_fill',
						name: '账单'
					},
					{
						fill: 'budget',
						line: 'budget_fill',
						name: '预算'
					},
					{
						fill: 'assets',
						line: 'assets_fill',
						name: '资产管家'
					},
					{
						fill: 'saving',
						line: 'saving_fill',
						name: '购物返现'
					},
					{
						fill: 'more',
						line: 'more_fill',
						name: '更多'
					},
				],
				amountInfos: [{
						icon: '',
						desc: '交通',
						options: [{
							text: '删除',
							style: {
								backgroundColor: '#e45656',
							}
						}]
					},
					{
						icon: '',
						desc: '购物',
						options: [{
							text: '删除',
							style: {
								backgroundColor: '#e45656',
							}
						}]
					},
					{
						icon: '',
						desc: '餐饮',
						options: [{
							text: '删除',
							style: {
								backgroundColor: '#e45656',
							}
						}]
					},

				]
			}
		},
		mounted() {
			this.curDateMonth = new Date().getTime();
			this.year = new Date().getFullYear();
			this.month = new Date().getMonth() + 1;
		},
		methods: {
			formatter(type, value) {
				if (type === 'year') {
					return `${value}年`
				}
				if (type === 'month') {
					return `${value}月`
				}
				return value
			},
			openDateMonth() {
				this.curDateMonth = new Date(this.year + '-' + ('0' + this.month).slice(-2)).getTime();
				this.dateShow = true;
			},
			dateConfirm(e) {
				this.curDateMonth = e.value;
				this.year = new Date(this.curDateMonth).getFullYear();
				this.month = new Date(this.curDateMonth).getMonth() + 1;
				this.dateShow = false;
			},
			dateCancel() {
				this.dateShow = false;
			}
		}
	}
</script>

<style lang="scss" scoped>
	@import "/style/perfect.css";

	.details {
		height: 100vh;

		.top-box {
			padding: 20rpx 20rpx 0;
			box-sizing: border-box;
			width: 100%;
			// background-color: #f9db61;
			background-image: linear-gradient(0deg, #fff, #f9db61 20%);

			.top-tit {
				display: flex;
				justify-content: space-between;

				.status {

					.rest-icon,
					.work-icon {
						width: 40rpx;
					}
				}

				.operate {

					.search-icon,
					.calendar-icon {
						width: 40rpx;
					}

					.calendar-icon {
						margin-left: 20rpx;
					}
				}
			}

			.info-con {
				display: flex;
				justify-content: space-between;
				margin-top: 20rpx;
				-webkit-animation: bounce-in-top 1.1s both;
				animation: bounce-in-top 1.1s both;


				.date-time {
					width: 20%;

					.year {
						font-size: 20rpx;
						color: #8a752c;
					}

					.month {
						font-size: 42rpx;
						color: #110d09;
						display: flex;
						align-items: center;
						margin-top: 10rpx;

						.unit {
							font-size: 24rpx;
						}

						.down-icon {
							width: 20rpx;
							margin-left: 10rpx;
							position: relative;
							bottom: -8rpx;
						}

						.line {
							width: 1px;
							height: 40rpx;
							background-color: #d2b750;
							margin-left: 20rpx;
						}
					}
				}

				.cost-info {
					width: 80%;
					display: flex;
					justify-content: space-around;

					.income,
					.expend {
						.tit {
							color: #8a752c;
							font-size: 20rpx;
						}

						.amount {
							color: #110d09;
							font-size: 36rpx;
							margin-top: 10rpx;

							.decimal {
								font-size: 24rpx;
							}
						}
					}
				}

				.eye-icon {
					width: 30rpx;
					position: relative;
					right: 6rpx;
				}
			}

			.sub-menu {
				width: 100%;
				background-color: #fff;
				border-radius: 20rpx;
				margin: 20rpx auto;
				padding: 20rpx 0;
				box-sizing: border-box;
				display: flex;
				justify-content: space-around;
				box-shadow: #ddd 0rpx 2rpx 11rpx -1rpx;
				-webkit-animation: slide-in-right 0.4s cubic-bezier(0.250, 0.460, 0.450, 0.940) both;
				animation: slide-in-right 0.4s cubic-bezier(0.250, 0.460, 0.450, 0.940) both;

				.menu-item {
					display: flex;
					flex-direction: column;
					justify-content: center;
					align-items: center;

					.icon {
						width: 40rpx;
						height: 50rpx;
					}

					.title {
						font-size: 20rpx;
						color: #666666;
					}
				}
			}
		}

		.group-detail {
			.tit-con {
				height: 60rpx;
				display: flex;
				justify-content: space-between;
				align-items: center;
				font-size: 20rpx;
				padding: 0 20rpx;
				box-sizing: border-box;
				color: #b1b1b1;
				border-bottom: 1rpx solid #dadbde;

				.day {
					.week {
						margin-left: 12rpx;
					}
				}

				.right-con {
					display: flex;

					p:last-child {
						margin-left: 20rpx;
					}
				}
			}

			.swipe-action {
				border-top: none;

				&__content {
					padding: 25rpx 0;

					&__text {
						font-size: 28rpx;
						color: #424242;
						// padding-left: 30rpx;
					}
				}
			}
		}



	}
</style>
