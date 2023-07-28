---
title: ES6 +
icon: es6
date: 2022-11-14
category:
  - JS
tag:
  - 编程进阶
---

更加细致的学习文档可以参考 [ES6文档](https://www.bookstack.cn/read/es6-3rd/sidebar.md)

## let与const

- **let**:声明变量

  特点：不允许重复声明；块级作用域；不存在变量提升；不影响作用域链

- **const**: 声明常量(*内存地址指针不会变化的变量，对象也算*)

  特点：不允许重复声明；块级作用域；不存在变量提升；声明必须赋初始值

- **var**： 声明变量

  特点：允许重复声明；全局作用域；存在变量提升



## 解构赋值

允许按照一定模式, **从数组和对象中提取值, 对变量进行赋值**

- 数组解构： `const [x, y] = [1, 2]`
- 对象解构： `const { x, y } = { x: 1, y: 2 }`

```js
// 利用解构赋值交换变量
let x = 1
let y = 2
[x, y] = [y, x]

// 遍历map结构
for (let [key, value] of map) {
 console.log(key + " is " + value);
}
```



## 模板字符串

模板字符串（template string）是增强版的字符串, 用反引号[ \` ]标识. 它可以当作普通字符串使用, 也可以用来定义多行字符串, 或者在字符串中嵌入变量. 嵌入变量使用 `${变量名}` 当然也可以放js表达式。



## 箭头函数

ES6 允许使用“箭头”（`=>`）定义函数.

```js
let fn = () => {
 console.log('123')
}
```

注意点：

1. 函数体内的 [ this ] 对象, 就是定义时所在的上下文对象, 而不是使用时所在的对象.
2. 不可以当作构造函数使用。
3. 不可以使用`arguments`对象, 该对象在函数体内不存在. 如果要用, 可以用 rest 参数代替.



**高阶函数**:就是一个函数就可以接收另一个函数作为参数, 或者是返回一个函数。常见的高阶函数有map、reduce、filter、sort等

**函数柯里化**： 柯里化后, 将第一个参数变量存在函数内部(闭包), 然后本来需要n个参数的函数可以变成只需要剩下的（n - 1个）参数就可以调用。



## rest参数

ES6 引入 rest 参数（形式为`...变量名`）, 用于获取函数的多余参数。rest参数在函数里内部将是一个由多余参数组成的数组。

```js
function fn(val1,val2, ...others) {
  console.log(others)
}
fn(1,2,3,4,5) // [3,4,5]
```





## Set数据结构

set是形如数组，但成员值都是唯一且没有重复情况的数据结构。需要使用 `new Set()` 创建。

- **add()**: 添加值，返回实例

- **delete()**: 删除值，返回布尔

- **has()**: 检查值，返回布尔

- **clear()**: 清除所有成员

- **keys()**: 等同values()方法

- **values()**: 返回以属性值为遍历器的对象

- **entries()**: 返回以属性值和属性值为遍历器的对象

- **forEach()**: 使用回调函数遍历每个成员

  

**应用场景**

- 去重字符串: `[...new Set(str)].join("")`
- 去重数组: `[...new Set(arr)]`或`Array.from(new Set(arr))`
- 声明: `const a = new Set(arr1)`
- 并集: `new Set([...a, ...b])`
- 交集: `new Set([...a].filter(v => b.has(v)))` //此处的has指的是set自带的方法
- 差集: `new Set([...a].filter(v => !b.has(v)))`





## Map数据结构

map是形如对象，成员键是任何类型且可以重复，与值一一对应的数据结构。需要使用 `new Map()` 创建。

- 方法
- **get()**: 返回键值对
- **set()**: 添加键值对，返回实例
- **delete()**: 删除键值对，返回布尔
- **has()**: 检查键值对，返回布尔
- **clear()**: 清除所有成员
- **keys()**: 返回以键为遍历器的对象
- **values()**: 返回以值为遍历器的对象
- **entries()**: 返回以键和值为遍历器的对象
- **forEach()**: 使用回调函数遍历每个成员



## Promise

Promise 是 JS 中**进行异步编程**的新解决方案

它有三种状态：并且状态一旦切换，就无法逆转。调用 `resolve()` 切换成成功状态，调用 `reject()` 切换成失败状态。

- *待定（pending）*：初始状态。
- *已兑现（fulfilled）*：意味着操作成功完成。
- *已拒绝（rejected）*：意味着操作失败。

```js
function promiseAjax(url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest()
    xhr.onreadystatechange = () => {
      if (xhr.readyState !== 4) return
      const {
        status,
        response
      } = xhr
      // 请求成功, 调用 resolve(value)
      if (status >= 200 && status < 300) {
        resolve(JSON.parse(response))
      } else { // 请求失败, 调用 reject(reason)
        reject(new Error('请求失败: status: ' + status))
      }
    }
    xhr.open("GET", url)
    xhr.send()
  })
}
```

下面介绍一些promise的常用方法：

- `Promise.prototype.then` 方法: (onResolved, onRejected) => {}

  指定用于得到成功 value 的成功回调和用于得到失败 reason 的失败回调 返回一个新的 promise 对象

- `Promise.prototype.catch` 方法: (onRejected) => {}

  当运行到最后,**没被处理的所有异常错误**都会进入这个方法的回调函数中.这意味着当错误在then的第二个参数被处理时，此方法就不会执行。

- `Promise.prototype.finally` 方法: () => {}

  运行到最后,不管成功还是失败都会执行的方法。

  

- `Promise.resolve` 方法: (value) => {}

  返回一个**成功/失败**的 promise 对象,直接改变promise状态.返回的 promise 状态取决于value的类型：

  - value是普通参数，则返回成功的promise，value作为成功的参数
  - value是一个promise，则返回的promise状态取决于传入的promise。如果传入的转变成了失败状态，则最终返回的也会是失败的promise

- `Promise.reject` 方法: (reason) => {}

  返回一个**失败**的 promise 对象,直接改变promise状态

  

- `Promise.all 方法`: (promises) => {}

  传递一个由promise组成的数组，返回一个新的 promise, 只有所有的 promise **都成功才成功**, 只要有一 个失败了就直接失败

- `Promise.race` 方法: (promises) => {}

  传递一个由promise组成的数组,返回一个新的 promise, **第一个完成**的 promise 的结果状态就是最终的结果状态
  
- `Promise.any` 方法: (promises) => {}
  
  传递一个由promise组成的数组,返回一个新的 promise, **第一个成功**的 promise 的结果状态就是最终的结果状态，如果所有 promise 都失败了，则返回一个失败原因的数组。



**讨论：**

**1. 改变 promise 状态和指定回调函数谁先谁后?**

  都有可能，如果在执行器内都是同步代码，则会先改变状态，然后指定相应回调，推入微任务队列等待执行。但如果执行器内有异步代码如定时器，且改变状态的函数放在了定时器中，则会先指定相应回调，推入微任务队列直到定时器结束状态改变才执行。

**2. promise.then()返回的新 promise 的结果状态由什么决定?**

  由指定的回调函数执行的结果决定。

  - 回调函数抛出异常，则返回的promise变为失败状态
  - 回调函数返回非 promise值，则状态变为成功
  - 回调函数返回 promise，状态由返回的promise决定

**3. promise链式调用与异常穿透是什么意思？**

  **链式调用**即promise的 then()返回一个新的promise, 新promise又可以继续调用then，达成链式调用。

  **异常穿透**即当使用 promise 的 then 链式调用时, 可以在最后指定一个catch失败的回调。这样前面任何操作出了异常, 都会传到最后失败的回调中处理。注意每个then()的第二个回调函数也可以进行err处理，如果前面已经处理过，则不会在走最后的兜底操作。

**4. 链式调用时如何中断promise链？**

  只需在需要中断的位置返回一个pedding状态的promise即可，这样后面的then就一致拿不到前面的状态而无法执行。

