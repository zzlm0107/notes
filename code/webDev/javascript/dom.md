---
title: DOM 相关
icon: dom
date: 2022-10-20
category:
  - JS
  - 浏览器
tag:
  - dom
---


Document Object Model 文档对象模型，通过DOM可以来任意来修改网页中各个内容

网页中浏览器已经为我们提供了**document对象**，**它代表的是整个网页，它是window对象的属性，可以在页面中直接使用。**

## 元素

### 查找

下面是一些查找全局元素的方式：

- 获取HTML元素： `document.documentElement`
- 获取body元素： `document.body`

- 根据id获取元素： `document.getElementById("id属性值")`
- 根据标签名获取元素数组： `document.getElementsByTagName("标签名")`
- 根据类名获取元素数组：`document.getElementsByClassName("类名")`

- 自定义查找第一个匹配元素： `document.querySelector("名称")` 名称规则与css选择器相同

- 自定义查找全部匹配元素： `document.querySelectorAll("名称")` 名称规则与css选择器相同



下面是查找指定元素前后或内部元素的方式： **设 el 为找到的指定元素**

- `el.parentNode` 获取当前元素的父元素节点

- `el.childNodes` 获取当前元素的所有子节点，包括子元素和空白文本节点。（不常用）
- `el.children` 获取当前元素的所有子元素，仅元素标签。
- `el.firstChild/lastChild` 获取当前元素的第一个/最后一个子节点。包括空白文本节点。（不常用）
- `el.firstElementChild/lastElementChild` 获取当前元素的第一个/最后一个子元素。仅元素标签。
- `el.previousSibling/nextSibling` 获取当前元素的前一个/后一个兄弟节点。包括空白文本节点。（不常用）
- `el.previousSibling/nextSibling` 获取当前元素的前一个/后一个兄弟元素。仅元素标签。



### 修改

查找到页面元素后就可以对其属性值进行读取和修改

```js
let el = document.querySelector("#root")

// el.className 获取元素类名列表 仅用于读取
// el.classList 获取元素类名列表数组 可用于修改和删除
// el.style.xxx 获取元素样式
// el.innerHTML 获取元素内部内容
```



### 增加

1. `document.write(内容)` 直接将内容重绘到页面上，会将之前的页面内容全部删除
2. `el.innerHTML = 内容` 直接将元素内部值修改为html结构
3. `document.createElement(标签名)` 创建一个指定的标签，仅创建标签，插入页面还需要其他操作：
   - `el.appendChild(newNode)` 将新创建的元素插入到指定元素的子节点列表末尾
   - `el.insertBefore(newNode, child)` 将新创建的元素插入到指定元素的指定子节点之前

此外还有克隆节点：

`el.cloneNode(flag)` 克隆元素，flag为false时只克隆元素本身，为true时还会克隆内部子节点。



### 删除

- `el.removeChild(child)` 移除元素的指定子节点，返回删除的节点





### 自定义属性

对于内置的属性，我们可以直接使用 `element.xxx` 获取，但对于自定义属性需要使用方法来获取

- `element.getAttribute(属性名)` 获取自定义属性
- `element.setAttribute(属性名，属性值)` 修改自定义属性
- `element.removeAttribute(属性名)` 移除自定义属性





### 元素偏移量 offset

我们使用 offset系列相关属性可以动态的得到该元素的位置（偏移）、大小等。

| 属性                 | 作用                                                         |
| -------------------- | ------------------------------------------------------------ |
| element.offsetParent | 返回作为该元素带有定位的父级元素，如果父级都没有定位则返回body |
| element.offsetTop    | 返回距离作为该元素带有定位的父元素上边框的偏移               |
| element.offsetLeft   | 返回距离作为该元素带有定位的父元素左边框的偏移               |
| element.offsetWidth  | 返回自身padding、边框、内容区的宽度，返回的数值不带单位      |
| element.offsetHeight | 返回自身padding、边框、内容区的高度，返回的数值不带单位      |



### 元素可视区 client

我们使用 client 系列的相关属性来获取元素可视区的相关信息。通过 client系列的相关属性可以动态的得到该元素的边框大小、元素大小等。

