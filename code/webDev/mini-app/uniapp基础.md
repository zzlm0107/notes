---
title: uni-app 基础
icon: jichu
date: 2023-03-20
category:
  - 小程序
tag:
  - 基础部分
---

## 初识uni-app

`uni-app` 是一个使用 [Vue.js](https://vuejs.org/)开发所有前端应用的框架，开发者编写一套代码，可发布到iOS、Android、Web（响应式）、以及各种小程序（微信/支付宝/百度/头条/飞书/QQ/快手/钉钉/淘宝）、快应用等多个平台。

> 翻译翻译就是：写一套代码就可以在多端运行

- uni-app的编辑工具官方推荐使用 **[HBuilderX](https://dcloud.io/hbuilderx.html)**

- 详细文档和具体语法规范请查看 **[uni-app官网](https://uniapp.dcloud.net.cn/tutorial/)**



## 初见HBuilderX

第一次使用时需要配置打开的默认浏览器路径，下载相关语法插件，配置关联微信开发者工具（微信开发者工具需要开启服务端口，在微信工具的设置->安全中）等

官方提供的项目文件夹放置规范如下：

```txt
┌─uniCloud              云空间目录，阿里云为uniCloud-aliyun,腾讯云为uniCloud-tcb（详见uniCloud）
│─components            符合vue组件规范的uni-app组件目录
│  └─comp-a.vue         可复用的a组件
├─utssdk                存放uts文件
├─pages                 业务页面文件存放的目录
│  ├─index
│  │  └─index.vue       index页面
│  └─list
│     └─list.vue        list页面
├─static                存放应用引用的本地静态资源（如图片、视频等）的目录，注意：静态资源只能存放于此
├─uni_modules           存放[uni_module](/uni_modules)。
├─platforms             存放各平台专用页面的目录，详见
├─nativeplugins         App原生语言插件 详见
├─nativeResources       App端原生资源目录
│  └─android            Android原生资源目录 详见
├─hybrid                App端存放本地html文件的目录，详见
├─wxcomponents          存放小程序组件的目录，详见
├─unpackage             非工程代码，一般存放运行或发行的编译结果
├─AndroidManifest.xml   Android原生应用清单文件 详见
├─main.js               Vue初始化入口文件
├─App.vue               应用配置，用来配置App全局样式以及监听 应用生命周期
├─manifest.json         配置应用名称、appid、logo、版本等打包信息，详见
├─pages.json            配置页面路由、导航条、选项卡等页面类信息，详见
└─uni.scss              这里是uni-app内置的常用样式变量 
```

**Tips**

- 编译到任意平台时，`static` 目录下的文件均会被完整打包进去，且不会编译。非 `static` 目录下的文件（vue、js、css 等）只有被引用到才会被打包编译进去。

- `static` 目录下的 `js` 文件不会被编译，如果里面有 `es6` 的代码，不经过转换直接运行，在手机设备上会报错。

- `css`、`less/scss` 等资源不要放在 `static` 目录下，建议这些公用的资源放在自建的 `common` 目录下。

- HbuilderX 1.9.0+ 支持在根目录创建 `ext.json`、`sitemap.json` 等小程序需要的文件。

  

同时为了实现多端兼容，综合考虑编译速度、运行性能等因素，`uni-app` 约定了如下开发规范：

- 页面文件遵循 Vue 单文件组件 (SFC) 规范
- 组件标签靠近小程序规范，详见[uni-app 组件规范](https://uniapp.dcloud.net.cn/component/)
- 接口能力（JS API）靠近微信小程序规范，但需将前缀 `wx` 替换为 `uni`，详见[uni-app接口规范](https://uniapp.dcloud.net.cn/api/)
- 数据绑定及事件处理同 `Vue.js` 规范，同时补充了App及页面的生命周期
- 为兼容多端运行，建议使用flex布局进行开发



## 全局文件之pages.json

`pages.json` 文件用来对 uni-app 进行全局配置，决定页面文件的路径、窗口样式、原生的导航栏、底部的原生tabbar 等。下面介绍几个比较重要的：

### globalStyle

globalStyle 用于设置应用的状态栏、导航条、标题、窗口背景色等。

```json
// pages.json
{
  "globalStyle": {
		"navigationBarTextStyle": "black", //导航栏文字样式 black | white
		"navigationBarTitleText": "演示",   //导航栏文字内容
		"navigationBarBackgroundColor": "#F8F8F8",  //导航栏背景颜色
		"enablePullDownRefresh": false //是否开启下拉刷新
		//...
	},
  //...
}
```

更多配置项请查看官方文档里的 [相关部分](https://uniapp.dcloud.net.cn/collocation/pages.html#globalstyle)

### pages

`uni-app` 通过 pages 节点配置应用由哪些页面组成，pages 节点接收一个数组，数组每个项都是一个对象。主要包含两项:

| 属性  | 类型   | 默认值 | 描述                                                         |
| :---- | :----- | :----- | :----------------------------------------------------------- |
| path  | String |        | 配置页面路径                                                 |
| style | Object |        | 配置页面窗口表现，配置项参考官方（页面配置会覆盖全局配置） [pageStyle](https://uniapp.dcloud.net.cn/collocation/pages#style) |

**Tips：**

- pages节点的第一项为应用入口页（即首页）
- **应用中新增/减少页面**，都需要对 pages 数组进行修改
- 文件名**不需要写后缀**，框架会自动寻找路径下的页面资源

### tabBar

如果应用是一个多 tab 应用，可以通过 tabBar 配置项指定一级导航栏，以及 tab 切换时显示的对应页。

```json
{
  "tabBar": {	
    "color": "#ccc",  // tab 上的文字默认颜色	
    "selectedColor": "#ff55ff", // tab 上的文字选中时的颜色
    "backgroundColor": "#ffffff" // tab背景色
    "list": [
    	{
    		"text":"首页", //名称
				"pagePath":"pages/index/index", //页面路径
				"iconPath":"static/logo.png", //图片路径
				"selectedIconPath":"static/1.jpg" //选择后图片路径
    		// "iconfont": {} //字体图标配置，优先级比iconPath更高
  		}
    ]
  }
}
```

更多配置项请查看官方文档里的 [相关部分](https://uniapp.dcloud.net.cn/collocation/pages.html#pages)

### condition

启动模式配置，仅开发期间生效，用于模拟直达页面的场景，如：小程序转发后，用户点击所打开的页面。

```json
"condition": { //模式配置，仅开发期间生效
	"current": 0, //当前激活的模式（或list 的索引项）
	"list": [{
			"name": "swiper", //模式名称
			"path": "pages/component/swiper/swiper", //启动页面，必选
			"query": "interval=4000&autoplay=false" //启动参数，在页面的onLoad函数里面得到。
		},
		{
			"name": "test",
			"path": "pages/component/switch/switch"
		}
	]
}
```

注意在微信小程序开发工具中需要手动改变编译模式。



### easycom

> `HBuilderX 2.5.5`起支持`easycom`组件模式。

传统vue组件，需要安装、引用、注册，三个步骤后才能使用组件。`easycom`将其精简为一步。 只要组件安装在项目根目录或uni_modules的components目录下，并符合`components/组件名称/组件名称.vue` 或 `uni_modules/插件ID/components/组件名称/组件名称.vue`目录结构。就可以不用引用、注册，直接在页面中使用，相当于全局组件。



## 内置组件

### view

类似于传统html中的div，用于包裹各种元素内容。

| 属性名                 | 类型    | 默认值 | 说明                                                         |
| :--------------------- | :------ | :----- | :----------------------------------------------------------- |
| hover-class            | String  | none   | 指定按下去的样式类。当 hover-class="none" 时，没有点击态效果 |
| hover-stop-propagation | Boolean | false  | 指定是否阻止本节点的祖先节点出现点击态，App、H5、支付宝小程序、百度小程序不支持（支付宝小程序、百度小程序文档中都有此属性，实测未支持） |
| hover-start-time       | Number  | 50     | 按住后多久出现点击态，单位毫秒                               |
| hover-stay-time        | Number  | 400    | 手指松开后点击态保留时间，单位毫秒                           |



### scroll-view

可滚动视图区域。用于区域滚动。[官方文档](https://uniapp.dcloud.net.cn/component/scroll-view.html)

| 属性名   | 类型    | 默认值 | 说明         |
| :------- | :------ | :----- | :----------- |
| scroll-x | Boolean | false  | 允许横向滚动 |
| scroll-y | Boolean | false  | 允许纵向滚动 |

使用竖向滚动时，需要给 `<scroll-view>` 一个固定高度，通过 css 设置 height；使用横向滚动时，需要给`<scroll-view>`添加`white-space: nowrap;`样式。



### swiper

滑块视图容器。一般用于左右滑动或上下滑动，比如banner轮播图。

```html
<swiper>
  <swiper-item> xxx </swiper-item>
  <swiper-item> xxx </swiper-item>
  <swiper-item> xxx </swiper-item>
</swiper>
```

一些重要的属性如下：

| 属性名                 | 类型    | 默认值            | 说明                 |
| :--------------------- | :------ | :---------------- | :------------------- |
| indicator-dots         | Boolean | false             | 是否显示面板指示点   |
| indicator-color        | Color   | rgba(0, 0, 0, .3) | 指示点颜色           |
| indicator-active-color | Color   | #000000           | 当前选中的指示点颜色 |
| autoplay               | Boolean | false             | 是否自动切换        |
| current                | Number  | 0                 | 当前所在滑块的 index |
| interval               | Number  | 5000              | 自动切换时间间隔     |
| duration               | Number  | 500               | 滑动动画时长        |
| circular               | Boolean | false             | 是否采用衔接滑动，即播放到末尾后重新回到开头|
| vertical               | Boolean | false             | 滑动方向是否为纵向   |

此外还有一些绑定事件，具体参考 [官方文档](https://uniapp.dcloud.net.cn/component/swiper.html)



### icon

**属性说明**

| 属性名 | 类型   | 默认值 | 说明                     |
| ------ | ------ | ------ | ------------------------ |
| type   | String |        | icon的类型               |
| size   | Number | 23     | icon的大小，单位px       |
| color  | Color  |        | icon的颜色，同css的color |

各平台 type 有效值说明：

|             平台              |                         type 有效值                          |
| :---------------------------: | :----------------------------------------------------------: |
| App、H5、微信小程序、QQ小程序 | success, success_no_circle, info, warn, waiting, cancel, download, search, clear |
|         支付宝小程序          | info, warn, waiting, cancel, download, search, clear, success, success_no_circle,loading |
|          百度小程序           | success, info, warn, waiting, success_no_circle, clear, search, personal, setting, top, close, cancel, download, checkboxSelected, radioSelected, radioUnselect |



### text

文本组件。用于包裹文本内容。 相当于 span标签。 [相关文档](https://uniapp.dcloud.net.cn/component/text.html)

| 属性名      | 类型    | 默认值 | 说明         | 平台差异说明 |
| :---------- | :------ | :----- | :----------- | :----------- |
| selectable  | Boolean | false  | 文本是否可选 |              |
| user-select | Boolean | false  | 文本是否可选 | 微信小程序   |

**Tips**

- `<text>` 组件内只支持嵌套 `<text>`，不支持其它组件或自定义组件，否则会引发在不同平台的渲染差异。
- 在app-nvue下，只有`<text>`才能包裹文本内容。无法在`<view>`组件包裹文本。
- decode 可以解析的有 ` ` `<` `>` `&` `'` ` ` ` `。
- 各个操作系统的空格标准并不一致。
- 除了文本节点以外的其他节点都无法长按选中。
- 支持 `\n` 方式换行。
- 如果使用 `<span>` 组件编译时会被转换为 `<text>`。



### navigator

用于页面跳转。该组件类似HTML中的`<a>`组件，但只能跳转本地页面。目标页面必须在pages.json中注册。

| 属性名    | 类型   | 默认值   | 说明                                                         |
| :-------- | :----- | :------- | :----------------------------------------------------------- |
| url       | String |          | 应用内的跳转链接，值为相对路径或绝对路径，如："../first/first"，"/pages/first/first"，注意不能加 `.vue` 后缀 |
| open-type | String | navigate | 跳转方式 navigate \| redirect \| switchTab \| reLaunch \| navigateBack |

在url上可以携带参数，相当于vue中的query参数。但url有长度限制，太长的字符串会传递失败，可使用[窗体通信](https://uniapp.dcloud.io/tutorial/page.html#emit)、[全局变量](https://ask.dcloud.net.cn/article/35021)，或`encodeURIComponent`等多种方式解决，如下为`encodeURIComponent`示例。

```js
<navigator :url="'/pages/navigate/navigate?item='+ encodeURIComponent(JSON.stringify(item))"></navigator>

// navigate.vue页面接受参数
onLoad: function (option) {
	const item = JSON.parse(decodeURIComponent(option.item));
}
```



### imgae

| 属性名    | 类型    | 默认值        | 说明                                             | 平台差异说明                                       |
| --------- | ------- | ------------- | ------------------------------------------------ | -------------------------------------------------- |
| src       | String  |               | 图片资源地址                                     |                                                    |
| mode      | String  | 'scaleToFill' | 图片裁剪、缩放的模式                             |                                                    |
| lazy-load | Boolean | false         | 图片懒加载。只针对page与scroll-view下的image有效 | 微信小程序、百度小程序、字节跳动小程序、飞书小程序 |

**Tips**

- `<image>` 组件默认宽度 320px、高度 240px；`app-nvue平台，暂时默认为屏幕宽度、高度 240px；`
- `src` 仅支持相对路径、绝对路径，支持 base64 码；
- 页面结构复杂，css样式太多的情况，使用 image 可能导致样式生效较慢，出现 “闪一下” 的情况，此时设置 `image{will-change: transform}` ,可优化此问题。
- 自定义组件里面使用 `<image>`时，若 `src` 使用相对路径可能出现路径查找失败的情况，故建议使用绝对路径。
- svg 格式的图片在不同的平台支持情况不同。具体为：app-nvue 不支持 svg 格式的图片，小程序上只支持网络地址。



**mode 有效值：**

mode 有 14 种模式，其中 5 种是缩放模式，9 种是裁剪模式。

| 模式 | 值          | 说明                                                         |
| ---- | ----------- | ------------------------------------------------------------ |
| 缩放 | scaleToFill | 不保持纵横比缩放图片，使图片的宽高完全拉伸至填满 image 元素 （常用） |
| 缩放 | aspectFit   | 保持纵横比缩放图片，使图片的长边能完全显示出来。也就是说，可以完整地将图片显示出来。 |
| 缩放 | aspectFill  | 保持纵横比缩放图片，只保证图片的短边能完全显示出来。也就是说，图片通常只在水平或垂直方向是完整的，另一个方向将会发生截取。 |
| 缩放 | widthFix    | 宽度不变，高度自动变化，保持原图宽高比不变                   |
| 缩放 | heightFix   | 高度不变，宽度自动变化，保持原图宽高比不变 **App 和 H5 平台 HBuilderX 2.9.3+ 支持、微信小程序需要基础库 2.10.3** |





## uni-app的 API

### API Promise化

1.具体 API `Promise 化` 的策略：

- 异步的方法，如果不传入 success、fail、complete 等 callback 参数，将以 Promise 返回数据。例如：`uni.getImageInfo()`
- 异步的方法，且有返回对象，如果希望获取返回对象，必须至少传入一项 success、fail、complete 等 callback 参数。例如：

```js
 // 正常使用
 const task = uni.connectSocket(
  success(res){
   console.log(res)
  }
 )

 // Promise 化
 uni.connectSocket().then(res => {
   // 此处即为正常使用时 success 回调的 res
   // uni.connectSocket() 正常使用时是会返回 task 对象的，如果想获取 task ，则不要使用 Promise 化
   console.log(res)
 })
```

2. 不进行 `Promise 化` 的 API：

- 同步的方法（即以 sync 结束）。例如：`uni.getSystemInfoSync()`
- 以 create 开头的方法。例如：`uni.createMapContext()`
- 以 manager 结束的方法。例如：`uni.getBackgroundAudioManager()`

具体相关信息请查看官网。



### 网络请求

通过 **`uni.request(OBJECT)`** 发起网络请求。

> 在各个小程序平台运行时，网络相关的 API 在使用前需要配置域名白名单。
>
> **success等等函数最好都要用箭头函数，不然拿不到this指定的data数据！**

**OBJECT 参数说明**

| 参数名   | 类型                      | 必填 | 默认值 | 说明                                             | 平台差异说明                                                 |
| -------- | ------------------------- | ---- | ------ | ------------------------------------------------ | ------------------------------------------------------------ |
| url      | String                    | 是   |        | 开发者服务器接口地址                             |                                                              |
| data     | Object/String/ArrayBuffer | 否   |        | 请求的参数                                       | App 3.3.7 以下不支持 ArrayBuffer 类型                        |
| header   | Object                    | 否   |        | 设置请求的 header，header 中不能设置 Referer。   | App、H5端会自动带上cookie，且H5端不可手动修改                |
| method   | String                    | 否   | GET    | 有效值详见下方说明                               |                                                              |
| timeout  | Number                    | 否   | 60000  | 超时时间，单位 ms                                | H5(HBuilderX 2.9.9+)、APP(HBuilderX 2.9.9+)、微信小程序（2.10.0）、支付宝小程序 |
| success  | Function                  | 否   |        | 收到开发者服务器成功返回的回调函数               |                                                              |
| fail     | Function                  | 否   |        | 接口调用失败的回调函数                           |                                                              |
| complete | Function                  | 否   |        | 接口调用结束的回调函数（调用成功、失败都会执行） |                                                              |

**success 返回参数说明**

| 参数       | 类型                      | 说明                                         |
| ---------- | ------------------------- | -------------------------------------------- |
| data       | Object/String/ArrayBuffer | 开发者服务器返回的数据                       |
| statusCode | Number                    | 开发者服务器返回的 HTTP 状态码               |
| header     | Object                    | 开发者服务器返回的 HTTP Response Header      |
| cookies    | `Array.<string>`          | 开发者服务器返回的 cookies，格式为字符串数组 |

```js
uni.request({
    url: 'https://www.example.com/request', //仅为示例，并非真实接口地址。
    data: {
        text: 'uni.request'
    },
    header: {
        'custom-header': 'hello' //自定义请求头信息
    },
    success: (res) => {
        console.log(res.data);
        this.text = 'request success';
    }
});
```

**返回值**

如果希望返回一个 `requestTask` 对象，需要至少传入 success / fail / complete 参数中的一个。例如：

```js
var requestTask = uni.request({
	url: 'https://www.example.com/request', //仅为示例，并非真实接口地址。
	complete: ()=> {}
});
requestTask.abort();
```



如果没有传入 success / fail / complete 参数，则会返回封装后的 Promise 对象：[Promise 封装](https://uniapp.dcloud.net.cn/api/#promise-封装)

通过 `requestTask`，可中断请求任务。



### 路由跳转

**`uni.navigateTo(OBJECT)`**

保留当前页面，跳转到应用内的某个页面，使用`uni.navigateBack`可以返回到原页面。

| 参数              | 类型     | 必填 | 默认值 | 说明                                                         | 平台差异说明 |
| ----------------- | -------- | ---- | ------ | ------------------------------------------------------------ | ------------ |
| url               | String   | 是   |        | 需要跳转的应用内非 tabBar 的页面的路径 , 路径后可以带参数。参数与路径之间使用?分隔，参数键与参数值用=相连，不同参数用&分隔；如 'path?key=value&key2=value2'，path为下一个页面的路径，下一个页面的onLoad函数可得到传递的参数 |              |
| animationType     | String   | 否   | pop-in | 窗口显示的动画效果，详见：[窗口动画](https://uniapp.dcloud.net.cn/api/router#animation) | App          |
| animationDuration | Number   | 否   | 300    | 窗口动画持续时间，单位为 ms                                  | App          |
| events            | Object   | 否   |        | 页面间通信接口，用于监听被打开页面发送到当前页面的数据。2.8.9+ 开始支持。 |              |
| success           | Function | 否   |        | 接口调用成功的回调函数                                       |              |
| fail              | Function | 否   |        | 接口调用失败的回调函数                                       |              |
| complete          | Function | 否   |        | 接口调用结束的回调函数（调用成功、失败都会执行）             |              |

相关示例：

```javascript
//在起始页面跳转到test.vue页面并传递参数
uni.navigateTo({
	url: 'test?id=1&name=uniapp'
});
```

```javascript
// 在test.vue页面接受参数
export default {
	onLoad: function (option) { //option为object类型，会序列化上个页面传递的参数
		console.log(option.id); //打印出上个页面传递的参数。
		console.log(option.name); //打印出上个页面传递的参数。
	}
}
```

```js
// 在起始页面跳转到test.vue页面，并监听test.vue发送过来的事件数据
uni.navigateTo({
  url: 'pages/test?id=1',
  events: {
    // 为指定事件添加一个监听器，获取被打开页面传送到当前页面的数据
    acceptDataFromOpenedPage: function(data) {
      console.log(data)
    },
    someEvent: function(data) {
      console.log(data)
    }
    ...
  },
  success: function(res) {
    // 通过eventChannel向被打开页面传送数据
    res.eventChannel.emit('acceptDataFromOpenerPage', { data: 'data from starter page' })
  }
})

// 在test.vue页面，向起始页通过事件传递数据
onLoad: function(option) {
  const eventChannel = this.getOpenerEventChannel();
  eventChannel.emit('acceptDataFromOpenedPage', {data: 'data from test page'});
  eventChannel.emit('someEvent', {data: 'data from test page for someEvent'});
  // 监听acceptDataFromOpenerPage事件，获取上一页面通过eventChannel传送到当前页面的数据
  eventChannel.on('acceptDataFromOpenerPage', function(data) {
    console.log(data)
  })
}
```



此外还要一些其他的跳转方式：

- **`uni.redirectTo(OBJECT)`**  关闭当前页面，跳转到应用内的某个页面。

- **`uni.reLaunch(OBJECT)`**  关闭所有页面，打开到应用内的某个页面。
- **`uni.switchTab(OBJECT)`**  跳转到 tabBar 页面，并关闭其他所有非 tabBar 页面。
- **`uni.navigateBack({delta：1})`**  关闭当前页面，返回上一页面或多级页面。可通过 `getCurrentPages()` 获取当前的页面栈，决定需要返回几层。

**OBJECT参数说明**

| 参数     | 类型     | 必填 | 说明                                                         |
| :------- | :------- | :--- | :----------------------------------------------------------- |
| url      | String   | 是   | 需要跳转的应用内非 tabBar 的页面的路径，路径后可以带参数。参数与路径之间使用?分隔，参数键与参数值用=相连，不同参数用&分隔；如 'path?key=value&key2=value2' |
| success  | Function | 否   | 接口调用成功的回调函数                                       |
| fail     | Function | 否   | 接口调用失败的回调函数                                       |
| complete | Function | 否   | 接口调用结束的回调函数（调用成功、失败都会执行）             |



此外在 2.8.9+ 后支持 [页面间事件通信通道](https://uniapp.dcloud.net.cn/api/router.html#event-channel)



### 数据缓存

**`uni.setStorage(OBJECT)`** 与 **`uni.setStorageSync(KEY,DATA)`**

将数据存储在本地缓存中指定的 key 中，会覆盖掉原来该 key 对应的内容。

**OBJECT 参数说明**

| 参数名   | 类型     | 必填 | 说明                                                         |
| :------- | :------- | :--- | :----------------------------------------------------------- |
| key      | String   | 是   | 本地缓存中的指定的 key                                       |
| data     | Any      | 是   | 需要存储的内容，只支持原生类型、及能够通过 JSON.stringify 序列化的对象 |
| success  | Function | 否   | 接口调用成功的回调函数                                       |
| fail     | Function | 否   | 接口调用失败的回调函数                                       |
| complete | Function | 否   | 接口调用结束的回调函数（调用成功、失败都会执行）             |



**`uni.getStorage(OBJECT)`** 与 **`uni.getStorageSync(KEY)`**

从本地缓存中读取指定的 key 的值

**OBJECT 参数说明**

| 参数名   | 类型     | 必填 | 说明                                             |
| :------- | :------- | :--- | :----------------------------------------------- |
| key      | String   | 是   | 本地缓存中的指定的 key                           |
| success  | Function | 是   | 接口调用的回调函数，res = {data: key对应的内容}  |
| fail     | Function | 否   | 接口调用失败的回调函数                           |
| complete | Function | 否   | 接口调用结束的回调函数（调用成功、失败都会执行） |



**`uni.removeStorage(OBJECT)`** 与 **`uni.removeStorageSync(KEY)`**

从本地缓存中移除指定 key。 异步方法的参数对象与上面相同。



**`uni.clearStorage()`** 与 **`uni.clearStorageSync()`**

清除本地缓存。 均无参数



### 交互反馈

**`uni.showToast(OBJECT)`**    显示消息提示框。

**OBJECT参数说明**

| 参数     | 类型     | 必填 | 说明                                                         | 平台差异说明                    |
| -------- | -------- | ---- | ------------------------------------------------------------ | ------------------------------- |
| title    | String   | 是   | 提示的内容，长度与 icon 取值有关。                           |                                 |
| icon     | String   | 否   | 图标，有效值详见下方说明。                                   |                                 |
| image    | String   | 否   | 自定义图标的本地路径（app端暂不支持gif）                     | App、H5、微信小程序、百度小程序 |
| mask     | Boolean  | 否   | 是否显示透明蒙层，防止触摸穿透，默认：false                  | App、微信小程序                 |
| duration | Number   | 否   | 提示的延迟时间，单位毫秒，默认：1500                         |                                 |
| position | String   | 否   | 纯文本轻提示显示位置，填写有效值后只有 `title` 属性生效，且不支持通过 uni.hideToast 隐藏。top \| center \| bottom | App                             |
| success  | Function | 否   | 接口调用成功的回调函数                                       |                                 |
| fail     | Function | 否   | 接口调用失败的回调函数                                       |                                 |
| complete | Function | 否   | 接口调用结束的回调函数（调用成功、失败都会执行）             |                                 |

其中 icon 取值：

| 值        | 说明                                                         | 平台差异说明                                                 |
| :-------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| success   | 显示成功图标，此时 title 文本在`小程序`平台最多显示 7 个汉字长度。 | 支付宝小程序无长度无限制                                     |
| error     | 显示错误图标，此时 title 文本在`小程序`平台最多显示 7 个汉字长度。 | 支付宝小程序、快手小程序、字节小程序、百度小程序、京东小程序、QQ小程序不支持 |
| fail      | 显示错误图标，此时 title 文本无长度显示。                    | 支付宝小程序、字节小程序                                     |
| exception | 显示异常图标。此时 title 文本无长度显示。                    | 支付宝小程序                                                 |
| loading   | 显示加载图标，此时 title 文本在`小程序`平台最多显示 7 个汉字长度。 | 支付宝小程序不支持                                           |
| none      | 不显示图标，此时 title 文本在`小程序`最多可显示两行，`App`仅支持单行显示。 |                                                              |



**`uni.hideToast()`**  

隐藏消息提示框。



**`uni.showLoading(OBJECT)`**  

显示 loading 提示框, 需主动调用 `uni.hideLoading()` 才能关闭提示框。

**OBJECT参数说明**

| 参数     | 类型     | 必填 | 说明                                             | 平台差异说明                    |
| :------- | :------- | :--- | :----------------------------------------------- | :------------------------------ |
| title    | String   | 是   | 提示的文字内容，显示在loading的下方              |                                 |
| mask     | Boolean  | 否   | 是否显示透明蒙层，防止触摸穿透，默认：false      | H5、App、微信小程序、百度小程序 |
| success  | Function | 否   | 接口调用成功的回调函数                           |                                 |
| fail     | Function | 否   | 接口调用失败的回调函数                           |                                 |
| complete | Function | 否   | 接口调用结束的回调函数（调用成功、失败都会执行） |                                 |



**`uni.showModal(OBJECT)`**

显示模态弹窗，可以只有一个确定按钮，也可以同时有确定和取消按钮。

**OBJECT参数说明**

| 参数        | 类型     | 必填 | 说明                                |
| :---------- | :------- | :--- | :---------------------------------- |
| title       | String   | 否   | 提示的标题                          |
| content     | String   | 否   | 提示的内容                          |
| showCancel  | Boolean  | 否   | 是否显示取消按钮，默认为 true       |
| cancelText  | String   | 否   | 取消按钮的文字，默认为"取消"        |
| cancelColor | HexColor | 否   | 取消按钮的文字颜色，默认为"#000000" |
| confirmText | String   | 否   | 确定按钮的文字，默认为"确定"        |
| success  | Function | 否   | 接口调用成功的回调函数                           |
| fail     | Function | 否   | 接口调用失败的回调函数                           |
| complete | Function | 否   | 接口调用结束的回调函数（调用成功、失败都会执行） |

**success返回参数说明**

| 参数    | 类型    | 说明                                                         | 平台差异说明                                                 |
| :------ | :------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| confirm | Boolean | 为 true 时，表示用户点击了确定按钮                           |                                                              |
| cancel  | Boolean | 为 true 时，表示用户点击了取消（用于 Android 系统区分点击蒙层关闭还是点击取消按钮关闭） |                                                              |
| content | String  | `editable` 为 true 时，用户输入的文本                        | H5 (3.2.10+)、App (3.2.10+)、微信小程序 (2.17.1+)、字节小程序（2.62.0+） |



### 下拉刷新

在 js 中定义 onPullDownRefresh 处理函数（和onLoad等生命周期函数同级），监听该页面用户下拉刷新事件。

- 需要在 `pages.json` 里，找到的当前页面的pages节点，并在 `style` 选项中开启 `enablePullDownRefresh`。
- 当处理完数据刷新后，`uni.stopPullDownRefresh` 可以停止当前页面的下拉刷新。



**`uni.startPullDownRefresh(OBJECT)`**

开始下拉刷新，调用后触发下拉刷新动画，效果与用户手动下拉刷新一致。

**OBJECT 参数说明**

| 参数名   | 类型     | 必填 | 说明                                             |
| :------- | :------- | :--- | :----------------------------------------------- |
| success  | Function | 否   | 接口调用成功的回调                               |
| fail     | Function | 否   | 接口调用失败的回调函数                           |
| complete | Function | 否   | 接口调用结束的回调函数（调用成功、失败都会执行） |



**`uni.stopPullDownRefresh()`**

停止当前页面下拉刷新。





### 页面通讯

**`getCurrentPages()`**

`getCurrentPages()` 函数用于获取当前页面栈的实例，以数组形式按栈的顺序给出，第一个元素为首页，最后一个元素为当前页面。

**注意：** `getCurrentPages()`仅用于展示页面栈的情况，请勿修改页面栈，以免造成页面状态错误。

每个页面实例的方法属性列表：

| 方法                  | 描述                          | 平台说明 |
| --------------------- | ----------------------------- | -------- |
| page.$getAppWebview() | 获取当前页面的webview对象实例 | App      |
| page.$vm              | 当前页面的 Vue 实例           |          |
| page.route            | 获取当前页面的路由            |          |

熟悉页面栈后才能更好的使用getCurrentPages()，下面将列举几种常用的使用场景： 1.利用页面栈的长度 例如：进入小程序非默认首页时，需要提供返回首页的按钮或者执行其它事件

```js
onShow() {
     let pages = getCurrentPages(); //当前页面栈
     if (pages.length == 1) {
        //todo
     }
},
```

2、跨页面赋值

```js
 let pages = getCurrentPages();//当前页面栈
 let prevPage = pages[pages.length - 2];//上一页面
 //直接给上移页面赋值
 prevPage.list = [...]; 
```

3、页面跳转后自动刷新

```js
 //举例
 uni.switchTab({
     url: '../index/index',
     success: function (e) {
         var page = getCurrentPages().pop(); //当前页面
         if (page == undefined || page == null) return;
         page.onLoad(); //或者其它操作
     }
 })
```

4、获取当前页面相关信息

```js
let pages = getCurrentPages(); //当前页面栈
//当前页面为页面栈的最后一个元素
let prevPage = pages[pages.length - 1];//当前页面
// or
// pop() 方法用于删除并返回数组的最后一个元素
let prevPage = pages.pop();//当前页面

console.log( prevPage.route) //举例：输出为‘pages/index/index
```



**`getApp()`**

`getApp()` 函数用于获取当前应用实例，一般用于获取globalData 。

**实例**

```js
const app = getApp()
console.log(app.globalData) 
```

**注意：**

- 不要在定义于 `App()` 内的函数中，或调用 `App` 前调用 `getApp()` ，可以通过 `this.$scope` 获取对应的app实例
- 通过 `getApp()` 获取实例之后，不要私自调用生命周期函数。
- 当在首页`nvue`中使用`getApp()`不一定可以获取真正的`App`对象。对此提供了`const app = getApp({allowDefault: true})`用来获取原始的`App`对象，可以用来在首页对`globalData`等初始化



- **`uni.$emit(eventName,OBJECT)`**  触发全局的自定义事件，附加参数都会传给监听器回调函数。

- **`uni.$on(eventName,callback)`**  监听全局的自定义事件，事件由 uni.$emit 触发，回调函数会接收事件触发函数的传入参数。

- **`uni.$once(eventName,callback)`**  监听全局的自定义事件，事件由 uni.$emit 触发，但仅触发一次，在第一次触发之后移除该监听器。

- **`uni.$off([eventName, callback])`**   移除全局自定义事件监听器。

**Tips**

- 如果uni.$off没有传入参数，则移除App级别的所有事件监听器；
- 如果只提供了事件名（eventName），则移除该事件名对应的所有监听器；
- 如果同时提供了事件与回调，则只移除这个事件回调的监听器；
- 提供的回调必须跟$on的回调为同一个才能移除这个回调的监听器；

**注意事项**

- uni.\$emit、 uni.\$on 、 uni.\$once 、uni.\$off 触发的事件都是 App 全局级别的，跨任意组件，页面，nvue，vue 等
- 使用时，注意及时销毁事件监听，比如，页面 onLoad 里边 uni.\$on 注册监听，onUnload 里边 uni.\$off 移除，或者一次性的事件，直接使用 uni.\$once 监听
- 注意 uni.\$on 定义完成后才能接收到 uni.\$emit 传递的数据





## 生命周期

生命周期的概念：一个对象从创建、运行、销毁的整个过程被成为生命周期。

生命周期函数：在生命周期中每个阶段会伴随着每一个函数的触发，这些函数被称为生命周期函数。



### 应用生命周期

`uni-app` 支持如下应用生命周期函数：

| 函数名               | 说明                                                         |
| :------------------- | :----------------------------------------------------------- |
| onLaunch             | 当`uni-app` 初始化完成时触发（全局只触发一次）               |
| onShow               | 当 `uni-app` 启动，或从后台进入前台显示                      |
| onHide               | 当 `uni-app` 从前台进入后台                                  |
| onError              | 当 `uni-app` 报错时触发                                      |
| onUniNViewMessage    | 对 `nvue` 页面发送的数据进行监听，可参考 [nvue 向 vue 通讯](https://uniapp.dcloud.io/tutorial/nvue-api?id=communication) |
| onUnhandledRejection | 对未处理的 Promise 拒绝事件监听函数（2.8.1+）                |
| onPageNotFound       | 页面不存在监听函数                                           |
| onThemeChange        | 监听系统主题变化                                             |

**分析：**

- 第一次加载页面时会调用`onLaunch` ，`onShow` 
- 当你在浏览器离开页面，去查看其它页面，或者其它应用会触发`onHide`，在回来查看该页面时触发`onShow` 

- **应用生命周期仅可在`App.vue`中监听，在其它页面监听无效**。



### 页面生命周期

`uni-app` 支持如下主要的页面生命周期函数：此外还要一些 [其他的钩子](https://uniapp.dcloud.net.cn/tutorial/page.html#componentlifecycle)

| 函数名   | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| onLoad   | 监听页面加载，其参数为上个页面传递的数据，参数类型为 Object（用于页面传参），参考[示例](https://uniapp.dcloud.net.cn/api/router#navigateto) |
| onShow   | 监听页面显示。页面每次出现在屏幕上都触发，包括从下级页面点返回露出当前页面 |
| onReady  | 监听页面初次渲染完成。注意如果渲染速度快，会在页面进入动画完成前触发 |
| onHide   | 监听页面隐藏                                                 |
| onUnload | 监听页面卸载                                                 |



### 组件生命周期

同vue组件生命周期。

