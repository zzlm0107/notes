## 字符串方法

### 查找

str.`charAt`(2) == str[2]					返回字符串指定位置的字符
str.`includes`(searchStr, position?)		 判断是否包含指定子串
str.`indexOf`(searchStr, position?)		   返回指定字串第一次出现的开始索引，没有返回-1
str.`lastIndexOf`(searchStr, position?)	   返回指定字串最后一次出现的开始索引,没有返回-1
str.`search`(regexp)						  返回正则第一次匹配到的索引，没有返回-1


### 其他常用方法
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





## 数组方法

### 增加元素

*arr.`push`(3)*				   增加元素到数组末尾
*arr.`unshift`(0)*				增加元素到数组开头

### 删除元素

*arr.`pop`()*					 删除末尾元素
*arr.`shift`()*   				删除开头元素

从哪个下标位置开始（包括），删几个，增加的元素（多选）。对原数组做修改
*arr.`splice`(start, deleteCount?, item?)*

### 修改元素

数组下标直接修改

### 查找元素

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

### 遍历数组

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

### 其他常用方法

**数组拼接**，返回一个新数组，valueN为数组或值
*arr.`concat`(value0, value1, /* … ,*/ valueN)*

**数组转字符串**，参数为分隔符，不传分隔符是逗号
*arr.`join`('-')*

**数组翻转**
*arr.`reverse()`*

**数组截取**
*arr.`slice`(start, end)*

**数组排序**
*arr.`sort`((a, b) => { return a - b } )*		升序
*arr.`sort`((a, b) => { return b - a } )*		降序

**获取所有的键**
*arr.`keys()`*

**获取所有的值**
*arr.`values()`*

**数组判断**
*`Array.isArray`(arr)*



## 对象方法

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