<template>
	<view class="index">
		<!-- 明细 -->
		<details-info v-if="curTab=='details'"></details-info>
		<!-- 添加账单 -->
		<add-bill class="add-bill" @cancel="onCancel" :class="isAddBill?'add-bill-animate':''"></add-bill>
		<view class="tabbar">
			<view class="tabbar-item" @click="switchTab('details')">
				<imgs class="icon" line="tabbar/details" fill="tabbar/details_fill" :curShow="curTab=='details'"></imgs>
				<p class="desc">明细</p>
			</view>
			<view class="tabbar-item" @click="switchTab('chart')">
				<imgs class="icon" line="tabbar/chart" fill="tabbar/chart_fill" :curShow="curTab=='chart'"></imgs>
				<p class="desc">图表</p>
			</view>
			<view class="tabbar-item" @click="isAddBill=true">
				<img class="icon bookkeep" src="@/static/tabbar/bookkeeping.png" mode="widthFix" alt="">
				<p class="desc">记账</p>
			</view>
			<view class="tabbar-item" @click="switchTab('community')">
				<imgs class="icon" line="tabbar/community" fill="tabbar/community_fill" :curShow="curTab=='community'">
				</imgs>
				<p class="desc">社区</p>
			</view>
			<view class="tabbar-item" @click="switchTab('mine')">
				<imgs class="icon" line="tabbar/mine" fill="tabbar/mine_fill" :curShow="curTab=='mine'"></imgs>
				<p class="desc">我的</p>
			</view>
		</view>
	</view>
</template>

<script>
	import detailsInfo from '@/components/detailsInfo.vue';
	import imgs from '@/components/imgs.vue';
	import addBill from '@/components/addBill/index.vue';
	export default {
		components: {
			detailsInfo,
			imgs,
			addBill
		},
		data() {
			return {
				curTab: 'details',
				isAddBill: false,
			}
		},
		onLoad() {

		},
		methods: {
			switchTab(val) {
				this.curTab = val;
			},
			onCancel(){
				this.isAddBill = false;
			}
		}
	}
</script>

<style lang="scss" scoped>
	@import "/style/perfect.css";

	.index {
		height: 100vh;
		position: relative;
		
		.add-bill {
			width: 100%;
			position: absolute;
			top: 100vh;
			transition: 300ms;
			z-index: 10;
			&-animate {
				top: 0;
				transition: 400ms;
			}
		}

		.tabbar {
			height: 10%;
			background-color: #fff;
			border-top: 1px solid #f7f7f7;
			position: fixed;
			bottom: 0;
			left: 0;
			right: 0;
			padding-top: 16rpx;
			box-sizing: border-box;
			display: flex;

			-webkit-animation: slide-in-blurred-top 0.6s cubic-bezier(0.230, 1.000, 0.320, 1.000) both;
			animation: slide-in-blurred-top 0.6s cubic-bezier(0.230, 1.000, 0.320, 1.000) both;

			.tabbar-item {
				width: 20%;
				display: flex;
				flex-direction: column;
				align-items: center;
				position: relative;

				.icon {
					width: 40rpx;

					&.bookkeep {
						width: 120rpx;
						height: 120rpx;
						position: relative;
						top: -80rpx;
					}
				}

				.desc {
					position: absolute;
					top: 50rpx;
					font-size: 20rpx;
					color: #636363;
				}
			}
		}
	}
</style>
