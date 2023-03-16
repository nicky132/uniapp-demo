<template>
	<view class="goods-item">
		<!-- 商品左侧图片区域 -->
		<view class="goods-item-left">
			<radio v-if="showRadio" :checked="goods.goods_state" color="#c00000" @click="radioClickHandler"></radio>
			<image :src="goods.goods_small_logo || defaultPic" class="goods-pic"></image>
		</view>
		<!-- 商品右侧信息区域 -->
		<view class="goods-item-right">
			<!-- 商品标题 -->
			<view class="goods-name">{{goods.goods_name}}</view>
			<view class="goods-info-box">
				<!-- 商品价格 -->
				<view class="goods-price">￥{{goods.goods_price | tofixed}}</view>
				<!-- 商品数量 -->
				<uni-number-box :min="1" :value="goods.goods_count" v-if="showNum" @change="numChangeHandler">
				</uni-number-box>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
			// 商品的信息对象
			goods: {
				type: Object,
				default: {}
			},
			// 是否显示左边的 radio 
			showRadio: {
				type: Boolean,
				default: false
			},
			// 是否显示商品数量
			showNum: {
				type: Boolean,
				default: false
			}
		},
		data() {
			return {
				// 默认的空图片
				defaultPic: 'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png'
			};
		},
		filters: {
			// 把数字处理为带两位小数的数字
			tofixed(num) {
				return Number(num).toFixed(2)
			}
		},
		onLoad() {
			this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
		},
		methods: {
			radioClickHandler() {
				// console.log(this)
				this.$emit('radiochange', {
					// id
					goods_id: this.goods.goods_id,
					// 商品最新勾选状态
					goods_state: !this.goods.goods_state
				})
			},
			// NumberBox 组件的 change 事件处理函数
			numChangeHandler(e) {
				// 通过 this.$emit() 触发外界通过 @ 绑定的 num-change 事件
				// console.log(e)
				this.$emit('num-change', {
					// id
					goods_id: this.goods.goods_id,
					// 商品的数量值
					goods_count: e
				})
			}
		}
	}
</script>
<style lang="scss">
	.goods-item {
		// 让 goods-item 项占满整个屏幕的宽度
		width: 750rpx;
		// 设置盒模型为 border-box
		box-sizing: border-box;
		display: flex;
		padding: 10px 5px;
		border-bottom: 1px solid #f0f0f0;

		.goods-item-left {
			margin-right: 5px;
			display: flex;
			justify-content: space-between;
			align-items: center;

			.goods-pic {
				width: 100px;
				height: 100px;
				display: block;
			}
		}

		.goods-item-right {
			display: flex;
			flex: 1;
			flex-direction: column;
			justify-content: space-between;

			.goods-info-box {
				display: flex;
				justify-content: space-between;
				align-items: center;
			}

			.goods-name {
				font-size: 13px;
			}

			.goods-price {
				font-size: 16px;
				color: #c00000;
			}
		}
	}
</style>
