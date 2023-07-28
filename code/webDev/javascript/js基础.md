---
title: JS 基础
icon: jichu
date: 2022-10-06
category:
  - JS
tag:
  - 编程基础
---


## 初识JS

JavaScript负责页面中的的行为。是一门运行在浏览器端的脚本语言。

JS可以直接写在HTML文件内部的 **script标签** 中，也可以在外部创建一个js文件然后利用script标签的src属性引入（这样这个script标签内部不能在编写代码）。



## 数据类型

JS目前共有8种数据类型：number、string、boolean、null、undefined、object、symbol、bigInt。

我们可以使用 **typeof运算符** 来检查数据类型。 `typeof xxx` 会返回变量的数据类型。（但需要注意的是 `typeof null` 返回的是 object，这是js的历史遗留问题，用 `xxx === null` 代替判断）

- **基本数据类型:** 除 object 以外的其他7种类型都是。
- **引用数据类型：** object，其内部包含 Array，Function， Date。



## 类型转换

- **转string类型：** （前两个为强制类型转换，第三个是隐式转换）
  - `xxx.toString()` 方法。**强制转换** 但不适用于null和undefined，因为他们自身上没有方法。
  - `String(xxx)` **强制转换** 对自身上有 `toString()` 的会直接调用。null直接返回"null",undefined直接返回"undefined"。
  - `xxx + ''` 任意的数据类型 + "". **隐式转换** 隐式的将整体转换为字符串

- **转number类型：**
  - `Number(xxx)` **强制转换** 纯数字的字符串会正常转换，非纯数字字符串或undefined转为 **NaN**。空串或null转为 0，布尔值true为 1，false为 0.
  - `parseInt(xxx)` 或 `parseFloat(xxx)` **强制转换** 可以将**一个字符串中的头部有效的整数位**提取出来，并转换为Number。 非string会先转为string再执行。

- **转boolean类型：**
  - `Boolean(xxx)` **强制转换 ** 除了空串， 0， NaN, undefined， null会转为false，其余都是true。



## 基础语法

关于流程控制，判断，循环，遍历等不再记录笔记，各种语言都是相通的。



## 常用方法

### **字符串**

#### 查找

str.`charAt`(2) == str[2]					返回字符串指定位置的字符

str.`includes`(searchStr, position?)		 判断是否包含指定子串

str.`indexOf`(searchStr, position?)		   返回指定字串第一次出现的开始索引，没有返回-1

str.`lastIndexOf`(searchStr, position?)	   返回指定字串最后一次出现的开始索引,没有返回-1

str.`search`(regexp)						  返回正则第一次匹配到的索引，没有返回-1


#### 其他常用方法

str.`toLowerCase`()				返回字符串的全小写格式

str.`toUpperCase`()				返回字符串的全大写格式

str.`trim`()					 清除字符串两边可能存在的空格

str.`trimStart`()				清除字符串开头可能存在的空格

str.`trimEnd`()				  清除字符串结尾可能存在的空格

str.`concat`(str2,[...strN])			   字符串拼接，返回拼接完的新字符串

str.`repeat`(count)					    返回重复指定次数的新字符串

str.`substring`(indexStart[, indexEnd])	返回指定开始位置到指定结束（不包括）的子串，不影响原来的

str.`slice`(beginIndex[, endIndex])		返回指定开始位置到指定结束（不包括）的子串，不影响原来的

str.`split`(separator[, limit])		    返回以指定字符分隔后的数组

判断字符串是否以指定子串开头/结尾，index表示搜索位置，默认0 length表示设置字符串结尾位置

str.`stratsWith`(searchStr,index?)

str.`endsWith`(searchStr,length?)

匹配指定的字符串或正则表达式，并用新的字符串代替

str.`replace`(regexp|substr, newSubStr|function)



### **数组**

#### 增加元素

*arr.`push`(3)*				   增加元素到数组末尾

