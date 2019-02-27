<script>
	import jpress from "@/common/jpress.js"
	export default {
		onLaunch: function () {
			jpress.init({
			 host:'这里填写jpress的外网请求url',
			 app_id:'这里填写小程序的appid',
			 app_secret:'这里填写你在jpress后台中设置的密钥'
			}) 
			 jpress.getAppName()
			   .then(data => {
			     if (data.value) {
					uni.setStorageSync('appName',data.value);
			     }
			   })
						
						
			 jpress.getCopyright()
			   .then(data => {
			     if (data.value) {
					uni.setStorageSync('copyright',data.value);
			     }
			   })
		},
		onShow: function () {
			console.log('App Show')
		},
		onHide: function () {
			console.log('App Hide')
		}
	}
</script>

<style>
	/*每个页面公共css */
</style>
