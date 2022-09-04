<template>
	<view class="recipe">
		<view class="input-con" v-if="!isResult">
			<view class="vegetable">
				<u-image :src="'/static/recipe/'+vageTables[curVageI]+'.svg'" :showLoading="false" width="120px" height="120px"></u-image>
				<!-- <text class="label">猜猜这个是什么蔬菜！</text> -->
			</view>
			<view class="val">
				<u-input placeholder="想做什么就查什么!" border="surround" v-model="recipeName" @confirm="search">
					<template slot="suffix">
						<!-- <u-icon @click="search" name="fingerprint"></u-icon> -->
						<p @click="search" style="color: #c0ae7d;">搜索</p>
					</template>
				</u-input>				
			</view>
			<p class="desc-tit">如果你想做红烧鱼，那么你就搜索红烧鱼。</p>
		</view>
		<view class="result" v-else>
			<u-loading-page :image="'/static/recipe/'+vageTables[curVageI]+'.svg'"></u-loading-page>
			<view class="banner">
				<u-image :showLoading="true" loadingIcon="eye" :src="information.foodpic" fade width="100%" @click="click"></u-image>
			</view>
			<p class="title">{{information.name}}</p>
			<view class="result-con">
				<u-cell-group>
					<u-cell title="烹饪方式" :value="information.technology"></u-cell>
					<u-cell title="风味" :value="information.taste"></u-cell>
					<u-cell title="烹饪时间" :value="information.cooking_time"></u-cell>
					<u-cell title="份量" :value="information.amount"></u-cell>
				</u-cell-group>
				<p class="tit">主料</p>
				<u-cell-group>
					<u-cell :title="information.main_ingredients.maining_redient_title" :value="information.main_ingredients.maining_redient_amount"></u-cell>
				</u-cell-group>
				<p class="tit">配料</p>
				<u-cell-group>
					<block v-for="(item,index) in ingredients" :key="index">
						<u-cell :title="item.name" :value="item.quantity"></u-cell>
					</block>
				</u-cell-group>
				<p class="tit">做法<span class="desc">（共{{stepCont.length}}步）</span></p>
				<view class="step-item" v-for="(item,index) in stepCont" :key="index">
					<p class="step-name">{{index+1}}. {{item.title}}</p>
					<p class="step-desc">{{item.content}}</p>
					<u-image :showLoading="true" :src="item.pic" width="100%" radius="6" fade></u-image>
				</view>	
				<p class="thanks">谢谢观看~</p>
			</view>
		</view>
		<!-- 提示框 -->
		<u-notify ref="uNotify"></u-notify>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				recipeName:'', // 食谱名称
				vageTables:[
					'baicai',
					'cong',
					'dasuan',
					'huacai',
					'huluobo',
					'jiang',
					'lajiao',
					'lianou',
					'luobo',
					'nangua',
					'qiezi',
					'qincai',
					'sigua',
					'tudou',
					'wandou',
					'xianggu',
					'xihongshi',
					'yangcong',
					'zhusun'
				], // 蔬菜种类
				curVageI:0, // 当前蔬菜下标
				vageTimerId:null,
				isResult:false,
				information:{}, // 食谱信息
				ingredients:[], // 配料
				stepCont:[], // 步骤
				baseUrl:'https://qqlykm.cn/api',
				key:'iTaNXunoCHhu8lAVJpldNBwxnx', // 请求key
			};
		},
		created(){
			this.getCurVageTable();
			// this.getRecipe();
		},
		methods:{
			// 获取当前展示的蔬菜
			getCurVageTable(){
				this.vageTimerId = setInterval(()=>{
					this.curVageI = Math.floor(Math.random() * this.vageTables.length);
				},5000)
			},
			getRecipe(){
				uni.request({
					url: `${this.baseUrl}/recipe/get?key=${this.key}&name=${this.recipeName}`,
					method: 'GET',
					success: (res) => {
						if(res.statusCode == 200 && res.data.accessories){
							this.isResult = true;
							this.information = res.data.information;
							this.ingredients = res.data.accessories;
							this.stepCont = res.data.cooksteps;
						}else {
							// 增加随机性
							let flag = new Date().getTime() % 2 == 0;
							this.$refs.uNotify.show({
								top: 0,
								message:flag?"如果你喜欢的话，我现在就学习这道菜！":"你选的这道菜真的会有人喜欢吃嘛？",
								type: 'warning',
								color:'#fff',
								duration: 1000 * 2,
							})
						}
					}
				});
			},
			search(){
				if(!this.recipeName){
					this.$refs.uNotify.show({
						top: 0,
						message:"你没输入我怎么查！",
						type: 'warning',
						color:'#fff',
						duration: 1000 * 2,
					})
					return;
				}
				this.getRecipe();
			}
		}
	}
</script>

<style lang="scss">
.recipe {
	.input-con {
		height: 100vh;
		position: relative;
		.val {
			position: absolute;
			bottom: 600rpx;
			left: 20rpx;
			right: 20rpx;
		}
		.vegetable {
			display: flex;
			flex-direction: column;
			align-items: center;
			position: absolute;
			top: 200rpx;
			left: 0;
			right: 0;
			animation: fadeShow 5s infinite;
			.label {
				color: #333;
				font-size: 32rpx;
			}
		}
		.desc-tit {
			color: #c0ae7d;
			position: absolute;
			bottom: 200rpx;
			left: 0;
			right: 0;
			text-align: center;
		}
	}
	.result {
		height: 100vh;
		.title {
			font-size: 38rpx;
			font-weight: 600;
			text-align: center;
			color: #c0ae7d;
			margin: 20rpx;
		}
		.result-con {
			padding: 0 30rpx 50rpx;
			box-sizing: border-box;
			.tit {
				font-size: 26rpx;
				font-weight: 600;
				line-height: 80rpx;
				color: #c0ae7d;
				margin-top: 20rpx;
				.desc {
					font-size: 20rpx;
					color: #666;
					font-weight: normal;
				}
			}
			.step-item {
				font-size: 24rpx;
				color: #333;
				line-height: 40rpx;
				margin-bottom: 20rpx;
				.step-name {
					font-size: 22rpx;
					font-weight: 600;
				}
				.step-desc {
					margin-bottom: 10rpx;
				}
			}
			.thanks {
				font-size: 22rpx;
				margin-top: 50rpx;
				text-align: center;
				color: #c0ae7d;
			}
		}
	}
	@keyframes fadeShow {
		0% {
			transform: scale(0.8);
			opacity: 0;
		}
		50% {
			transform: scale(1.2);
			opacity: 1;
		}
		100% {
			transform: scale(0.8);
			opacity: 0;
		}
	}
}
</style>
