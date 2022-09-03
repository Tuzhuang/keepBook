<template>
	<view class="eat-what">
		<view class="query-cond">
			<view class="query-fancy">
				<p class="label">地点</p>
				<u-input v-model="locatVal" @change="getLocation" @focus="getLocation" placeholder="Please enter your location">
					<template slot="suffix" @click="position">
						<u-icon name="map-fill" color="#19be6b"></u-icon>
					</template>
				</u-input>
				<view class="fancy-con" v-if="isRecord && locatVal">
					<view class="record-item" @click="selRecord(item)" v-for="(item,index) in recordList" :key="index">
						<p class="title">{{item.name}}</p>
						<p class="detail-address">{{item.address}}</p>
					</view>
				</view>
			</view>
			<view class="din-type">
				<p class="label">餐厅分类</p>
				<u-input v-model="dinType" readonly border="surround" color="#333" placeholder="选择你中意的餐厅"></u-input>
				<!-- 餐厅分类占位标签 可供点击使用 -->
				<view class="type-holder" @click="openDinType"></view>
			</view>
			<p class="label">范围(米)</p>
			<u-slider v-model="range" @change="rangeChange" showValue step="200" min="200" max="3000"></u-slider>
		</view>
		<view class="din-room" v-if="dinRoomList.length">
			<u-checkbox-group v-model="likeDinRoom" placement="column">
				<u-checkbox :customStyle="{marginBottom: '8px'}" v-for="(item, index) in dinRoomList"
					 :key="index" :label="item.name" :name="item.id">
				</u-checkbox>
			</u-checkbox-group>
			<u-loadmore v-if="dinRoomList.length" :status="loadStatus" @loadmore="onLoadmore" loadmoreText="点击加载更多" />
		</view>
		<view v-else class="not-data">{{dinTypeNone?'暂无收录该分类的餐厅，换个试试~':''}}</view>
		<view class="btn">
			<u-button type="error" @click="popupShow=true;" hairline plain shape="circle">算了，不吃了</u-button>
			<u-button type="primary" @click="submit" shape="circle">选择困难 退退 🤺</u-button>
		</view>
		<view class="cur-food">
			<p class="din-name">{{curDinRoom.name}}</p>
			<p class="din-address">{{curDinRoom.address}}</p>
		</view>
		<!-- 离开时弹出层 -->
		<u-popup :show="popupShow" mode="center" @close="closePopup">
			<u-image :src="src" showLoading width="200px" fade duration="450" height="250px" ></u-image>
		</u-popup>
		<!-- 餐厅分类弹出层 -->
		<u-popup :show="dinTypeShow" class="din-type-popup" mode="top" :round="10" 
			@close="dinTypeShow=false" :safeAreaInsetBottom="false">
			<cascad-select :jsonData="mapSurround.dinTypes" @selSubItems="getSelSubItems" />
		</u-popup>
		<!-- 提示框 -->
		<u-notify ref="uNotify"></u-notify>
	</view>
</template>

