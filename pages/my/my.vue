<template>
	<view>
		my
		<uni-icons type="contact" size="30"></uni-icons>

	</view>
</template>

<script>
	// 导入自己封装的 mixin 模块
	import badgeMix from '@/mixins/tabbar-badge.js'

	export default {
		// 将 badgeMix 混入到当前的页面中进行使用
		mixins: [badgeMix],
		data() {
			return {

			};
		}
	}
</script>

<style lang="scss">

</style>