*arr.`unshift`(0)*				增加元素到数组开头

#### 删除元素

*arr.`pop`()*					 删除末尾元素

*arr.`shift`()*   				删除开头元素

从哪个下标位置开始（包括），删几个，增加的元素（多选）。对原数组做修改

*arr.`splice`(start, deleteCount?, item?)*

#### 修改元素

数组下标直接修改

#### 查找元素

返回符合回调函数的第一个元素/最后一个元素的 **值** ，没有返回undefind  回调函数需要返回一个Boolean值

*arr.`find`( (element, index?, array?) => { /* … */ } )*

*arr.`findLast`( (element, index?, array?) => { /* … */ } )*

返回符合回调函数的第一个元素/最后一个元素的 **索引**，没有返回-1  回调函数需要返回一个Boolean值

*arr.`findIndex`((element, index, array) => { /* … */ } )*

*arr.`findLastIndex`((element, index, array) => { /* … */ } )*

判断数组中是否 **包含** 指定元素

*arr.`includes`(searchElement, fromIndex?)*

返回数组中指定元素的第一个/最后一个 **索引**,没有返回-1

*arr.`indexOf`(searchElement, fromIndex?)*

*arr.`lastIndexOf`(searchElement, fromIndex?)*

#### 遍历数组

对数组的每个元素执行一次给定的函数，没有返回值

*arr.`forEach`((element, index?, array?) => { /* … */ })*

测试数组内是否所有元素都能通过指定函数,返回Boolean值

*arr.`every`((element, index, array) => { /* … */ } )*

测试数组内是否至少有一个元素可以通过指定函数,返回Boolean值

*arr.`some`((element, index, array) => { /* … */ } )*

测试数组内的每个元素是否可以通过指定函数，返回由通过元素组成的数组

*arr.`filter`((element, index, array) => { /* … */ } )*

对数组的每个元素执行一次给定的函数，并将每个元素执行返回的值组成一个新数组

*arr.`map`((element, index, array) => { /* … */ })*

对数组中的每个元素按序执行一次指定函数，并且每次都会将先前元素的计算结果作为参数传入，最后将其结果汇总为单个返回值。若不传初始值initialValue，则将数组索引为 0 的元素作为初始值，从索引 1 元素开始执行函数。

*arr.`reduce`((previousValue, currentValue, currentIndex, array) => { /* … */ }, initialValue?)*

#### 其他常用方法

**数组拼接**，返回一个新数组，valueN为数组或值

*arr.`concat`(value0, value1, /* … ,*/ valueN)*

**数组转字符串**，参数为分隔符，不传分隔符是逗号

*arr.`join`('-')*

**数组翻转

*arr.`reverse()`*

**数组截取**

*arr.`slice`(start, end)*

**数组排序

*arr.`sort`((a, b) => { return a - b } )*		升序

*arr.`sort`((a, b) => { return b - a } )*		降序

**获取所有的值的迭代器对象**

*arr.`values()`*

**数组判断**

*`Array.isArray`(arr)*

**类数组转数组**

*Array.`form(arr)`*



### **对象**

**对象拷贝（浅拷贝）** 将所有可枚举的自有属性从原对象拷贝到目标对象上

*`Object.assgin`(target,sources)*

**对象属性存在判断**

*`Object.hasOwn`(instance, prop)*

*obj.`hasOwnProperty`(prop)*

**对象原型链关系判断** 测试一个对象是否存在于另一个对象的原型链上

*prototypeObj.`isPrototypeOf`(object)*

**获取所有的键**
*`Object.keys`(obj)*					   返回可枚举属性的数组

*`Object.getOwnPropertyNames`(obj)*		返回所有自身属性的数组(包括不可枚举属性,不包括Symbol)

*`Object.getOwnPropertySymbols`(obj)*	  返回Symbol属性的数组

**获取所有的值（浅层）**

*`Object.values`(obj)*
