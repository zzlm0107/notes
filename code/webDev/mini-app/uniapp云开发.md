---
title: uni-app 云开发
icon: 进阶
date: 2023-03-25
category:
  - 小程序
tag:
  - 进阶部分
---


*云开发的部分适用于简单后台逻辑交互，如果你已经有了他人开发后台，或者想自己使用其他语言如java或node开发，可以忽略此部分*

## uniCloud

### 1.初遇uniCloud

1. 创建uniCloud项目

HBuilderX中新建项目，选择uni-app项目，并勾选`启用uniCloud`，在右侧选择服务供应商（腾讯云或阿里云）

2. 关联服务空间

一个开发者可以拥有多个服务空间，每个服务空间都是一个独立的serverless云环境，不同服务空间之间的云函数、数据库、存储都是隔离的。

对项目根目录`uniCloud`点右键选择`关联云服务空间`，绑定之前创建的服务空间，或者新建一个服务空间。

3. 创建云函数/云对象

`uniCloud`项目创建并绑定服务空间后，开发者可以创建云函数（云对象是云函数的一种，云函数可泛指普通云函数和云对象）。

在`uniCloud/cloudfunctions`目录右键创建云函数/云对象

- 创建云函数后，生成一个目录，该目录下自动生成index.js，是该云函数的入口文件，不可改名。
- 创建云对象后，生成一个目录，该目录下自动生成index.obj.js，是该云对象的入口文件，不可改名。

如果该云函数/云对象还需要引入其他js，可在index.js入口文件中引用。

4. 给云对象编写方法

打开helloco.obj.js，我们为它添加一个 sum 方法，逻辑就是接收传入a和b2个参数，返回求和结果。

```js
module.exports = {
	sum(a, b) {
		// 此处省略a和b的有效性校验
		return a + b
	}
}
```

5. 在前端调用云对象

在项目首页，pages/index/index.vue 里，添加一个按钮，点击后执行异步方法sum。

js 中 import 这个 helloco 对象，调用它的 sum 方法

```html
<template>
	<view class="content">
		<button @click="testco()">请求云对象的方法</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
			}
		},
		methods: {
			async testco() { // 注意异步
				const helloco = uniCloud.importObject('helloco') // 导入云对象
				try {
					const res = await helloco.sum(1,2) //导入云对象后就可以直接调用该对象的sum方法了，注意使用异步await
					console.log(res) // 结果是3
				} catch (e) {
					console.log(e)
				}
			}
		}
	}
</script>
```

6. 运行

将项目运行到浏览器或其他平台，点页面上的按钮，控制台会打印结果：3

HBuilderX自带一个云函数本地运行环境，运行项目时也默认选择 连接本地云函数。可以在底部控制台中的前端控制台右上角进行切换。

可以对helloco云对象点右键上传到uniCloud服务空间，然后在前端控制台右上角切换为 连接云端云函数，那么此时客户端连接的就是真正的现网uniCloud服务器了。

