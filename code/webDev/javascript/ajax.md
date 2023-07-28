---
title: Ajax 笔记
icon: ajax
date: 2022-11-28
category:
  - JS
  - 浏览器
  - HTTP
tag:
  - ajax
---

## Ajax初识

Ajax是前后端用来进行交互的一种主流方式，其实现主要是通过创建 **XMLHttpRequest实例** 来发送 HTTP 请求。

```js
// ajax简单使用
const xhr = new XMLHttpRequest(); // 创建实例

xhr.open('GET','http://www.example.org/example.txt'); // 确定请求方法和地址
xhr.send();	// 发送，此处若是post请求可放请求体数据

xhr.onreadystatechange = function() { // 监听状态变化，接收返回
    if(xhr.readyState === 4){
        if(xhr.status >= 200 && xhr.status < 300){
            console.log(xhr.response);
        }
    }
}
```



## 实例上的相关属性

创建的实例xhr上具有许多的属性，大部分都是和返回内容相关。下面是一些常用的：

- xhr.**status**  		响应数字状态码
- xhr.**readyState**      实例当前状态
- xhr.**response**        返回响应的正文
- xhr.**responseType**    返回响应的类型，可以修改（'arraybuffer','blob','json','text'等）
- xhr.**timeout**         请求发送前设置超时时间，超时就停止请求



## readyState属性

实例上的readyState属性返回一个 XMLHttpRequest 代理当前所处的状态，当状态码等于4时，表示请求返回内容已经可以读取到，可以在此时查看内容。

| 状态值 |     描述  |
|-------|-----------|
|  0    | 代理被创建，但尚未调用 open() 方法。|
|  1    |open() 方法已经被调用。|
|  2    |send() 方法已经被调用，并且头部和状态已经可获得。|
|  3    |下载中；responseText 属性已经包含部分数据。|
| 4   |下载操作已完成。|



## 解决浏览器缓存问题

当我们使用ajax发送请求时，如果多次对一个地址获取数据，即url没有改变，那么每次获取到的数据都相同。那是因为ajax为了提高效率，会缓存请求数据，当检测到二次发送相同地址的请求时，就会直接读取缓存数据导致每次数据都一样。

解决办法：

- 在`ajax`发送请求前加上 `xhr.setRequestHeader("If-Modified-Since","0")`
- 在`ajax`发送请求前加上 `xhr.setRequestHeader("Cache-Control","no-cache")`
- 在url地址上拼接一个随机数或时间戳，使每次地址都不同



## 请求的超时，终止，异常

ajax实例上存在许多方法，调用他们可以控制请求。下面是一些常用的：

- **load事件**：请求完成时会触发
- **error事件**：请求出错时会触发
- **abort事件**：请求中止时会触发，调用xhr.abort()主动终止请求
- **readyStateChange事件**：请求的状态变化时会触发
- **timeout事件**：请求超时时触发



## fetch方法

除了创建Ajax实例，浏览器还提供了fetch方法来发送请求，fetch发送的也是Ajax请求。此方法会返回一个 promise，这个 promise 会在请求响应后被 resolve，并传回 [`Response`](https://developer.mozilla.org/zh-CN/docs/Web/API/Response) 对象。

`Promise<Response> fetch(url[, init])`

第二个参数是一个可选的配置对象，包括`method`,`headers`，`body`等对请求的配置。
