<template>
	<view class="add-bill">
		<!-- <p @click="cancel">取消</p> -->
		<view class="tabs">
			<view class="tabs-tit">
				<!-- <view class="tabs-item" @click="curTab='expend'" :class="curTab=='expend'?'active':''">支出</view>
				<view class="tabs-item" @click="curTab='income'" :class="curTab=='income'?'active':''">收入</view> -->
				<u-tabs class="bill-tabs" :list="tabsList" @click="click">
					<view slot="right">
						<p class="cancel" @click="cancel">取消</p>
					</view>
				</u-tabs>
			</view>
			<view class="tabs-con">
				<swiper class="swiper-wrapper">
					<swiper-item class="swiper-wrapper-item">
						<add-expend></add-expend>
					</swiper-item>
					<swiper-item class="swiper-wrapper-item">
						<add-income></add-income>
					</swiper-item>
				</swiper>
			</view>
		</view>
	</view>
</template>

<script>
	import addExpend from '@/components/addBill/components/addExpend.vue';
	import addIncome from '@/components/addBill/components/addIncome.vue';
	export default {
		name:"addBill",
		components:{
			addExpend,
			addIncome
		},
		data() {
			return {
				tabsList:[
					{name:'支出'},
					{name:'收入'}
				],
				curTab:'expend'
			};
		},
		methods:{
			cancel(){
				this.$emit('cancel');
			},
			click(val){
				console.log(val)
			}
		}
	}
</script>

<style lang="scss">
.add-bill {
	height: 100vh;
	background-color: #fff;
	// overflow-y: scroll;
	.tabs {
		.tabs-tit {
			width: 100%;
			height: 88rpx;
			// background-color: #f9db61;
			background-color: white;
			// display: flex;
			// justify-content: center;
			// align-items: center;
			// position: relative;
			// .tabs-item {
			// 	font-size: 32rpx;
			// 	height: 99%;
			// 	line-height: 60rpx;
			// 	padding: 0 10rpx;
			// 	&:nth-child(2) {
			// 		margin-left: 20rpx;
			// 	}
			// 	&.active {
			// 		border-bottom: 2rpx solid #333;
			// 		transition: 300ms;
			// 	}
			// }
			.bill-tabs {
				// display: flex;
				// align-items: center;
				// justify-content: center;
				::v-deep .u-tabs__wrapper__scroll-view {
					align-items: center;
				}
			}
			.cancel {
				font-size: 24rpx;
				color: #333;
				padding-right: 40rpx;
				box-sizing: border-box;
				// position: absolute;
				// right: 40rpx;
			}
		}
		.tabs-con {
			height: calc(100vh - 88rpx);
			// background-color: #ccc;
			.swiper-wrapper {
				width: 100%;
				height: 100%;
				.swiper-wrapper-item {
					width: 100%;
					height: 100%;
					// background-color: pink;
				}
			}
		}
	}
}
</style>