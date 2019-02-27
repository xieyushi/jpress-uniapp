<template>
	<view>
		{{article.text}}
	</view>
</template>

<script>
	export default {
		data() {
			return {
				article:{}
			};
		},
		onLoad() {
			this.article = uni.getStorageSync('article');
		}
	}
</script>

<style>

</style>
