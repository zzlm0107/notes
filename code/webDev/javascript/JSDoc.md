## JSDoc简介

[JSDoc](https://www.jsdoc.com.cn/)是记录代码和给编写的代码添加注释的一种方式，通过规范的代码注释，我们就能收获到不一样的开发体验。

如果使用过ts，并且规范化书写各种参数的类型，那么当我们使用时就会发现ts会给我们许多智能化代码提示，这极大的提高了我们的开发效率；但是面对没有使用ts的老项目，
以及还没有学习过ts相关知识，我们就可以使用 JSDoc 来达到类似ts的代码提示，只需要我们在书写时多增加一些**特殊的代码注释**即可。

在 VS Code 编辑器中，我们在js文件中输入 `/**` 就会出现，回车换行即可，然后就可以愉快的写注释了

**注意，JSDoc注释必须写在需要提示代码的正上方。**
```js
/**
 * 
 */
```



## 常用标记

下面是一些常见的标记关键字：



**描述相关**
`@description xxx`  提供这个成员的描述说明
`@since 1.2.0`  描述这个成员加入的版本，一般是指项目版本
`@version 1.1.0`  描述这个成员的版本信息，一般是指成员修改的版本
`@deprecated`  标记已被弃用，后面可以补充一些文本说明原因
`@todo xxx`  描述需要完成的任务
`@alias xxx`  描述成员的别名，主要用于生成文档
`{@link xxx}` 创建指向指定的 namepath 或 URL 的链接
`@see {@link xxx}`  描述可以参考其他相关文档，后面可以补充一些文字或 {@link}



**变量与对象相关**
`@type {number}`  提供一个类型
`@typedef {(number|string)} mytype` 描述一个自定义类型，并可以给定一个名称方便在其他地方引用
`@constant/@const {number} xxx`  标记一个常量，可以提供类型和说明
`@default`  标记默认值，主要用于生成文档
`@global`  标记全局变量
`@readonly`  标记只读变量

`@namespace xxx` 标记对象为其成员创建命名空间，主要用于生成文档
`@memberof xxx`  描述成员隶属于哪一个父级标识符，主要用于生成文档
`@property/@prop {number} data - xxx`  提供类、命名空间或其他对象的属性/方法的 类型、名称和描述

```js
/**
 * @readonly
 * @default
 * @global
 * @constant {number} 圆周率常量
 */
const PI = 3.14

/** @type {(string|undefined)} */
let name = 'andy'


/**
 * 使用typedef自定义类型， 相当于ts的interface
 * @typedef {{
 *  name: string,
 *  age: number
 * }} Person
 * @see {@link https://www.jsdoc.com.cn/tags-type}
 */

/** @type {Person} */
let per = {
  name: 'lucky',
  age: 18
}


/**
 * 也可以这样定义对象
 * @typedef Obj
 * @type {object}
 * @property {string} id - an ID.
 * @property {string} name - your name.
 * @property {number} age - your age.
 */

/** @type {Obj} */
var props;


/**
 * 这样来定义嵌套对象
 * @namespace
 * @property {string}  [level]                - 可选属性
 * @property {object}  defaults               - The default values for parties.
 * @property {number}  defaults.players       - The default number of players.
 * @property {object}  defaults.treasure      - The default treasure.
 * @property {number}  defaults.treasure.gold - How much gold the party starts with.
 */
var config = {
  level:   'beginner',
  defaults: {
    players: 1,
    treasure: {
      gold: 0
    }
  }
};

```




**类相关**
`@class/@constructor`  标记为一个类或构造函数，需要用new来实例化
`@classdesc xxx`  提供类的描述说明，更加语义化
`@abstract`  标记这个成员必须在继承的子类中重写
`@static`  标记为一个静态成员
`@private`  标记为一个私有成员
`@protected`  标记为一个受保护成员
`@public`  标记为一个公共成员



**方法相关**
`@function/@method`  标记为一个函数
`@param/@argument {number} data - xxx`  提供函数参数的类型、名称和描述
`@returns/@return {number} xxx`  提供返回值的类型和描述
`@example xxx`  提供如何使用的示例。此标记后面的文本将显示为突出显示的代码
`@callback xxx`  描述一个回调函数，不需要有实例，说明的类型可以被其他函数引用

```js
/**
 * 这是一个普通的求和函数
 * @function
 * @param {number} a - 一个数字
 * @param {number} b - 另一个数字
 * @returns {number} 两个参数的和
 * @example
 * // 这是一个示例
 * let sum = fu(4, 5)
 * console.log(sum) // 9
 */
function fn(a, b) {
  return a + b
}


/**
 * ajax请求完成需要执行的回调函数
 * @callback requestCallback
 * @param {number} code 
 * @param {string} meg 
 * @return {void}
 */

/**
 * 封装一个ajax
 * @param {requestCallback} cb
 */
function myAjax(cb) {
  // code
}
```



**文件与模块相关**
`@file xxx`  提供文件的说明，放在在文件开头的 JSDoc 注释部分
`@author xxx`  提供这个成员的作者，后面可以补充尖括号括起来的电子邮件地址
`@copyright xxx`  描述一个文件的版权信息，搭配 file 标识使用放在文件开头
`@module xxx`  描述一个模块的名称
`@requires xxx`  描述当前模块需要的依赖的其他模块
`@exports`  描述除了 exports 或 module.exports 属性外导出的任何内容

```js
/**
 * @file 这是一个测试文件
 * @author me <me@163.com>
 * @copyright 随便用
 * 
 * @module testModule
 * @requires otherModule
 */

```