| 属性                 | 作用                                                        |
| -------------------- | ----------------------------------------------------------- |
| element.clientTop    | 返回元素上边框的大小                                        |
| element.clientLeft   | 返回元素左边框的大小                                        |
| element.clientWidth  | 返回自身padding、内容区的宽度，不包含边框返回的数值不带单位 |
| element.clientHeight | 返回自身padding、内容区的宽度，不包含边框返回的数值不带单位 |



### 元素滚动 scroll

我们使用 scroll 系列的相关属性可以动态的得到该元素的大小、滚动距离等。

| 属性                 | 作用                                           |
| -------------------- | ---------------------------------------------- |
| element.scrollTop    | 返回被卷去的上方距离                           |
| element.scrollLeft   | 返回被卷去的左边距离                           |
| element.scrollWidth  | 返回自身实际宽度，不包含边框返回的数值不带单位 |
| element.scrollHeight | 返回自身实际高度，不包含边框返回的数值不带单位 |

**页面滚动的距离通过 window.pageXOffset 获得**





## 事件

事件即为页面元素在某种操作下执行的一定回调方法，此即触发了一个事件。

事件三要素：触发源，触发方式，触发回调



### 事件绑定

1. **on + 事件类型**   例如 onClick，小驼峰书写方式。这种方式只能给元素绑定一个事件。

2. **addEventListener**  通过此方法添加事件，可以给元素绑定多个事件。有三个参数，分别是事件类型，事件回调，是否开启捕获模式（默认false）

   ```js
   let el = document.querySelector('#root')
   
   function fn1() {
     console.log('第1种方式')
   }
   function fn2() {
     console.log('第2种方式')
   }
   
   el.onClick = fn1 // 第一种 on + 事件类型 绑定
   el.onClick = null // 解除绑定
   
   el.addEventListener('click', fn2, false) // 第二种 
   el.removeEventListener('click', fn2) // 解除绑定
   ```



### 事件对象

当响应函数被调用时，浏览器每次都会将一个事件对象作为实参传递进响应函数中，这个事件对象中封装了当前事件的相关信息。

```js
let el = document.querySelector('#root')

function fn(event) {
  console.log(event.target, '触发事件的元素')
  console.log(event.type, '事件的类型')
  // event.preventDefault()  阻止默认事件
  // event.stopPropagation() 阻止冒泡
}

el.addEventListener('click', fn)
```



### 事件委派

将事件统一绑定给元素的共同的祖先元素，这样当后代元素上的事件触发时，会一直冒泡到祖先元素，从而通过祖先元素的响应函数来处理事件。

事件委派是利用了冒泡，通过委派可以减少事件绑定的次数，提高程序的性能



### 事件传播机制

事件传播有三个阶段：

1.**捕获阶段**：在捕获阶段时从最外层的祖先元素，向目标元素进行事件的捕获，但是默认此时不会触发事件

2.**目标阶段**：事件捕获到目标元素，捕获结束开始在目标元素上触发事件

3.**冒泡阶段**：事件从目标元素向他的祖先元素传递，依次触发祖先元素上的事件



### 鼠标事件

下面是一些常见的鼠标事件类型：

**mousedown**（鼠标按下） **mousemove**（鼠标移动） **mouseup**（鼠标松开） 

**mousewheel**（鼠标滚轮滚动） **focus** (获取焦点)  **blur** （失去焦点）

**mouseover**(鼠标经过，会冒泡，经过子元素也会触发事件) 

**mouseout**（鼠标离开，会冒泡，离开子元素但仍在元素内 或 从绑定元素移动到子元素也会触发，因为子元素遮盖了父元素的可视区域）

**mouseenter**（鼠标进入，不会冒泡，仅进入自身元素触发）

**mouseleave**（鼠标离开，不会冒泡，仅离开自身元素触发）



以及事件对象种关于鼠标的相关位置信息：

**event.clientX/clientY**  返回触发时鼠标相对于浏览器窗口可视区的X/Y坐标

**event.pageX/pageY**  返回触发时鼠标相对于页面文档的X/Y坐标

**event.screenX/screenY**  返回触发时鼠标相对于显示器屏幕的X/Y坐标

**event.offsetX/offsetY**  返回触发时鼠标相对于触发元素内填充边的X/Y坐标



### 键盘事件

下面是一些常见的键盘事件类型：

keydown（按键按下） onkeyup（按键松开）

并且使用 **event.keyCode** 来判断按下了哪一个按键。