<script>
	import mapDataJson from '@/utils/surround.js';
	import cascadSelect from '@/components/cascadSelect.vue';
	export default {
		components:{
			cascadSelect
		},
		data() {
			return {
				mapSurround: mapDataJson,
				locatVal: '',
				dinType:"",
				range: 600, // 范围
				recordList: [],
				isRecord: false,
				dinTypeShow:false,
				curLocation:'', // 当前定位坐标
				dinRoomList:[], // 餐厅列表
				likeDinRoom:[],
				loadStatus:'loadmore', // 加载更多状态 loading / nomore
				curPage:1,
				poiTypes:['050000'], // 附近POI类型
				curDinRoom:{},
				popupShow:false,
				localTimerId:null,
				dinTypeNone:false, // 当前搜索的分类暂无数据
				src:'https://s1.ax1x.com/2022/08/17/v0vGFJ.jpg',
				baseUrl:'https://restapi.amap.com/v3',
				key:'d86bbefe9b11825f0e015dc4dae2eac0', // 请求key
			};
		},
		created(){
		},
		methods: {
			position(){
				uni.request({
					url:'http://pv.sohu.com/cityjson?ie=utf-8',
					method:'GET',
					success:res=>{
						// console.log('res',res.data.returnCitySN)
						let returnCitySN = res.data;
						console.log(JSON.parse(returnCitySN.replace(returnCitySN.split('{')[0],'').replace(';','')));
						let ipconfigObj = JSON.parse(returnCitySN.replace(returnCitySN.split('{')[0],'').replace(';',''));
						this.locatVal = ipconfigObj.cname;
						uni.request({
							url:`${this.baseUrl}/ip?key=${this.key}&ip=${ipconfigObj.cip}`,
							method:'POST',
							success:resp=>{
								console.log('resp',resp)
							}
						})
					}
				})
				this.$refs.uNotify.show({
					top: 0,
					message:"当前定位信息可能存在误差~",
					type: 'warning',
					color:'#fff',
					duration: 1000 * 2,
					bgColor:'#71d5a1'
			  })
			},
			// 获取附近地址
			getLocation() {
				if(!this.locatVal) return;
				// 简单的防抖，首先先清除上一次的计时器
				clearTimeout(this.localTimerId);
				this.localTimerId = setTimeout(()=>{
					uni.request({
						url: `${this.baseUrl}/place/text?key=${this.key}&keywords=${this.locatVal}`,
						method: 'GET',
						success: (res) => {
							if (res.data.pois.length) {
								this.isRecord = true;
								this.recordList = res.data.pois.map(item => {
									return {
										name: item.name,
										address: item.pname + item.cityname + item.adname + item.address,
										location: item.location,
									}
								})
							}
						}
					});
				},200);
			},
			// 选择某一个地理位置
			selRecord(val){
				this.locatVal = val.name;
				this.isRecord = false;
				this.curLocation = val.location;
				// 先清空之前的餐厅数据
				this.likeDinRoom = [];
				this.getDinRoom();
			},
			openDinType(){
				if(!this.locatVal.length) {
					this.$refs.uNotify.show({
						top: 0,
						message:"请先输入定位地点~",
						type: 'warning',
						color:'#fff',
						duration: 1000 * 2,
						bgColor:'#fcbd71'
					})
					return;
				};
				this.dinTypeShow = true;
			},
			// 选择范围
			rangeChange(val){
				this.range = val;
				this.dinRoomList = [];
				if(this.curLocation && this.poiTypes){
					this.getDinRoom();
				}
			},
			// 获取附近餐厅
			getDinRoom(){
				uni.request({
					url: `${this.baseUrl}/place/around?key=${this.key}&location=${this.curLocation}&types=${this.poiTypes.join('|')}&radius=${this.range}&offset=21&page=${this.curPage}`,
					method: 'GET',
					success: (res) => {
						this.loadStatus = 'nomore';
						if (res.data.pois.length) {
							// 如果有分页的话合并之前的数据
							let dinRoom = res.data.pois.map(item=>{
								return {
									name: item.name,
									address: item.adname + item.address,
									id:item.id
								}
							});
							this.dinRoomList = this.dinRoomList.concat(dinRoom);
							let likeDin = res.data.pois.map(it=>it.id);
							this.likeDinRoom = this.likeDinRoom.concat(likeDin);
							// 判断如果当前数据等于20条的时候说明有可能还存在分页数据
							if(res.data.pois.length>=20){
								this.loadStatus = 'loadmore';
							}
						}else {
							this.dinTypeNone = true;
							this.dinRoomList = res.data.pois.map(item=>{
								return {
									name: item.name,
									address: item.adname + item.address,
									id:item.id
								}
							});
						}
					}
				});
			},
			// 加载更多
			onLoadmore(){
				this.loadStatus = 'loading';
				this.curPage++;
				this.getDinRoom();
			},
			submit(){
				if(!this.likeDinRoom.length){
					this.$refs.uNotify.show({
						top: 0,
						message:"你还没有选择你喜欢的餐厅呢~",
						type: 'warning',
						color:'#fff',
						duration: 1000 * 2,
						bgColor:'#fcbd71'
					})
					return;
				}
				let random;
				let timerId = setInterval(()=>{
					random = Math.floor(Math.random() * this.likeDinRoom.length);
					this.curDinRoom = this.dinRoomList[random];
				},50);
				setTimeout(()=>{
					clearInterval(timerId)
				},2000);
			},
			closePopup(){
				this.popupShow = false;
				uni.navigateBack();
			},
			getSelSubItems(val){
				console.log('传过来的值',val);
				this.dinTypeShow = false;
				this.dinType = val.map(it=>it.key).join('、');
				this.poiTypes = val.map(it=>it.code);
				this.getDinRoom();
			}
		}
	}
</script>

<style lang="scss">
	.eat-what {
		padding: 0 20rpx;
		box-sizing: border-box;

		.query-cond {
			.query-fancy {
				margin-bottom: 40rpx;

				.fancy-con {
					max-height: 400rpx;
					background-color: #f4f4f5;
					padding: 10rpx 20rpx;
					box-sizing: border-box;
					overflow-y: scroll;

					.record-item {
						font-size: 20rpx;
						height: 78rpx;
						color: #333;

						.title {
							font-size: 30rpx;
						}

						.detail-address {
							line-height: 32rpx;
							overflow: hidden;
							white-space: nowrap;
							text-overflow: ellipsis;
						}
					}
				}
			}
			.din-type {
				position: relative;
				margin-bottom: 40rpx;
				.type-holder {
					width: 100%;
					height: 70rpx;
					position: absolute;
					top: 49rpx;
					z-index: 10;
				}
			}

			.label {
				font-size: 28rpx;
				color: #999;
				margin-bottom: 10rpx;
			}
			
		}
		.din-room {
			margin-top: 40rpx;
			padding-left: 20rpx;
			max-height: 600rpx;
			overflow-y: scroll;
		}
		.not-data {
			text-align: center;
			margin-top: 100rpx;
			color: #999;
		}

		.btn {
			display: flex;
			justify-content: space-between;
			position: absolute;
			bottom: 220rpx;
			left: 0;
			right: 0;

			.u-button {
				width: 32%;

				&:nth-child(2) {
					width: 58%;
				}
			}
		}
		.cur-food {
			height: 200rpx;
			position: absolute;
			bottom: 0;
			left: 0;
			right: 0;
			text-align: center;
			.din-name {
				font-size: 42rpx;
				color: #333;
			}
			.din-address {
				font-size: 24rpx;
				color: #999;
				line-height: 50rpx;
			}
		}
		/deep/.u-popup__content {
			height: 250px;
		}
		.din-type-popup {
			height: 500rpx;
		}
	}
</style>
