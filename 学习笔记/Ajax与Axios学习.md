## Ajax学习

### Ajax初识

Ajax是前后端用来进行交互的一种主流方式，其实现主要是通过创建 **XMLHttpRequest实例** 来发送 HTTP 请求。

```js
// ajax简单使用
const xhr = new XMLHttpRequest(); // 创建实例

xhr.open('GET','http://www.example.org/example.txt'); // 确定请求方法和地址
xhr.send();	// 发送

xhr.onreadystatechange = function() { // 监听状态变化，接收返回
    if(xhr.readyState === 4){
        if(xhr.status >= 200 && xhr.status < 300){
            console.log(xhr.response);
        }
    }
}
```



### 实例上的相关属性

创建的实例xhr上具有许多的属性，大部分都是和返回内容相关。下面是一些常用的：

- xhr.**status**  		响应数字状态码
- xhr.**readyState**      实例当前状态
- xhr.**response**        返回响应的正文
- xhr.**responseType**    返回响应的类型，可以修改（'arraybuffer','blob','json','text'等）
- xhr.**timeout**         请求发送前设置超时时间，超时就停止请求



### readyState属性

实例上的readyState属性返回一个 XMLHttpRequest 代理当前所处的状态，当状态码等于4时，表示请求返回内容已经可以读取到，可以在此时查看内容。

| 状态值 |     描述  |
|-------|-----------|
|  0    | 代理被创建，但尚未调用 open() 方法。|
|  1    |open() 方法已经被调用。|
|  2    |send() 方法已经被调用，并且头部和状态已经可获得。|
|  3    |下载中；responseText 属性已经包含部分数据。|
| 4   |下载操作已完成。|



### 解决浏览器缓存问题

当我们使用ajax发送请求时，如果多次对一个地址获取数据，即url没有改变，那么每次获取到的数据都相同。那是因为ajax为了提高效率，会缓存请求数据，当检测到二次发送相同地址的请求时，就会直接读取缓存数据导致每次数据都一样。

解决办法：

- 在`ajax`发送请求前加上 `xhr.setRequestHeader("If-Modified-Since","0")`
- 在`ajax`发送请求前加上 `xhr.setRequestHeader("Cache-Control","no-cache")`
- 在url地址上拼接一个随机数或时间戳，使每次地址都不同



### 请求的超时，终止，异常

ajax实例上存在许多方法，调用他们可以控制请求。下面是一些常用的：

- **load事件**：请求完成时会触发
- **error事件**：请求出错时会触发
- **abort事件**：请求中止时会触发，调用xhr.abort()主动终止请求
- **readyStateChange事件**：请求的状态变化时会触发
- **timeout事件**：请求超时时触发



### fetch方法

除了创建Ajax实例，浏览器还提供了fetch方法来发送请求，fetch发送的也是Ajax请求。此方法会返回一个 promise，这个 promise 会在请求响应后被 resolve，并传回 [`Response`](https://developer.mozilla.org/zh-CN/docs/Web/API/Response) 对象。

`Promise<Response> fetch(url[, init])`

第二个参数是一个可选的配置对象，包括`method`,`headers`，`body`等对请求的配置。





## Axios学习

### Axios初识

axios是目前前端最流行的请求库，在vue和react项目中都被经常使用。其本质就是**对ajax的封装**。



### 常见请求方式

1. `GET`:从服务器读取数据 查(R)
2. `post`：向服务器添加新的数据 增
3. `put`: 更新服务器端已存在的数据 
4. `delete`：删除服务器的数据 删



### 请求参数

`query参数` 参数包含在请求地址中 格式为/xxxx?name=tom&age=18
`param参数` 参数包含在请求地址中 格式为 /xxxx/tom/18
`请求体参数` 参数包含在请求体中 ，注意**get请求没有请求体**

请求体参数格式包括urlencoded格式（对应请求头：Content-Type:application/x-www-form-urlencoded）  
json格式（对应请求头：Content-Type:application/x-www-form-urlencoded）两种。



### 请求的分类

http请求可以分为**一般请求**和**ajax请求**。

- 一般请求:浏览器一般会直接显示响应体数据,也就是我们常说的刷新/跳转页面
- ajax请求:浏览器不会对界面进行任何更新操作,只是调用监视的回调函数并传入响应相关数据 （ajax实例，fetch）



### axios使用

axios请求会返回一个promise，可以设置成功和失败的回调。

```js
// axios一般使用
axios({
  url: 'http://localhost:5000/persons',
  method: 'post',
  params: { id: personId.value } //此处写的是params 但携带的是query参数,自动拼接到路径上
  //data:{a:3,d:3}, //配置请求体参数 (json)
	//data:'e=5&f=6' //配置请求体参数（urlencoded）
  //...
}).then()
```



### axios默认配置项

我们可以在全局定义一些axios常用的配置项，这样axios实例都会自动带上这些配置。[全部配置](https://www.axios-http.cn/docs/req_config)

使用 `axios.defaults.xxx` 来进行配置，请求实例上的配置优先级高于默认配置。

```js
axios.defaults.timeout = 2000
axios.defaults.headers = {name:atjm}
axios.defualts.baseURL='http://localhost:5000'
```



### 创建axios实例

使用 `axios.create（）`可以创建多个实例，这样就可以给每个实例配置不同的配置项。

```js
const instance = axios.create({
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
})
```



### 拦截器

```js
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 2xx 范围内的状态码都会触发该函数。
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 超出 2xx 范围的状态码都会触发该函数。
    // 对响应错误做点什么
    return Promise.reject(error);
  })
```



### 批量请求

调用`axios.all`方法可以实现批量发送请求，其本质也是使用了`Promise.all()`方法。

```js
 axios.all([
    axios.get('http://localhost:5050/test'),
    axios.get('http:'),
    axios.get('http://localhost:5050/test3')
]).then(
    response => { console.log('请求成功了', response.data), },
    error => { console.log('请求失败了', error) }
)
```



### 取消请求

axios支持通过创建一个 [`AbortController`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) 取消请求：

```js
const controller = new AbortController();

axios.get('/foo/bar', {
   // 设置signal
   signal: controller.signal
}).then(function(response) {
   //...
});
// 取消请求
controller.abort()
```

