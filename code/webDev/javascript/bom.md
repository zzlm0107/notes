---
title: BOM 相关
icon: bom
date: 2022-11-03
category:
  - JS
  - 浏览器
tag:
  - bom
---


browser object model 浏览器对象模型。BOM可以使我们通过JS来操作浏览器，**注意浏览器的全局对象只有一个即window，下面的说明对象都是挂载在window的，属于是window的属性。只是书写时会省略掉window**



##  navigator对象

代表的当前浏览器的信息，使用较少，其中主要用 **userAgent属性** 来判断浏览器标识。



## history对象

history对象可以用来操作浏览器的历史记录，实现向前或向后翻页。

- `history.back()`: 回退到上一个页面
- `history.forward()`: 前进到下一个页面

- `history.go(参数)`: 跳转到指定的页面，当前页面是0，正整数向前跳转对应数量页面，负整数向后回退对应数量页面



## location对象

location对象中封装了浏览器的地址栏的信息，并且对其相应属性修改操作也会导致地址栏变化引起跳转。

- `location.href`: 浏览器的地址栏信息
- `location.protocol`: 网址的协议
- `location.host`: 网址的域名
- `location.host`: 网址的端口号
- `location.search`: 网址的search参数
- `location.assign(url)`: 跳转页面，与修改 location.href 效果一致
- `location.reload()`: 刷新当前页面
- `location.replace(url)`: 清空历史记录并跳转到指定页面



## JSON对象

**JSON就是一个特殊格式的字符串**，这个字符串可以被任意的语言所识别。**JSON字符串中的属性名与值必须加双引号**

- `JSON.parse(json)`： 将json字符串转换为JS对象
- `JSON.stringify(obj)`： 将JS对象转换为json字符串



## 本地/会话存储

- **localStorage**：本地存储，存储在对应网址下的本地存储空间中，浏览器关闭也不会消失。适合持久性存储相关数据。需要手动清除。容量更大。
- **sessionStorage**：会话存储，存储在对应网址下的会话存储空间中，相关网址关闭即清空，适合暂时性存储数据。容量较小。

*注意：存储的数据必须都是字符串，如果想存对象可以先 JSON.stringify(obj) 转为字符串再存入，以键值对的方式存入。*



两种对象的方法都一致，下面以本地存储为例：

- `localStorage.setItem(key, value)` 存入键值对
- `localStorage.getItem(key)` 按key读取value，读取不到返回null
- `localStorage.removeItem(key)` 按key移除键值对
- `localStorage.clear()` 清空存储空间



## 计时器

window全局对象上有两个计时器方法：`setInterval` 和 `setTimeout`

**setInterval** : 定时器，设定时间间隔，每间隔指定时间就执行一次回调函数，会执行多次

**setTimeout** : 延时器，设定延时时间，延时指定时间后才执行回调函数，只执行一次

如果我们想给回调函数传递参数，只需要在设定时间的后面依次添加即可。

```js
function f1() {
  console.log('我会在1s后延时触发')
}
function f2(val) {
  console.log('我每隔1s就会触发一次'，val)
}

let timer1 = setTimeout(f1, 1000) // 设置延时器
clearTimeout(timer1) // 清除延时器

let timer2 = setInterval(f2, 1000, 'abc') // 设置定时器
clearInterval(timer2) // 清除定时器
```

