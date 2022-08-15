<template>
	<view class="add-expend">
		<view class="item" v-for="(item,index) in categorys" :key="index" @click="curCategory=item.icon">
			<img class="icon" :src="'../../../static/add-bill/'+item.icon+'.png'" :class="curCategory==item.icon?'active':''" mode="widthFix" >
			<p class="desc">{{item.name}}</p>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				categorys:[
					{icon:'repast',name:'餐饮'},
					{icon:'shopping',name:'购物'},
					{icon:'commodity',name:'日用'},
					{icon:'traffic',name:'交通'},
					{icon:'vegetable',name:'蔬菜'},
					{icon:'fruite',name:'水果'},
					{icon:'snack',name:'零食'},
					{icon:'sport',name:'运动'},
					{icon:'entertainmente',name:'娱乐'},
					{icon:'communicate',name:'通讯'},
					{icon:'dress',name:'服饰'},
					{icon:'beauty',name:'美容'},
					{icon:'house',name:'住房'},
					{icon:'home',name:'居家'},
					{icon:'child',name:'孩子'},
					{icon:'elder',name:'长辈'},
					{icon:'social',name:'社交'},
					{icon:'travel',name:'旅行'},
					{icon:'smoke',name:'烟酒'},
					{icon:'digital',name:'数码'},
					{icon:'car',name:'汽车'},
					{icon:'medical',name:'医疗'},
					{icon:'books',name:'书籍'},
					{icon:'study',name:'学习'},
					{icon:'pet',name:'宠物'},
					{icon:'money',name:'礼金'},
					{icon:'gift',name:'礼物'},
					{icon:'office',name:'办公'},
					{icon:'repair',name:'维修'},
					{icon:'donate',name:'捐赠'},
					{icon:'lottery',name:'彩票'},
					{icon:'friend',name:'亲友'},
					{icon:'express',name:'快递'},
					{icon:'set',name:'设置'}
				],
				curCategory:null,
			};
		}
	}
</script>

<style lang="scss">
.add-expend {
	padding: 20rpx;
	box-sizing: border-box;
	display: flex;
	flex-wrap: wrap;
	height: 100%;
	overflow-y: scroll;
	.item {
		width: 25%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		margin-bottom: 20rpx;
		.icon {
			width: 50%;
			padding: 5rpx;
			background-color: #f6f6f6;
			border-radius: 50%;
			&.active {
				background-color: #f9db61;
			}
		}
		.desc {
			font-size: 24rpx;
			color: #333;
			margin-top: 10rpx;
		}
	}
}
</style>