关于运行调试，有单独文档，[详见](https://uniapp.dcloud.net.cn/uniCloud/rundebug)

7. 小结

到此为止，你已经开发了第一个 first uniCloud 项目，完成了客户端和服务器的第一次交互。

这个云对象只做了一个求和运算，实际开发中不需要在服务器求和，前端就可以求和。服务器应该做更复杂的事情。

但你可以通过这个示例感知uniCloud的开发，其实非常简单。尤其是云对象，打破了服务器做接口传json给前端的传统，让云端服务对象化，让服务器代码像写前端js一样、清晰。

接下来，建议通读文档，进一步学习掌握uniCloud。

### 2.云函数/云对象

#### 2.1 简介

云函数是运行在云端的 `JavaScript` 代码，是基于 `Node.js` 的扩展。

在常规的 `Node API` 基础上，uniCloud的云函数环境内置了`uniCloud`对象，这个对象内置了网络、数据库等各种API。开发者未学习过 `Node.js` 也没有关系，只需要看uniCloud的文档，掌握这个`uniCloud`对象的API即可。

每个云函数是一个js包，在云函数被调用时，由 serverless 调度系统分配硬件资源启动一个 node 环境来运行这个云函数。在HBuilderX中可以新建云函数（HBuilderX 3.4 同时可以新建云对象）。每个云函数是一个目录，其中普通云函数有`index.js`入口文件，云对象的入口文件则是`index.obj.js`。

一个最简单的云函数只需要这个入口js文件，在里面编写代码即可。当然也可以在这个js中require该云函数目录下的其他js、json文件。

云函数的配置文件和 npm规范 相同，在云函数目录下可新建一个 package.json 来存放配置。uniCloud云函数扩展了 package.json，增加了一些特有的配置项。[详见](https://uniapp.dcloud.net.cn/uniCloud/cf-functions#packagejson)

云函数启动后实例会保留一段时间（如15分钟），超过保留期后若该云函数一直没有被再调用，那这个实例会被释放。所以云函数有冷启动的概念。不过由于js实例的启动要比php和java更快，所以js更适合serverless方式。

#### 2.2 云函数的分类

云函数有若干子概念，包括 普通云函数、云对象、公共模块、clientDB的action云函数、uniCloud扩展库。

- 云函数：通过传统json接口方式和客户端通信，客户端使用`uniCloud.callfunction("")`调用云函数
- 云对象：是通过前端导入对象来操作的，客户端使用`uniCloud.importObject("")`导入云对象。详见[云对象](https://uniapp.dcloud.net.cn/uniCloud/cloud-obj)
- 公共模块：用于不同的云函数/云对象，抽取和共享相同代码，详见[公共模块文档](https://uniapp.dcloud.net.cn/uniCloud/cf-functions#公共模块)
- action云函数：为了弥补clientDB客户端直接操作数据库的局限而设计的，详见[clientDB action文档](https://uniapp.dcloud.net.cn/uniCloud/clientdb#action)
- uniCloud扩展库：为了裁剪和控制云函数体积而设计的，一些不太常用的功能比如Redis，独立为可选扩展库，避免增大每个云函数的体积，详见[uniCloud扩展库](https://uniapp.dcloud.net.cn/uniCloud/cf-functions#扩展库)

HBuilderX中uniCloud项目的云函数均在项目的`uniCloud/cloudfunctions`目录下，目录结构如下：

```markdown
|——— cloudfunctions               云函数目录
|   │───common                    云函数公用模块目录 
|   |   └──hello-common           云函数公用模块
|   |      │──index.js            公用模块代码
|   |      └──package.json        公用模块package.json
|   │───uni-clientDB-actions
|   │      └──new_action.js       clientDB action代码 
|   │───function-name             云函数目录
|   │     │──index.js             云函数代码
|   │     └──package.json         包含云函数的配置信息，如url化、定时设置、可用内存等内容 
|   └───object-name               云对象目录
|         │──index.obj.js         云对象代码
|         └──package.json         包含云对象的配置信息，可用内存等内容 
```

#### 2.3 响应体规范

`uniCloud响应体规范`（uniCloud response format），是DCloud制定的、服务器给客户端返回json数据的一种建议格式。

云对象、clientDB、uni-id公共模块均支持此规范。

除此之外响应体规范还包含`newToken`字段，用于token的自动续期（云对象接收含有newToken的响应后会自动更新storage内存储的`uni_id_token`及`uni_id_token_expired`，此行为新增于`HBuilderX 3.4.13`）。开发者一般无需关心此数据，uni-app客户端和云端uni-id之间会自动管理token及续期。

`uniCloud响应体`示例如下：

```json
// 失败返回值
{
  "errCode": 'uni-id-account-banned',
  "errMsg": '账号被禁用'
}
```

```json
// 成功返回值
{
  "errCode": 0,
  "errMsg": '登录成功',
  "uid": 'xxx', // 其他信息
  "newToken": { // 用于下发新token给客户端
	  "token": 'xxx',
	  "tokenExpired": 'xxx'
  }
}
```

### 3.云对象

云对象就是将多个云函数放到对象中暴露出去，在上面我们已经尝试创建了一个云对象，具体细节请查看 uniCloud官方文档。

### 4.普通云函数

callFunction方式云函数，也称之为普通云函数。

uni-app的前端代码，不再执行`uni.request`联网，而是通过`uniCloud.callFunction`调用云函数。

callFunction方式避免了服务器提供域名，不暴露固定ip，减少被攻击的风险。

- 对于uni-app前端而言，使用云对象会比使用callFunction云函数方式更为简单清晰。
- 但对于非uni-app前端调用的场景，比如5+App、外部应用、服务器要调用云函数，或者uniCloud定时任务，此时不适合使用云对象，还是需要云函数。

`uniCloud.callFunction`可以在uni-app前端执行，也可以在uniCloud云函数中执行。也就是前端和云端都可以调用另一个云函数。

`callFunction`方法的参数和返回值如下：

#### 4.1 callFunction

`uniCloud.callFunction`需要一个json对象作为参数，其中包含2个字段

| 字段 |  类型  | 必填 |         说明         |
| :--: | :----: | :--: | :------------------: |
| name | String |  是  |      云函数名称      |
| data | Object |  否  | 客户端需要传递的参数 |

**返回json**

|   字段    |      类型      |                             说明                             |
| :-------: | :------------: | :----------------------------------------------------------: |
|  result   |     Object     |                 云函数中代码return的返回结果                 |
| requestId |     String     | 云函数请求序列号，用于错误排查，可以在uniCloud web控制台的云函数日志中查到 |
|  header   |     Object     |                       服务器header信息                       |
|  errCode  | Number或String |                         服务器错误码                         |
|  success  |      bool      |                         执行是否成功                         |

**前端示例代码**

假使云服务空间有一个云函数名为“hellocf”，那么前端可以通过如下方式调用这个云函数

```javascript
// promise方式
uniCloud.callFunction({
    name: 'hellocf',
    data: { a: 1 }
  })
  .then(res => {});

// callback方式
uniCloud.callFunction({
	name: 'hellocf',
	data: { a: 1 },
	success(){},
	fail(){},
	complete(){}
});
```

#### 4.2 入参

客户端callFunction调用云函数时，云函数通过入参接收客户端数据，通过头信息上下文获取客户端信息，经过业务逻辑处理后给客户端返回结果。

假使客户端代码调用云函数`hellocf`，并传递了`{a:1,b:2}`的数据，

那么云函数侧的代码如下，将传入的两个参数求和并返回客户端：

```js
// hellocf云函数index.js入口文件代码
'use strict';
exports.main = async (event, context) => {
	//event为客户端上传的参数
	let c = event.a + event.b
	return {
		sum: c
	} // 通过return返回结果给客户端
}
```

云函数的传入参数有两个，一个是`event`对象，一个是`context`对象。

- `event`指的是触发云函数的事件。当客户端调用云函数时，`event`就是客户端调用云函数时传入的参数。
- `context` 对象包含了本次请求的上下文，包括客户端的ip、ua、appId等信息，以及云函数的环境情况、调用来源source等信息。

**4.2.1 event对象**

event对象，可以理解为客户端上行参数中的json对象。在使用`uni-id`且登录成功后，会自动多添加了一个`uniIdToken`属性。

可以通过 `event.uniIdToken` 获取 uni-id 的 token，如下：

```js
'use strict';
exports.main = async (event, context) => {
  let token = event.uniIdToken // 客户端uni-id token
}
```

所以开发者需注意，自己上行的参数对象不要包含uniIdToken属性，避免同名冲突。

**入参的体积限制**

云函数上行的参数内容不能传太大。

- 阿里云event大小不可超过1MB
- 腾讯云event大小不可超过5MB

**4.2.2 context对象**

- `context` 对象包含了本次请求的上下文，包括客户端的ip、ua、appId等信息，以及云函数的环境情况、调用来源source等信息。

context对象的属性清单如下：

| 属性名称      | 类型   | 说明                                                         |
| ------------- | ------ | ------------------------------------------------------------ |
| SPACEINFO     | object | 服务空间信息                                                 |
| \|- spaceId   | string | 服务空间id                                                   |
| \|- provider  | string | 服务空间供应商：aliyun\|tencent                              |
| SOURCE        | string | 云函数调用来源 [详见](https://uniapp.dcloud.net.cn/uniCloud/cf-callfunction.html#context-source) |
| FUNCTION_NAME | string | 获取云函数名称                                               |
| FUNCTION_TYPE | string | 获取云函数类型，对于云函数来说，这里一定会返回`cloudfunction`，新增于HBuilderX 3.5.1。 |
| CLIENTIP      | string | 客户端IP。如果调用来源是其他服务器，会返回调用方的ip         |
| CLIENTUA      | string | 客户端userAgent。注意非本地运行环境下客户端getSystemInfoSync也会获取ua参数并上传给云函数，但是云函数会从http请求头里面获取ua而不是clientInfo里面的ua |
| uniIdToken    | string | 客户端uni-id token字符串，新增于HBuilderX 3.5.1。            |
| requestId     | string | 当前请求id，新增于HBuilderX 3.5.5。                          |

为了保持向下兼容，新版并没有去掉老版那些大写属性的客户端信息，但文档标注为以废弃。对于新版HBuilderX用户而言，请使用 `uni.getSystemInfo` 返回的驼峰属性。

示例：

```js
'use strict';
exports.main = async (event, context) => {
	//event为客户端上传的参数
  //...
  //context中可获取客户端调用的上下文
  let clientIP = context.CLIENTIP // 客户端ip信息
  let spaceInfo = context.SPACEINFO // 当前环境信息 {spaceId:'xxx',provider:'tencent'}
  let source = context.SOURCE // 云函数调用来源
  // 以下属性只有使用uni-app以callFunction方式调用才能获取，即context.SOURCE=="client"，调用方不是uni-app客户端则没有相应数据
  let appid = context.appId // manifest.json中配置的appid
  let deviceId = context.deviceId // 客户端标识，新增于HBuilderX 3.1.0，同uni-app客户端getSystemInfo接口获取的deviceId
	//... //其它业务代码
}
```

#### 4.3 返回格式

普通云函数返回给客户端的是json格式数据。返回结果包裹在result下。

前端发起callFunction到云端接收参数并响应，然后反馈前端，前端接收，完整流程代码如下：

```js
// 客户端发起调用云函数hellocf，并传入data数据
uniCloud.callFunction({
	name: 'hellocf',
	data: {a:1,b:2}
}).then((res) => {
	console.log(res.result) // 结果是 {sum: 3}
}).catch((err) => {
	console.error(err)
})
```

```js
// 云函数hellocf的代码，接收到客户端传递的data，并对其中a和b相加返回给客户端
'use strict';
exports.main = async (event, context) => {
	//event为客户端上传的参数
	console.log('event : ', event)
	//返回数据给客户端
	return {sum : event.a + event.b}
};
```

那么客户端得到的res结构如下

```json
{
	"errCode": 0,
	"errMsg": "",
	"header": {
		// xxx
	}
	"requestId": "ac1403831656185314624173902"
	"result": {sum: 3}
	"success": true
}
```

其中`result`是开发者云函数代码返回的数据，其余是云平台返回的。

注意：HBuilderX本地运行云函数时，如果没有系统错误，则只返回`result`，其他需要在云端运行云函数才会返回。

- errCode为0时，success也是true。
  - 表示云函数在系统层面没有运行错误。可以正常返回result。前端callFunction会进入success回调
  - 如果开发者的业务有报错，可以在 result 里返回 errCode 和 errMsg。
- errCode不为0时，success为false。
  - 表示云函数在系统层面报错了，比如联网失败、云函数超时、内存超限等错误。前端callFunction会进入fail回调
  - 发生系统错误时 result 里无法正常返回业务错误。errCode不为0时，还会返回errMsg。
- requestId是云函数的请求id，线上运行时，可以在uniCloud web控制台的云函数日志中查看运行日志。
- header是云厂商的一些信息，阿里云和腾讯云不同，上面示例代码是阿里云的header。





## 云数据库

### 1 基础入门

#### 1.1 基础概念

`uniCloud`提供了一个 JSON 格式的文档型数据库。顾名思义，数据库中的每条记录都是一个 JSON 格式的文档。

它是 nosql 非关系型数据库，如果您之前熟悉 sql 关系型数据库，那么两者概念对应关系如下表：

| 关系型              | JSON 文档型                                             |
| :------------------ | :------------------------------------------------------ |
| 数据库 database     | 数据库 database                                         |
| 表 table            | 集合 collection。但行业里也经常称之为“表”。无需特意区分 |
| 行 row              | 记录 record / doc                                       |
| 字段 column / field | 字段 field                                              |
| 使用sql语法操作     | 使用MongoDB语法或jql语法操作                            |

- 一个`uniCloud`服务空间，有且只有一个数据库；
- 一个数据库可以有多个表；
- 一个表可以有多个记录；
- 一个个记录可以有多个字段。

例如，数据库中有一个表，名为user，存放用户信息。表user的数据内容如下：

```json
{"name":"张三","tel":"13900000000"}
{"name":"李四","tel":"13911111111"}
```

上述数据中，每行数据表示一个用户的信息，被称之为“记录(record/doc)”。name和tel称之为“字段(field)”。而“13900000000”则是第一条记录的字段tel的值。

每行记录，都是一个完整的json文档，获取到记录后可以使用常规json方式操作。但表并非json文档，表是多个json文档的汇总，获取表需要使用专门的API。

与关系型数据库的二维表格式不同，json文档数据库支持不同记录拥有不同的字段、支持多层嵌套数据。

仍然以user表举例，要在数据库中存储每个人的每次登录时间和登录ip，则变成如下：

```json
{
	"name":"张三","tel":"13900000000",
	"login_log":[
		{"login_date":1604186605445,"login_ip":"192.168.1.1"},
		{"login_date":1604186694137,"login_ip":"192.168.1.2"}
	]
}
{"name":"李四","tel":"13911111111"}
```

上述数据表示张三登录了2次，login_date里的值是时间戳(timestamp)格式，在数据库内timestamp就是一个数字类型的数据。而李四没有登录过。

可以看出json文档数据库相对于关系型数据库的灵活，李四可以没有login_log字段，也可以有这个字段但登录次数记录与张三不同。

*此处仅为举例，实际业务中，登录日志单独存放在另一个表更合适*

对于初学者，如果不了解数据库设计，可以参考[opendb](https://uniapp.dcloud.net.cn/uniCloud/opendb)，已经预置了大量常见的数据库设计。

对于不熟悉传统数据库，但掌握json的js工程师而言，uniCloud的云数据库更亲切，没有传统数据库高昂的学习成本。

在uniCloud web控制台新建表时，在下面的模板中也可以选择各种`opendb`表模板，直接创建。

uniCloud同时支持阿里云和腾讯云，它们的数据库大体相同，有细微差异。阿里云的数据库是mongoDB4.0，腾讯云则使用自研的文档型数据库（兼容mongoDB 4.0版本）。uniCloud基本抹平了不同云厂商的差异，有差异的部分会在文档中单独标注。

#### 1.2 创建表

1. 打开 uniCloud web控制台 [https://unicloud.dcloud.net.cn/](https://unicloud.dcloud.net.cn/)
2. 创建或进入一个已存在的服务空间，选择 云数据库->云数据库，创建一个新表

比如我们创建一个简历表，名为 `resume`。点击上方右侧的 创建 按钮即可。

新建表时，支持选择现成的 [opendb](https://uniapp.dcloud.net.cn/uniCloud/opendb) 表模板，选择一个或多个模板表，可以点击右下方按钮创建。

创建表一共有3种方式：

- 在web控制台创建

- 在HBuilderX中，项目根目录/uniCloud/database点右键新建schema，上传时创建

- 在代码中也可以创建表，但不推荐使用。

#### 1.3 数据表组成

每个数据表，包含3个部分：

- data：数据内容
- index：索引
- schema：数据表格式定义

在uniCloud的web控制台可以看到一个数据表的3部分内容。



1. 数据内容

data，就是存放的数据记录(record)。里面是一条一条的json文档。

record可以增删改查、排序统计。后续有API介绍。

可以先在 web控制台 为之前的 `resume` 表创建一条记录。

输入一个json

```json
{
    "name": "张三",
    "birth_year": 2000,
    "tel": "13900000000",
    "email": "zhangsan@zhangsan.com",
    "intro": "擅于学习，做事严谨"
}
```

创建一条新记录，是不管在web控制台创建，还是通过API创建，每条记录都会自带一个`_id`字段用以作为该记录的唯一标志。

`_id`字段是每个数据表默认自带且不可删除的字段。同时，它也是数据表的索引。

阿里云使用的是标准的mongoDB，`_id`是自增的，后创建的记录的`_id`总是大于先生成的`_id`。传统数据库的自然数自增字段在多物理机的大型数据库下很难保持同步，大型数据库均使用`_id`这种长度较长、不会重复且仍然保持自增规律的方式。

**腾讯云使用的是兼容mongoDB的自研数据库，`_id`并非自增**

插入/导入数据时也可以自行指定`_id`而不使用自动生成的`_id`，这样可以很方便的将其他数据库的数据迁移到uniCloud云数据库。



2. 数据库索引

所谓索引，是指在数据表的众多字段中挑选一个或多个字段，让数据库引擎优先处理这些字段。

设置为索引的字段，在通过该字段查询(where)或排序(orderBy)时可以获得更快的查询速度。

但设置过多索引也不合适，会造成数据新增和删除变慢。

新建的表，默认只有一个索引`_id`。

一个数据表可以有多个字段被设为索引。

索引分唯一型和非唯一型。

唯一型索引要求整个数据表多个记录的该字段的值不能重复。比如`_id`就是唯一型索引。

假使有2个人都叫“张三”，那么他们在user数据表里的区分就是依靠不同的`_id`来区分。

如果我们要根据name字段来查询，为了提升查询速度，此时可以把name字段设为非唯一索引。

索引内容较多，还有“组合索引”、“稀疏索引”、“地理位置索引”、“TTL索引”等概念。有单独的文档详细讲述索引，另见：[数据库索引](https://uniapp.dcloud.net.cn/uniCloud/db-index)

**注意**

- 如果记录中已经存在多个记录某字段相同的情况，那么将该字段设为唯一型索引会失败。

- 如果已经设置某字段为唯一索引，在新增和修改记录时如果该字段的值之前在其他记录已存在，会失败。

- 假如记录中不存在某个字段，则对索引字段来说其值默认为 null，如果该索引字段设为唯一型索引，则不允许存在两个或以上的该字段为null或不存在该字段的记录。此时需要设置稀疏索引来解决多个null重复的问题

  

3. 数据表格式定义

`DB Schema`是表结构描述。描述数据表有哪些字段、值域类型是什么、是否必填、数据操作权限等很多内容。

因为 MongoDB 的灵活性，理论上`DB Schema`不是必须的，使用传统 MongoDB API 操作数据库不需要`DB Schema`。

但如果使用 JQL，那`DB Schema`就是必须的。

`DB Schema`涉及内容较多，另见文档：[https://uniapp.dcloud.io/uniCloud/schema](https://uniapp.dcloud.net.cn/uniCloud/schema)

#### 1.4 获取数据库对象

想要通过代码操作数据库，第一步要获取服务空间里的数据库对象。

```js
const db = uniCloud.database(); //代码块为cdb
```

js中敲下代码块`cdb`，即可快速输入上述代码。

其中，云函数内使用JQL扩展库时，还需要做一个工作，就是指定操作用户身份。[详见](https://uniapp.dcloud.net.cn/uniCloud/jql-cloud#use-in-object)

```js
// 云函数中JQL使用示例
'use strict';
exports.main = async (event, context) => {
	const dbJQL = uniCloud.databaseForJQL({ // 获取JQL database引用，此处需要传入云函数的event和context，必传
		event,
		context 
	})
	return {
		dbJQL.collection('book').get() // 直接执行数据库操作
	}
};
```

#### 1.5 创建集合/表

- 阿里云

调用add方法，给某数据表新增数据记录时，如果该数据表不存在，会自动创建该数据表。如下代码给table1数据表新增了一条数据，如果table1不存在，会自动创建。

```js
const db = uniCloud.database();
db.collection("table1").add({name: 'Ben'})
```

- 腾讯云

腾讯云提供了专门的创建数据表的API，此API仅支持云函数内运行，不支持clientDB调用。

```js
const db = uniCloud.database();
db.createCollection("table1")
```

**注意**

- 如果数据表已存在，腾讯云调用createCollection方法会报错
- 腾讯云调用collection的add方法不会自动创建数据表，不存在的数据表会报错
- 阿里云没有createCollection方法
- **使用代码方式创建的表没有索引、schema，性能和功能都受影响，不建议使用这种方式**

### 2 云函数操作数据库

#### 2.1 获取集合的引用

```js
const db = uniCloud.database();
// 获取 `user` 集合的引用
const collection = db.collection('user');
```



1. 集合 Collection

通过 `db.collection(name)` 可以获取指定集合的引用，在集合上可以进行以下操作

| 类型     | 接口    | 说明                                                         |
| -------- | ------- | ------------------------------------------------------------ |
| 写       | add     | 新增记录（触发请求）                                         |
| 计数     | count   | 获取符合条件的记录条数                                       |
| 读       | get     | 获取集合中的记录，如果有使用 where 语句定义查询条件，则会返回匹配结果集 (触发请求) |
| 引用     | doc     | 获取对该集合中指定 id 的记录的引用                           |
| 查询条件 | where   | 通过指定条件筛选出匹配的记录，可搭配查询指令（eq, gt, in, ...）使用 |
|          | skip    | 跳过指定数量的文档，常用于分页，传入 offset                  |
|          | orderBy | 排序方式                                                     |
|          | limit   | 返回的结果集(文档数量)的限制，有默认值和上限值               |
|          | field   | 指定需要返回的字段                                           |

查询及更新指令用于在 `where` 中指定字段需满足的条件，指令可通过 `db.command` 对象取得。



2. 获取记录

通过 `db.collection(collectionName).doc(docId)` 可以获取指定集合上指定 _id 的记录的引用，在记录上可以进行以下操作

| 接口 | 说明   |                                                              |
| ---- | ------ | ------------------------------------------------------------ |
| 写   | update | 局部更新记录(触发请求)只更新传入的字段。如果被更新的记录不存在，会直接返回更新失败 |
|      | set    | 覆写记录;会删除操作的记录中的所有字段，创建传入的字段。如果操作的记录不存在，会自动创建新的记录 |
|      | remove | 删除记录(触发请求)                                           |
| 读   | get    | 获取记录(触发请求)                                           |

doc(docId)方法的参数只能是字符串，即数据库默认的_id字段。

如需要匹配多个`_id`的记录，应使用where方法。可以在where方法里用in指令匹配一个包含`_id`的数组。

新增文档时数据库会自动生成_id字段，也可以自行将_id设置为其他值



3. 查询筛选指令

以下指令挂载在 `db.command` 下

| 类型     | 接口 | 说明                               |
| -------- | ---- | ---------------------------------- |
| 比较运算 | eq   | 字段等于 ==                        |
|          | neq  | 字段不等于 !=                      |
|          | gt   | 字段大于 >                         |
|          | gte  | 字段大于等于 >=                    |
|          | lt   | 字段小于 <                         |
|          | lte  | 字段小于等于 <=                    |
|          | in   | 字段值在数组里                     |
|          | nin  | 字段值不在数组里                   |
| 逻辑运算 | and  | 表示需同时满足指定的所有条件       |
|          | or   | 表示需同时满足指定条件中的至少一个 |

如果你熟悉SQL，可查询[mongodb与sql语句对照表 (opens new window)](https://blog.csdn.net/xinghebuluo/article/details/7012788/)进行学习。



4. 字段更新指令

以下指令挂载在 `db.command` 下

| 类型 | 接口    | 说明                             |
| ---- | ------- | -------------------------------- |
| 字段 | set     | 设置字段值                       |
|      | remove  | 删除字段                         |
|      | inc     | 加一个数值，原子自增             |
|      | mul     | 乘一个数值，原子自乘             |
|      | push    | 数组类型字段追加尾元素，支持数组 |
|      | pop     | 数组类型字段删除尾元素，支持数组 |
|      | shift   | 数组类型字段删除头元素，支持数组 |
|      | unshift | 数组类型字段追加头元素，支持数组 |



#### 2.2 新增文档

**方法1： collection.add(data)**

参数说明

| 参数 | 类型            | 必填 | 说明                                     |
| ---- | --------------- | ---- | ---------------------------------------- |
| data | object \| array | 是   | {_id: '10001', 'name': 'Ben'} _id 非必填 |

示例：

```js
const collection = db.collection('unicloud-test')
// 单条插入数据
let res = await collection.add({
  name: 'Ben'
})
// 批量插入数据
let res = await collection.add([{
  name: 'Alex'
},{
  name: 'Ben'
},{
  name: 'John'
}])
```



**方法2： collection.doc().set(data)**

也可通过 `set` 方法新增一个文档，需先取得文档引用再调用 `set` 方法。 如果文档不存在，`set` 方法会创建一个新文档。

**参数说明**

| 参数 | 类型   | 必填 | 说明                              |
| ---- | ------ | ---- | --------------------------------- |
| data | object | 是   | 更新字段的Object，{'name': 'Ben'} |

**响应参数**

| 参数       | 类型   | 说明                                        |
| ---------- | ------ | ------------------------------------------- |
| updated    | Number | 更新成功条数，数据更新前后没变化时也会返回1 |
| upsertedId | String | 创建的文档id                                |

```js
let res = await collection.doc('doc-id').set({
  name: "Hey"
});
```



#### 2.3 查询文档

支持 `where()`、`limit()`、`skip()`、`orderBy()`、`get()`、`field()`、`count()` 等操作。

只有当调用`get()`时才会真正发送查询请求。

limit，即返回记录的最大数量，默认值为100，也就是不设置limit的情况下默认返回100条数据。

设置limit有最大值，腾讯云限制为最大1000条，阿里云限制为最大500条。

如需查询更多数据，需要分页多次查询。

**get响应参数**

| 参数 | 类型  | 说明         |
| ---- | ----- | ------------ |
| data | Array | 查询结果数组 |

**添加查询条件**

`collection.where(obj)`



**在聚合操作中请使用match**

设置过滤条件，where 可接收对象作为参数，表示筛选出拥有和传入对象相同的 key-value 的文档。比如筛选出所有类型为计算机的、内存为 8g 的商品：

```js
let res = await db.collection('goods').where({
  category: 'computer',
  type: {
    memory: 8,
  }
}).get()
```

如果要表达更复杂的查询，可使用高级查询指令，比如筛选出所有内存大于 8g 的计算机商品：

```js
const dbCmd = db.command // 取指令
db.collection('goods').where({
  category: 'computer',
  type: {
    memory: dbCmd.gt(8), // 表示大于 8
  }
})
```

在SQL里使用字符串表达式操作。但在NOSQL中使用json操作。这使得 等于 的表达，从 `=` 变成了 `:`；而大于的表达，从 `>` 变成了 `dbCmd.gt()`

所有的比较符，详见[表格(opens new window)](https://uniapp.dcloud.io/uniCloud/cf-database-dbcmd?id=查询筛选指令-query-command)

`where` 还可以使用正则表达式来查询文档，比如一下示例查询所有`name`字段以ABC开头的用户

```js
db.collection('user').where({
  name: new RegExp('^ABC')
})
```



**按照数组内的值查询**

mongoDB内按照数组内的值查询可以使用多种写法，以下面的数据为例

```js
{
  arr:[{
    name: 'item-1',
  },{
    name: 'item-2',
  }]
}

{
  arr:[{
    name: 'item-3',
  },{
    name: 'item-4',
  }]
}
```

如果想查询arr内第一个元素的name为item-1的记录可以使用如下写法

```js
const res = await db.collection('test').where({
  'arr.0.name': 'item-1'
})

res = {
  data:[{
    arr:[{
      name: 'item-1',
    },{
      name: 'item-2',
    }]
  }]
}
```

如果想查询arr内某个元素的name为item-1的记录（可以是数组内的任意一条name为item-1）可以使用如下写法

```js
const res = await db.collection('test').where({
  'arr.name': 'item-1'
})

res = {
  data:[{
    arr:[{
      name: 'item-1',
    },{
      name: 'item-2',
    }]
  }]
}
```



**获取查询数量**

`collection.count()`

```js
let res = await db.collection('goods').where({
  category: 'computer',
  type: {
    memory: 8,
  }
}).count()响应参数
```

- 数据量很大的情况下，带条件运算count全表的性能会很差，尽量使用其他方式替代，比如新增一个字段专门用来存放总数。不加条件时count全表不存在性能问题。



**设置记录数量**

`collection.limit(number)`

```js
let res = await collection.limit(1).get() // 只返回第一条记录
```

- limit不设置的情况下默认返回100条数据；设置limit有最大值，腾讯云限制为最大1000条，阿里云限制为最大500条。



**设置起始位置**

`collection.skip(number)`参数说明

```js
let res = await collection.skip(4).get() //跳过指定的位置，从位置之后返回数据
```

- 数据量很大的情况下，skip性能会很差，尽量使用其他方式替代，参考：[skip性能优化](https://uniapp.dcloud.net.cn/uniCloud/db-performance#skip)**



**对结果排序**

`collection.orderBy(field, orderType)`

参数说明

| 参数      | 类型   | 必填 | 说明                                |
| --------- | ------ | ---- | ----------------------------------- |
| field     | string | 是   | 排序的字段                          |
| orderType | string | 是   | 排序的顺序，升序(asc) 或 降序(desc) |

如果需要对嵌套字段排序，需要用 "点表示法" 连接嵌套字段，比如 style.color 表示字段 style 里的嵌套字段 color。

同时也支持按多个字段排序，多次调用 orderBy 即可，多字段排序时的顺序会按照 orderBy 调用顺序先后对多个字段排序

```js
let res = await collection.orderBy("name", "asc").get()
```

- 排序字段存在多个重复的值时排序后的分页结果，可能会出现某条记录在上一页出现又在下一页出现的情况。这时候可以通过指定额外的排序条件比如`.orderBy("name", "asc").orderBy("_id", "asc")`来规避这种情况。



**指定返回字段**

`collection.field(obj)`

从查询结果中，过滤掉不需要的字段，或者指定要返回的字段。

参数说明

| 参数 | 类型   | 必填 | 说明                                                         |
| ---- | ------ | ---- | ------------------------------------------------------------ |
| -    | object | 是   | 过滤字段对象，包含字段名和策略，不返回传false，返回传tru使用示例 |

```js
collection.field({ 'age': true }) //只返回age字段、_id字段，其他字段不返回
```

- field内指定是否返回某字段时，不可混用true/false。即{'a': true, 'b': false}是一种错误的参数格式
- 只有使用{ '_id': false }明确指定不要返回_id时才会不返回_id字段，否则_id字段一定会返回。



**查询指令**

查询指令以dbCmd.开头，包括等于、不等于、大于、大于等于、小于、小于等于、in、nin、and、or。

[uni-app官网 (dcloud.net.cn)](https://uniapp.dcloud.net.cn/uniCloud/cf-database.html#query)

**正则表达式查询**

根据正则表达式进行筛选

例如下面可以筛选出 `version` 字段开头是 "数字+s" 的记录，并且忽略大小写：

```js
// 可以直接使用正则表达式
db.collection('articles').where({
  version: /^\ds/i
})

// 也可以使用new RegExp
db.collection('user').where({
  name: new RegExp('^\\ds', 'i')
})

// 或者使用new db.RegExp，这种方式阿里云不支持
db.collection('articles').where({
  version: new db.RegExp({
    regex: '^\\ds',   // 正则表达式为 /^\ds/，转义后变成 '^\\ds'
    options: 'i'    // i表示忽略大小写
  }) 
})
```



**查询数组字段**

[uni-app官网](https://uniapp.dcloud.net.cn/uniCloud/cf-database.html#querywitharr)



#### 2.4 删除文档

**方式1 通过指定文档ID删除**

collection.doc(_id).remove()

```js
// 清理全部数据
let res = await collection.get()
res.data.map(async(document) => {
  return await collection.doc(document.id).remove();
});
```

**方式2 条件查找文档然后直接批量删除**

collection.where().remove()

```js
// 删除字段a的值大于2的文档
const dbCmd = db.command
let res = await collection.where({
  a: dbCmd.gt(2)
}).remove()

// 清理全部数据
const dbCmd = db.command
let res = await collection.where({
  _id: dbCmd.exists(true)
}).remove()
```

响应参数

| 字段    | 类型   | 必填 | 说明           |
| ------- | ------ | ---- | -------------- |
| deleted | Number | 否   | 删除的记录数量 |

示例：判断删除成功或失败，打印删除的记录数量

```js
const db = uniCloud.database();
db.collection("table1").doc("5f79fdb337d16d0001899566").remove()
	.then((res) => {
		console.log("删除成功，删除条数为: ",res.deleted);
	})
	.catch((err) => {
		console.log( err.message )
	})
	.finally(() => {
		
	})
```



#### 2.5 更新文档

**更新指定文档**

**使用腾讯云时更新方法必须搭配doc、where方法使用，`db.collection('test').update()`会报如下错误：`param should have required property 'query'`**

`collection.doc().update(data)`

> 未使用set、remove更新操作符的情况下，此方法不会删除字段，仅将更新数据和已有数据合并。

**参数说明**

| 参数 | 类型   | 必填 | 说明                                         |
| ---- | ------ | ---- | -------------------------------------------- |
| data | object | 是   | 更新字段的Object，{'name': 'Ben'} _id 非必填 |

```js
let res = await collection.doc('doc-id').update({
  name: "Hey",
  count: {
    fav: 1
  }
});
```

```json
// 更新前
{
  "_id": "doc-id",
  "name": "Hello",
  "count": {
    "fav": 0,
    "follow": 0
  }
}

// 更新后
{
  "_id": "doc-id",
  "name": "Hey",
  "count": {
    "fav": 1,
    "follow": 0
  }
}
```

更新数组时，已数组下标作为key即可，比如以下示例将数组arr内下标为1的值修改为 uniCloud

```js
let res = await collection.doc('doc-id').update({
  arr: {
    1: "uniCloud"
  }
})
```

```json
// 更新前
{
  "_id": "doc-id",
  "arr": ["hello", "world"]
}
// 更新后
{
  "_id": "doc-id",
  "arr": ["hello", "uniCloud"]
}
```



**更新文档，如果不存在则创建**

`collection.doc().set(obj)`

**注意：**

> 此方法会覆写已有字段，需注意与`update`表现不同，比如以下示例执行`set`之后`follow`字段会被删除

```js
let res = await collection.doc('doc-id').set({
  name: "Hey",
  count: {
    fav: 1
  }
})
```

```json
// 更新前
{
  "_id": "doc-id",
  "name": "Hello",
  "count": {
    "fav": 0,
    "follow": 0
  }
}

// 更新后
{
  "_id": "doc-id",
  "name": "Hey",
  "count": {
    "fav": 1
  }
}
```



**批量更新文档**

`collection.update()`

```js
const dbCmd = db.command
let res = await collection.where({name: dbCmd.eq('hey')}).update({
  age: 18,
})
```



**更新并返回更新后的数据**

> 新增于HBuilderX 3.2.0

此接口仅会操作一条数据，有多条数据匹配的情况下会只更新匹配的第一条并返回

**示例**

```js
const db = uniCloud.database()
await db.collection('test').where({
  uid: '1'
}).updateAndReturn({
  score: db.command.inc(2)
})

// 更新前
{
  _id: 'xx',
  uid: '1',
  score: 0
}
// 更新后
{
  _id: 'xx',
  uid: '1',
  score: 2
}

// 接口返回值
{
  updated: 1,
  doc: {
    _id: 'xx',
    uid: '1',
    score: 2
  }
}
```

- 使用updateAndReturn时，不可使用field方法
- 可以在事务中使用，可以使用`transaction.where().updateAndReturn()`以及`transaction.doc().updateAndReturn()`
- 不同于update接口，此接口返回的updated不表示数据真的进行了更新
- 腾讯云暂不支持`doc().updateAndReturn()`的写法可以使用`where().updateAndReturn()`替代



**更新数组内指定下标的元素**

```js
const res = await db.collection('query').doc('1').update({
  // 更新students[1]
  ['students.' + 1]: {
    name: 'wang'
  }
})
```

```js
// 更新前
{
  "_id": "1",
  "students": [
    {
      "name": "zhang"
    },
    {
      "name": "li"
    }
  ]
}

// 更新后
{
  "_id": "1",
  "students": [
    {
      "name": "zhang"
    },
    {
      "name": "wang"
    }
  ]
}
```



**更新数组内匹配条件的元素**

**注意：只可确定数组内只会被匹配到一个的时候使用**

```js
const res = await db.collection('query').where({
	'students.id': '001'
}).update({
  // 将students内id为001的name改为li，$代表where内匹配到的数组项的序号
	'students.$.name': 'li'
})
```

```js
// 更新前
{
  "_id": "1",
  "students": [
    {
      "id": "001",
      "name": "zhang"
    },
    {
      "id": "002",
      "name": "wang"
    }
  ]
}

// 更新后
{
  "_id": "1",
  "students": [
    {
      "id": "001",
      "name": "li"
    },
    {
      "id": "002",
      "name": "wang"
    }
  ]
}
```



**更新操作符**

更多数据库操作符请查看[数据库操作符](https://uniapp.dcloud.net.cn/uniCloud/cf-database.html#dbcmd) [uni-app官网](https://uniapp.dcloud.net.cn/uniCloud/cf-database.html#update-operator)



## 云存储

### 1.概述

云存储的上传方式有3种：

1. web界面：即在web控制台，点击云存储，通过web界面进行文件上传。该管理界面同时提供了资源浏览、删除等操作界面。
2. 客户端API或组件上传：在前端js中编写`uniCloud.uploadFile`，或者使用uni ui的[FilePicker组件](https://ext.dcloud.net.cn/plugin?id=4079)，文件选择+上传均封装完毕。
3. 云函数上传文件到云存储：即在云函数js中编写`uniCloud.uploadFile`

**注意：**

- 前端和云函数端，均有一个相同名称的api：`uniCloud.uploadFile`。请不要混淆。
- 前端还有一个`uni.uploadFile`的API，那个API用于连接非uniCloud的上传使用。请不要混淆。
- 在允许用户上传图片的应用里，违规检测是必不可少的，为此uniCloud提供了内容安全检测模块，可以很方便的实现图片鉴黄等功能。详情参考：[内容安全](https://ext.dcloud.net.cn/plugin?id=5460)

### 2.uni-file-picker

[uni-app官网](https://uniapp.dcloud.net.cn/component/uniui/uni-file-picker.html)

文件选择上传组件，可以选择图片、视频等任意文件并上传到当前绑定的服务空间

注意事项

> 为了避免错误使用，给大家带来不好的开发体验，请在使用组件前仔细阅读下面的注意事项，可以帮你避免一些错误。

- 组件需要依赖 `sass` 插件 ，请自行手动安装
- 如不绑定服务空间，`autoUpload`默认为`false`且不可更改
- 选择文件目前只支持 `H5` 和 `微信小程序平台` ，且 `微信小程序平台` 使用 `wx.chooseMessageFile()`
- v-model 值需要自动上传成功后才会绑定值，一般只用来回显数据

#### 2.1 基础用法

```vue
<uni-file-picker 
	v-model="imageValue" 
	fileMediatype="image" 
	mode="grid" 
	@select="select" 
	@progress="progress" 
	@success="success" 
	@fail="fail" 
/>
<script>
	export default {
		data() {
			return {
				imageValue:[]
			}
		},
		methods:{
			// 获取上传状态
			select(e){
				console.log('选择文件：',e)
			},
			// 获取上传进度
			progress(e){
				console.log('上传进度：',e)
			},
			
			// 上传成功
			success(e){
				console.log('上传成功')
			},
			
			// 上传失败
			fail(e){
				console.log('上传失败：',e)
			}
		}
	}
</script>
```

#### 2.2 上传限制

配置 `file-mediatype` 属性为 `image`，限定只选择图片

配置 `file-extname` 属性为 `'png,jpg'`，限定只选择 `png`和`jpg`后缀的图片

配置 `limit` 属性为 1 ，则最多选择一张图片

```vue
<uni-file-picker 
	v-model="imageValue"  
	file-mediatype="image"
	mode="grid"
	file-extname="png,jpg"
	:limit="1"
	@progress="progress" 
	@success="success" 
	@fail="fail" 
	@select="select"
/>
```

#### 2.3 手动上传

配置 `auto-upload` 属性为 `false` ，可以停止自动上传，通过`ref`调用`upload`方法自行选择上传时机

```vue
<template>
	<view>
		<uni-file-picker  ref="files" :auto-upload="false"/>
		<button @click="upload">上传文件</button>
	</view>
</template>
<script>
	export default {
		data() {},
		methods:{
			upload(){
				this.$refs.files.upload()
			}
		}
	}
</script>
```

#### 2.4 注意

**v-model/value 格式**

三个属性必填，否则影响组件显示

```json
[
	{
		"name":"file.txt",
		"extname":"txt",
		"url":"https://xxxx",
		// ...
	}
]
```

### 3.手写上传

#### 3.1 uni.chooseImage(OBJECT)

从本地相册选择图片或使用相机拍照。https://uniapp.dcloud.io/api/media/video?id=choosemedia)

**OBJECT 参数说明**

| 参数名    | 类型            | 必填 | 说明                                                     | 平台差异说明                              |
| :-------- | :-------------- | :--- | :------------------------------------------------------- | :---------------------------------------- |
| count     | Number          | 否   | 最多可以选择的图片张数，默认9                            | 见下方说明                                |
| sizeType  | `Array<String>` | 否   | original 原图，compressed 压缩图，默认二者都有           | App、微信小程序、支付宝小程序、百度小程序 |
| extension | `Array<String>` | 否   | 根据文件拓展名过滤，每一项都不能是空字符串。默认不过滤。 | H5(HBuilder X2.9.9+)                      |
| success   | Function        | 是   | 成功则返回图片的本地文件路径列表 tempFilePaths           |                                           |
| fail      | Function        | 否   | 接口调用失败的回调函数                                   | 小程序、App                               |
| complete  | Function        | 否   | 接口调用结束的回调函数（调用成功、失败都会执行）         |                                           |

**注：文件的临时路径，在应用本次启动期间可以正常使用，如需持久保存，需在主动调用 [uni.saveFile](https://uniapp.dcloud.net.cn/api/file/file#savefile)，在应用下次启动时才能访问得到。**

**success 返回参数说明**

| 参数          | 类型                           | 说明                                       |
| :------------ | :----------------------------- | :----------------------------------------- |
| tempFilePaths | `Array<String>`                | 图片的本地文件路径列表                     |
| tempFiles     | `Array<Object>`、`Array<File>` | 图片的本地文件列表，每一项是一个 File 对象 |

**File 对象结构如下**

| 参数 | 类型   | 说明                           |
| :--- | :----- | :----------------------------- |
| path | String | 本地文件路径                   |
| size | Number | 本地文件大小，单位：B          |
| name | String | 包含扩展名的文件名称，仅H5支持 |
| type | String | 文件类型，仅H5支持             |

**示例**

```javascript
uni.chooseImage({
	count: 6, //默认9
	sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
	sourceType: ['album'], //从相册选择
	success: function (res) {
		console.log(JSON.stringify(res.tempFilePaths));
	}
});
```

#### 3.2 uni.previewImage(OBJECT)

预览图片。

**OBJECT 参数说明**

| 参数名           | 类型            | 必填         | 说明                                                         | 平台差异说明 |
| :--------------- | :-------------- | :----------- | :----------------------------------------------------------- | :----------- |
| current          | String/Number   | 详见下方说明 | 详见下方说明                                                 |              |
| urls             | `Array<String>` | 是           | 需要预览的图片链接列表                                       |              |
| indicator        | String          | 否           | 图片指示器样式，可取值："default" - 底部圆点指示器； "number" - 顶部数字指示器； "none" - 不显示指示器。 | App          |
| loop             | Boolean         | 否           | 是否可循环预览，默认值为 false                               | App          |
| longPressActions | Object          | 否           | 长按图片显示操作菜单，如不填默认为**保存相册**               | App 1.9.5+   |
| success          | Function        | 否           | 接口调用成功的回调函数                                       |              |
| fail             | Function        | 否           | 接口调用失败的回调函数                                       |              |
| complete         | Function        | 否           | 接口调用结束的回调函数（调用成功、失败都会执行）             |              |

**current 参数说明**

> 1.9.5+ 支持传图片在 urls 中的索引值

current 为当前显示图片的链接/索引值，不填或填写的值无效则为 urls 的第一张。**App平台在 1.9.5至1.9.8之间，current为必填。不填会报错**

注意，当 urls 中有重复的图片链接时：

- 传链接，预览结果始终显示该链接在 urls 中第一次出现的位置。
- 传索引值，在微信/百度/字节跳动小程序平台，会过滤掉传入的 urls 中该索引值之前与其对应图片链接重复的值。其它平台会保留原始的 urls 不会做去重处理。

举例说明：

一组图片 `[A, B1, C, B2, D]`，其中 B1 与 B2 的图片链接是一样的。

- 传 B2 的链接，预览的结果是 B1，前一张是 A，下一张是 C。
- 传 B2 的索引值 3，预览的结果是 B2，前一张是 C，下一张是 D。此时在微信/百度/字节跳动小程序平台，最终传入的 urls 是 `[A, C, B2, D]`，过滤掉了与 B2 重复的 B1。

**longPressActions 参数说明**

| 参数      | 类型            | 必填 | 说明                                             |
| :-------- | :-------------- | :--- | :----------------------------------------------- |
| itemList  | `Array<String>` | 是   | 按钮的文字数组                                   |
| itemColor | String          | 否   | 按钮的文字颜色，字符串格式，默认为"#000000"      |
| success   | Function        | 否   | 接口调用成功的回调函数，详见返回参数说明         |
| fail      | Function        | 否   | 接口调用失败的回调函数                           |
| complete  | Function        | 否   | 接口调用结束的回调函数（调用成功、失败都会执行） |

**success 返回参数说明**

| 参数     | 类型   | 说明                     |
| :------- | :----- | :----------------------- |
| index    | Number | 用户长按图片的索引值     |
| tapIndex | Number | 用户点击按钮列表的索引值 |

**示例**

```javascript
// 从相册选择6张图
uni.chooseImage({
	count: 6,
	sizeType: ['original', 'compressed'],
	sourceType: ['album'],
	success: function(res) {
		// 预览图片
		uni.previewImage({
			urls: res.tempFilePaths,
			longPressActions: {
				itemList: ['发送给朋友', '保存图片', '收藏'],
				success: function(data) {
					console.log('选中了第' + (data.tapIndex + 1) + '个按钮,第' + (data.index + 1) + '张图片');
				},
				fail: function(err) {
					console.log(err.errMsg);
				}
			}
		});
	}
	});
```

#### 3.3 uni.getImageInfo(OBJECT)

获取图片信息。

小程序下获取网络图片信息需先配置download域名白名单才能生效。

**OBJECT 参数说明**

| 参数名   | 类型     | 必填 | 说明                                                         |
| :------- | :------- | :--- | :----------------------------------------------------------- |
| src      | String   | 是   | 图片的路径，可以是相对路径，临时文件路径，存储文件路径，网络图片路径 |
| success  | Function | 否   | 接口调用成功的回调函数                                       |
| fail     | Function | 否   | 接口调用失败的回调函数                                       |
| complete | Function | 否   | 接口调用结束的回调函数（调用成功、失败都会执行）             |

**success 返回参数说明**

| 参数名      | 类型   | 说明                         | 平台差异说明            |
| :---------- | :----- | :--------------------------- | :---------------------- |
| width       | Number | 图片宽度，单位px             |                         |
| height      | Number | 图片高度，单位px             |                         |
| path        | String | 返回图片的本地路径           |                         |
| orientation | String | 返回图片的方向，有效值见下表 | App、小程序、京东小程序 |
| type        | String | 返回图片的格式               | App、小程序、京东小程序 |

**示例**

```javascript
uni.chooseImage({
	count: 1,
	sourceType: ['album'],
	success: function (res) {
		uni.getImageInfo({
			src: res.tempFilePaths[0],
			success: function (image) {
				console.log(image.width);
				console.log(image.height);
			}
		});
	}
});
```

#### 3.4 uploadFile(Object object)

直接上传文件到云存储。

客户端上传文件到云函数、云函数再上传文件到云存储，这样的过程会导致文件流量带宽耗费较大。所以一般上传文件都是客户端直传。

**阿里云单文件大小限制为100M，腾讯云单文件最大为5G**

**[各个小程序平台运行时，网络相关的 API 在使用前需要配置域名白名单。](https://uniapp.dcloud.io/uniCloud/publish.html#useinmp)[参考](https://uniapp.dcloud.io/uniCloud/publish.html#useinmp)**

**请求参数**

|      参数名      |   类型   | 必填 | 默认值 |                             说明                             | 平台差异说明 |
| :--------------: | :------: | :--: | :----: | :----------------------------------------------------------: | :----------: |
|     filePath     |  String  |  是  |   -    |                       要上传的文件对象                       |      -       |
|    cloudPath     |  String  |  是  |   -    | 使用腾讯云时，表示文件的绝对路径，包含文件名。使用阿里云时，`cloudPath`为云端文件名 |      -       |
|     fileType     |  String  |  -   |   -    | 文件类型，支付宝小程序、钉钉小程序必填，可选image、video、audio |      -       |
| onUploadProgress | Function |  否  |   -    |                         上传进度回调                         |              |

**响应参数**

|   字段    |  类型  |                  说明                   |
| :-------: | :----: | :-------------------------------------: |
|  fileID   | String | 文件唯一 ID，用来访问文件，建议存储起来 |
| requestId | String |        请求序列号，用于错误排查         |

**示例代码**

```js
//前端代码
uni.chooseImage({
	count: 1,
	success(res) {
		console.log(res);
		if (res.tempFilePaths.length > 0) {
			let filePath = res.tempFilePaths[0]
			//进行上传操作
			const result = await uniCloud.uploadFile({
				filePath: filePath,
        		 cloudPath: Date.now() + '.jpg',
				onUploadProgress: function(progressEvent) {
			          console.log(progressEvent);
			          var percentCompleted = Math.round(
			            (progressEvent.loaded * 100) / progressEvent.total
			          );
			        }
			});
	}
});
```

**Tips**

- 阿里云返回的fileID为链接形式可以直接使用，腾讯云返回的为cloud://形式，如需展示需要调用getTempFileURL获取链接
- uniCloud.uploadFile 只有上传，没有文件选择。uni ui的封装了[FilePicker组件 (opens new window)](https://ext.dcloud.net.cn/plugin?id=4079)，从选择到上传一条龙。