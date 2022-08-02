<template>
	<view class="box">
		<img class="line" :src="'../static/'+line+'.png'" >
		<img class="fill" :class="!curShow?'isShow':''" :src="'../static/'+fill+'.png'" >
	</view>
</template>

<script>
	export default {
		data(){
			return {
				
			}
		},
		props:{
			line:{
				type:String,
				default:''
			},
			fill:{
				type:String,
				default:''
			},
			curShow:{
				type:Boolean,
				default:true,
			}
			
		}
	}
</script>

<style lang="scss">
	.box {
		position: relative;
		.line,.fill {
			position: absolute;
			width: 40rpx;
			height: 40rpx;
			&.isShow {
				display: none;
			}
		}
		.line {
			z-index: 10;
		}
	}
</style>