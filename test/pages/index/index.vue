<template>
	<view class="content">
        <view v-for="(article,index) in list" :key="index" @tap="articleDetail" :data-index="index">
			{{article.title}}
		</view>
	</view>
</template>

<script>
	import jpress from "@/common/jpress.js"
	export default {
		data() {
			return {
				title: 'Hello',
				list:[]
			}
		},
		onLoad() {
			this.loadArticles();
		},
		methods: {
			loadArticles(){
				jpress.getArticlePage({
						page: 1,
					})
					.then(data => {
						this.list=data.page.list
					});
			},
			articleDetail(e){
				uni.setStorageSync('article',this.list[e.currentTarget.dataset.index]);
				uni.navigateTo({
					url:'/pages/article/article'
				})
			}
		}
	}
</script>

<style>
	.content {
		text-align: center;
		height: 400upx;
	}
    .logo{
        height: 200upx;
        width: 200upx;
        margin-top: 200upx;
    }
	.title {
		font-size: 36upx;
		color: #8f8f94;
	}
</style>
