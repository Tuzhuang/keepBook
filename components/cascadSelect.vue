<template>
	<view class="cascad-select">
		<view class="menu-con" v-if="mainMenuList.length" :style="{height:mainMenuList.length * 40+'px'}">
			<view class="main-menu">
				<view class="item" 
				:class="[curMainMenu==item.value?'active':'',
				curMainMenuI-index===1?'prev':'',
				curMainMenuI-index===-1?'next':'']"
				v-for="(item,index) in mainMenuList" :key="index"
				@click="onMainItem(item.value,index)">
					<p class="label" 
					:style="{borderTopLeftRadius:curMainMenuI===index&&curMainMenuI===0?'inherit':'',
					borderBottomLeftRadius:curMainMenuI===index&&curMainMenuI===mainMenuList.length-1?'inherit':''}"
					>{{item.label}}</p>
				</view>
			</view>
			<view class="right-con">
				<view class="sub-menu">
					<view class="sub-tag" v-for="(item,index) in curSubMenus" :key="index">
						<u-tag :text="item.label" @click="dinTypeClick(item)" :plainFill="selSubItems.some(val=>val.code===item.value)"
						  plain size="mini" type="error"></u-tag>
					</view>
				</view>
				<view class="btn-con">
					<view class="empty">
						<u-button type="error" @click="empty" text="清空" size="mini" shape="circle"></u-button>
					</view>
					<view class="confirm">
						<u-button type="primary" @click="submit" text="确定" size="mini" shape="circle"></u-button>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				curMainMenu:'050100', // 当前主菜单
				curMainMenuI:0, // 当前选中的主菜单下标
				curSubMenus:[],
				mainMenuList:[],
				selSubItems:[],
				tempSelSubItems:[], // 临时选中的菜系，如果不点击确定的话就会清空该数组
			};
		},
		props:{
			jsonData:{
				type:Array,
				default:function(){
					return [];
				}
			},
			curPoiTypes:{
				type:Array,
				default:function(){
					return [];
				}
			}
		},
		watch:{
			curPoiTypes(newVal){
				console.log('newVal',newVal);
			}
		},
		created(){
			this.processData();
			// 默认展示第一大类别
			this.onMainItem(this.curMainMenu,0);
			// this.selSubItems = this.curPoiTypes;
			console.log('00',this.curPoiTypes)
		},
		methods:{
			processData(){
				let mainOtherList = [];
				this.jsonData.forEach(item=>{
					if(item.children){
						this.mainMenuList.push(item);
					}else {
						mainOtherList.push(item);
					}
				})
				this.mainMenuList.push({label:'其他',value:'',children:mainOtherList});
			},
			onMainItem(value,index){
				this.curMainMenu = value;
				this.curMainMenuI = index;
				this.curSubMenus = this.mainMenuList.map(it=>{
					if(it.value === this.curMainMenu && it.children){
						return it.children;
					}
				}).filter(val=>val!==undefined)[0];
			},
			dinTypeClick(item){
				// 选中每一项的时候 判断数组里如果存在该项的时候就删除，如果不存在的话就添加
				if(this.selSubItems.some(val=>val.code===item.value)){
					this.selSubItems.splice(this.selSubItems.findIndex(it=>it.code===item.value),1);
					return;
				}
				// 给临时存放数据的数组赋值 ----
				this.selSubItems.unshift({key:item.label,code:item.value});
				console.log(this.selSubItems)
			},
			// 给父组件传值
			submit(){
				// this.selSubItems = JSON.parse(JSON.stringify(this.tempSelSubItems));
				this.$emit('selSubItems',this.selSubItems.concat(this.tempSelSubItems));
				// this.tempSelSubItems = [];
			},
			empty(){
				this.selSubItems = [];
			}
		}
	}
</script>

<style lang="scss">
.cascad-select {
	.menu-con {
		display: flex;
		.main-menu {
			min-width: 200rpx;
			.item{
				height: 80rpx;
				text-align: center;
				line-height: 80rpx;
				background-color: #f1f1f1;
				// background-color: pink;
				font-size: 24rpx;
				&.active {
					font-weight: 600;
					overflow: hidden;
					.label {
						background-color: #fff;
						border-top-left-radius: 20rpx;
						border-bottom-left-radius: 20rpx;
					}
				}
				&.prev {
					border-bottom-right-radius: 30rpx;
				}
				&.next {
					border-top-right-radius: 30rpx;
				}
			}
		}
		.right-con {
			flex: 1;
			height: 100%;
			display: flex;
			flex-direction: column;
			.sub-menu {
				padding: 10rpx;
				box-sizing: border-box;
				height: 80%;
				overflow-y: scroll;
				.sub-tag {
					display: inline-block;
					margin-right: 20rpx;
					margin-bottom: 16rpx;
				}
			}
			.btn-con {
				flex: 1;
				display: flex;
				align-items: center;
				justify-content: flex-end;
				padding-right: 20rpx;
				.confirm {
					margin-left: 20rpx;
				}
			}
			
		}
	}
}
</style>
