<template>
	<view class="mine">
		<u-button @click="goToEat" type="primary" text="我来告诉你今天吃什么"></u-button>
		<u-button @click="toRecipe" type="success" text="我来告诉你今天做什么"></u-button>
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
			},
			toRecipe(){
				uni.navigateTo({
					url:'/pages/recipe/index'
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
