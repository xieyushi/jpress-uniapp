# jpress-uniapp 从0开始编写uniapp博客
应jpress作者海哥的要求.在这里重写一篇从0开始编写uniapp博客的傻瓜式教程,做为一个java程序员,其实我自己,做为一个java程序员,加一点点前端技术,对uniapp也并没有太过熟练的应用,不过在使用jpress大部分作用者都为java程序员的情况下,自己也是一步步从java程序员成长为全栈的, 希望这篇博客,能对大家有所帮助.  
在编写小程序前,请先将jpress的后台程序布置好.记得在系统的接口中开启启用api接口,并且配置上申请的小程序的appid,以及和前端约定一个签名密钥如下图:  
![image](https://raw.githubusercontent.com/xieyushi/blog/master/blogimg/blogimg33.png)  
jpress的部署问题,就不再在这里来描述了,大家有问题请找海哥...  
在进入uniapp开发前,大家可以先下载一下海哥写的一个小程序模板,因为里面有对jpress后台的接口的请求的一系列的封装.代码地址是[https://gitee.com/fuhai/jpress-miniprogram-simpleblog](https://gitee.com/fuhai/jpress-miniprogram-simpleblog)  
将项目中的jpress-miniprogram-simpleblog/src/utils/jpress.js,jpress-miniprogram-simpleblog/src/utils/md5.js与 
jpress-miniprogram-simpleblog/src/app.js下载下来.我们用uniapp新建项目时是会用到这两个文件的.  
现在开始编写小程序:  
#### 一.下载uniapp配套的开发工具:hbuilderX  
下载网址[https://www.dcloud.io/hbuilderx.html](https://www.dcloud.io/hbuilderx.html)  
#### 二.新建项目  
安装完成后打开工具,点左上角的文件-新建-项目,在上方选择uni-app(U),选择模板为默认模板,项目我们就暂时命名为test,然后点击创建新建项目:  
![image](https://raw.githubusercontent.com/xieyushi/blog/master/blogimg/blogimg34.png)  
项目新建好以后目录是这个样子的:  
![image](https://raw.githubusercontent.com/xieyushi/blog/master/blogimg/blogimg35.png)  
pages下就是访问的页面.新建的只有一个index.vue,static里面放的是静态资源(但是太大的文件不要放在这个里面,因为小程序的代码不能超过两M,在不分包的情况下,一般的小程序太小也不用分包,所以这里就不讲解这个东西了),App.vue里面,是放小程序加载的时候所需要运行的代码,以及加载一些通用的css之类,mainfest.json中是一些uniapp的配置项,pages.json是页面的一些配置,uni.scss这个文件我们暂时不用管.  
这里我们先双击打开mainfest.json,在hbuilderX中双击打开.开始不会是源码视图,而是一个配置界面:  
![image](https://raw.githubusercontent.com/xieyushi/blog/master/blogimg/blogimg36.png)  
点击微信小程序配置,然后将自己要开发的小程序appid填写上去,然后点击hbuilderX顶部的运行-运行到小程序模拟器-微信开发者工具(请先提前安装好微信开发者工具),就可以看到这个初始小程序的index界面.  
#### 三.加入jpress相关代码  
这里jpress的后台设置了密钥,所以首先会有一个签名的检验.  
将下载下来的app.js中的onLaunch: function () {}中的代码直接复制到test项目中的App.vue的onLaunch: function () {}中:  

```
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
```
上面的代码中加入了uni的方法.uni.setStorageSync表示将数据放入缓存.原来的app.js中是this.globalData.xxx=xxx这个.在uniapp中.不要再彡了.直接用缓存机制来存储数据会更好.因为如果你不止是做小程序,还做app,做百度小程序,头条,支付宝百度小程序的话.就用uni封装好的方法.这样在跨平台编译的时候,坑会少很多
在test项目根目录下新建common文件夹,将下载下来的jpress.js与md5.js放入common文件夹中,然后在App.vue中的export default {上面加入一行代码:  

```
import jpress from "@/common/jpress.js"
```
保存后微信开发者工具上会界面刷新,再看Network界面可以看到有url请求了,而且能够得到正常的数据,说明jpress的签名检验也是通过了的.  
#### 四.开始编写页面  
好了.到了这里.基本上小程序与jpress的后台已经能够正常访问了.这里,我们一共要编写两个页面,一个是博客的列表页.一个是博客的文章页.这里我们先说一下uniapp的页面结构:  
uniapp大部分代码与vue是很类似的.(如果大家不熟悉vue,其实也并没有关系,我就不会vue,但是uniapp我一天就上手了.前提是你至少要有一点前端知识.比如能用layui,easyui,ligerui.等来写一个前后端分离的管理后台什么的,如果只是写过jsp的java程序员,可能你的前端要上手uniapp,还需要一点耐心和时间),但是对于了解vue的同学来说.也同样要注意一些问题:uniapp因为需要跨平台编译,所以有一些vue的属性,也是不支持的,比如不支持函数,过滤器,不支持计算属性传参,等等,这里我就不再一一介绍,大家有时间去uniapp的官网.好好看一看[https://uniapp.dcloud.io/README](https://uniapp.dcloud.io/README)  
我们先来看一下新建的项目中的index.vue默认的代码:  

```
<template>
	<view class="content">
        <image class="logo" src="../../static/logo.png"></image>
		<view>
            <text class="title">{{title}}</text>
        </view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello'
			}
		},
		onLoad() {

		},
		methods: {

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
```
可以看到index.vue的代码分为三个部分:template,script,style,第一个是html内容代码块.这里一定要记住的是,template标签下,有且只有一个<view>的子元素标签,其它的html代码一定要在这个view标签内(至于为什么.我不知道.我说了.我也不懂vue,另外.这篇博客.是傻瓜式教程.所以我只做说做法,不做去深究原因);而在script标签内,代码块就稍稍复杂一点,export default{}代码块中是页面要运行的js,另外,如果你需要引入插件,组件等等 ,需要在export default代码块上面来引入具体方式参照上面说过的jpress.js的引入方式,在export default{}中有三个参数:data,onLoad,methods,data中直接return了一个json这个json就是在页面上我们要用到的变量,都可以放在这里,onLoad中,要写的是页面加载的时候要运行的js,而methods中则是写页面的公共方法. 这里我们先在export default 代码块之前引入jpress.js:  

```
import jpress from "@/common/jpress.js"
```
然后在methods中添加一个加载文章列表的方法:  

```
loadArticles(){
	jpress.getArticlePage({
			page: 1,
		})
		.then(data => {
			console.log(data);
		});
}
```
然后在onLoad()中添加js代码:  

```
this.loadArticles();
```
这样得到的代码如下:
![image](https://raw.githubusercontent.com/xieyushi/blog/master/blogimg/blogimg37.png)  
重新运行小程序.在console控制台上就可以看到数据了.如下图:  
![image](https://raw.githubusercontent.com/xieyushi/blog/master/blogimg/blogimg38.png)  
这里我们可以看到data的具体json格式,这里我要说的是,请求得到的数据与uni.request是有一点区别的.但是这里是海哥写的jpress封装好的.所以才是这个样子(大家在不熟悉小程序开发的时候.尽量多使用console.log将自己想要研究的对象打印出来,然后看一下json结构,再做其它的操作,避免因为数据结构不了解而浪费的时间和精力).这里面的data.page.list才是我们要的列表数据.在data中添加一个list:  
```
data() {
			return {
				title: 'Hello',
				list:[]
			}
		},
```
然后将list在loadArticles方法中赋值:  
将console.log(data);改为this.list=data.page.list,保存后,小程序页面再次刷新,这里我们可以查看AppData中的值,如下图:  
![image](https://raw.githubusercontent.com/xieyushi/blog/master/blogimg/blogimg39.png)  
可以看到当前的页面中.list已经赋值上了.接下来就是将list显示在页面上了.  
将template代码块修改成如下的代码:  

```
<template>
	<view class="content">
        <view v-for="(article,index) in list" :key="index">
			{{article.title}}
		</view>
	</view>
</template>
```
保存后微信开发者工具的界面会再次刷新:  
![image](https://raw.githubusercontent.com/xieyushi/blog/master/blogimg/blogimg40.png)  
博客的标题已经加载出来了(样式什么的,请大家自己再去调整,这个.我可手把手教不了太多...大家要是心中没有一个大致的样式,可以参考一下我现在的小程序博客:  
![image](https://raw.githubusercontent.com/xieyushi/blog/master/blogimg/miniapp.jpg)  
当然,大家也可以有自己的想法,而且应该绝大多数,都会比我的更好.)  
接下来.我们先新建博客内容页:  
在项目文件视图中的pages文件夹上点击右键.选择新建页面输入页面名称article,hbuilderX会在pages文件夹下新建一个article文件夹,文件夹中有一个article.vue,这就是我们接下来要用到的文章详情页.  
接下来为列表页添加点击跳转页面的事件:  
在methods:{}代码块中添加点击跳转的事件:  

```
articleDetail(e){
	console.log(e);
}
```
再一次看到了console,这里我们先来看看这个到底有什么用,将template中的代码:  

```
<view v-for="(article,index) in list" :key="index">
```
改为:  

```
<view v-for="(article,index) in list" :key="index" @tap="articleDetail">
```
保存后,微信开发者工具界面会刷新,点击任意一条数据.然后查看console的打印内容:  
![image](https://raw.githubusercontent.com/xieyushi/blog/master/blogimg/blogimg41.png)  
每一次的点击,你都可以看到,会有这些对象json出来.但是你从这些json数据上,也看不出来.你到底点了哪一条数据,没有jquery.就是这样...你得习惯.那么此时我们如何解决呢.
将代码:  
```
<view v-for="(article,index) in list" :key="index" @tap="articleDetail">
```
改为:  

```
<view v-for="(article,index) in list" :key="index" @tap="articleDetail" :data-index="index">
```
再次刷新页面然后点击数据.你会看到在console中的json的currentTarget.dataset.index有一个值,这个就是循环的列表的index.这样我们可以通过这个index.知道点击的是列表的第几条数据了.接下来.我们将跳转的方法再做修改:  

```
articleDetail(e){
	uni.setStorageSync('article',this.list[e.currentTarget.dataset.index]);
	uni.navigateTo({
		url:'/pages/article/article'
	})
}
```
然后在页面上点击标题就会跳转了.这里上面的两个方法我解释一下,uni.setStorageSync这个方法是将一个键值对存入缓存,便于在跳转到其它的页面上时,这个数据还能被其它页面所读取,而uni.navigateTo则是跳转页面.  
打开的article页面是空的.白板,接下来我们就开始写article里面的内容了,这里我就不再仔细的说了.源码如下:  

```
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
```
上面的代码,template中直接在view中展示article.text,在data中创建article数据为一个空json,在onLoad方法中,给this.article赋值,uni.getStorageSync则是通过键名获取缓存中的数据内容.至此,最简易的uniapp编写小程序博客就完成了,当然样式上,还有其它的东西.还需要大家自己去添砖加瓦,不过相信大家看到这里.应该也基本上算是入门了uniapp编写与jpress对接的小程序,这里先展示一下这个项目的效果图:  
![image](https://raw.githubusercontent.com/xieyushi/blog/master/blogimg/bloggif5.gif)  
傻瓜式的教程就到这里了.元素的丰富还需要大家多多去阅读uniapp的文档.另外.有很多的例子,与模板.都在uniapp中有样例,查看代码的方式是在hbuilder中新建项目.然后选择uni-app,然后选择模板为hello uni-app的模板.里面有大量现成的代码,效果,组件,模板等代码,大家只要用心看.一定能学会,并且将这些代码运用在自己的项目中.我也是这样一步一步的看过来的,希望大家有兴趣的话,能认真的学习.另外.将本次教程的源码我也上传一下github,供大家下载.不过在App.vue中的代码jpress.init方法中的三个参数:host,app_id,app_secret,源码中还需要大家自己配置好以后,才能正常运行的哦!源码下载地址是[https://github.com/xieyushi/jpress-uniapp](https://github.com/xieyushi/jpress-uniapp)  
最后,再一次推广我的小程序博客:  
![image](https://raw.githubusercontent.com/xieyushi/blog/master/blogimg/miniapp.jpg)   
也欢迎大家访问我的hexo博客[https://blog.coder666.cn/](https://blog.coder666.cn/)

