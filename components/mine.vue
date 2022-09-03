<template>
	<view class="mine">
			<view class="eat-what" @click="goToEat">
				今天吃什么
			</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			};
		},
		methods:{
			goToEat(){
				uni.navigateTo({
					url:'/pages/eatWhat/index'
				})
			}
		}
	}
</script>

<style lang="scss">
.mine {
	height: 100vh;
	.eat-what {
		width: 200rpx;
		height: 100rpx;
		border: 1rpx solid red;
		margin: 400rpx auto;
	}
}
</style>
