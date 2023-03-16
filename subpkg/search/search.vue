<template>
	<view class="content">
		<view class="search-box">
			<!-- 使用 uni-ui 提供的搜索组件 -->
			<uni-search-bar radius="100" placeholder="请输入搜索内容" clearButton="none" cancelButton="none"
				:focus="true" @confirm="search" @input="inputchange" />
			</uni-section>
		</view>
		<!-- 搜索历史 -->
		<view class="history-box" v-if="searchResults.length === 0">
			<!-- 标题区域 -->
			<view class="history-title">
				<text>搜索历史</text>
				<uni-icons type="trash" size="17" @click="cleanHistory"></uni-icons>
			</view>
			<!-- 列表区域 -->
			<view class="history-list">
				<uni-tag :text="item" v-for="(item, i) in historyList" :key="i" @click="gotoGoodsList(item)"></uni-tag>
			</view>
		</view>
		<!-- 搜索建议列表 -->
		<view class="sugg-list" v-else>
			<view class="sugg-item" v-for="(item, i) in searchResults" :key="i" @click="gotoDetail(item.goods_id)">
				<view class="goods-name">{{item.goods_name}}</view>
				<uni-icons type="arrowright" size="16"></uni-icons>
			</view>
		</view>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				// 延时器的 timerid
				timer: null,
				// 搜索关键字
				kw: '',
				// 搜索结果列表
				searchResults: [],
				// 搜索关键词的历史记录
				historyList: []
			};
		},
		onLoad() {
			this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
		},
		methods: {
			inputchange(e) {
				// 防抖
				// 清除timer对应的延时器
				clearTimeout(this.timer)
				// 重新开启延时器
				this.timer = setTimeout(() => {
					this.kw = e
					// console.log(this.kw)
					// 根据关键词，查询搜索建议列表
					this.getSearchList()
				}, 500)
			},
			// 2. 保存搜索关键词的方法
			saveSearchHistory() {
				// 2.1 直接把搜索关键词 push 到 historyList 数组中
				this.historyList.unshift(this.kw)
				// 转化为set去重
				let set = new Set(this.historyList)
				// 将 Set 对象转化为 Array 数组
				this.historyList = Array.from(set)
				// 调用 uni.setStorageSync(key, value) 将搜索历史记录持久化存储到本地
				uni.setStorageSync('kw', JSON.stringify(this.historyList))
			},
			// 根据搜索关键词，搜索商品建议列表
			async getSearchList() {
				// 判断关键词是否为空
				if (this.kw === '') {
					this.searchResults = []
					return
				}
				// 发起请求，获取搜索建议列表
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/goods/qsearch', {
					query: this.kw
				})
				if (res.meta.status !== 200) return uni.$showMsg()
				this.searchResults = res.message
				// 1. 查询到搜索建议之后，调用 saveSearchHistory() 方法保存搜索关键词
				this.saveSearchHistory()
			},
			gotoDetail(goods_id) {
				uni.navigateTo({
					// 指定详情页面的 URL 地址，并传递 goods_id 参数
					url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods_id
				})
			},
			// 清空搜索历史记录
			cleanHistory() {
				// 清空 data 中保存的搜索历史
				this.historyList = []
				// 清空本地存储中记录的搜索历史
				uni.setStorageSync('kw', '[]')
			},
			// 点击跳转到商品列表页面
			gotoGoodsList(kw) {
			  uni.navigateTo({
			    url: '/subpkg/goods_list/goods_list?query=' + kw
			  })
			}
		}
	}
</script>

<style lang="scss">
	.content {
		.search-box {
			background-color: #c00000;
			position: sticky;
			top: 0;
			z-index: 999;
		}

		.history-box {
			padding: 0 5px;

			.history-title {
				display: flex;
				justify-content: space-between;
				align-items: center;
				height: 40px;
				font-size: 13px;
				border-bottom: 1px solid #efefef;
			}

			.history-list {
				display: flex;
				flex-wrap: wrap;

				.uni-tag {
					margin-top: 5px;
					margin-right: 5px;
				}
			}
		}

		.sugg-list {
			padding: 0 5px;

			.sugg-item {
				font-size: 12px;
				padding: 13px 0;
				border-bottom: 1px solid #efefef;
				display: flex;
				align-items: center;
				justify-content: space-between;

				.goods-name {
					// 文字不允许换行（单行文本）
					white-space: nowrap;
					// 溢出部分隐藏
					overflow: hidden;
					// 文本溢出后，使用 ... 代替
					text-overflow: ellipsis;
					margin-right: 3px;
				}
			}
		}
	}
</style>
