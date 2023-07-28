---
title: Sass 预处理器
icon: sass
date: 2023-02-11
category:
  - 前端基础
tag:
  - css
---

Sess是一个css预处理器，利用提供的特定语法帮助我们更方便的书写css代码。

学习网站参考：

- **Sass中文网** <https://www.sass.hk/docs/>

## Sass学习

### 注释

```
单行注释： / / css文件里不会显示 压缩方式的css不会显示
多行注释： /**/ css文件里会显示 压缩方式的css不会显示
强制注释：/* ! */ css文件里会显示 压缩方式的css会显示
```



### 嵌套写法

与less相同，可查看上文。



### 父元素选择器&

与less相同，可查看上文。



### 占位符选择器%

占位符选择器声明那些主要用来扩展其他类的类，并且不会将其编译成css。

```scss
// 用来进行极差等扩展，不会对其进行编译
%box {
	width: 100px;
	height: 100px;
}
```



### 运算

sass同样有运算功能，用法基本与less相同。

```scss
// .scss文件
.box {
    width: 50px + 50px;
    height: 100px - 50px;
    // 这里不能两个都带单位，否则是100px*px这种不合法的值
    margin-top: 10px * 10; 
    // css会将/认为是合法的，所以需要加括号
    padding-top: (100px / 2) ;
}
```



### 变量

sass中变量的定义语法为 **`$变量名：值`** 。需要使用时直接在css值的位置写 **`$变量名`** 即可。

- 多个单词，变量名以-分割，如：$theme-color。

- 使用 **！global** 来定义全局变量; 使用 **！default** 来定义默认变量。

- 对于选择器名称、属性名称、url和@import使用变量时，变量必须以插值  **#{$变量名}**  的形式使用（同less）。

- 变量也可以用来定义**list列表**和**map集合**（一组键值对）。

```scss
// .scss文件  Sass后缀名为scss
$dark: #000;
$side: left;
$dark: #eee !default;   //默认变量，如果没有定义过就使用，定义过就使用定义的

$my-list: 1px 5px 10px 20px;	// list列表
$my-map: (font-10: 10px, font-red: red);  // map集合
.box {
    color: $dark;
    $my-color: #fff !global; 	// 在局部定义全局变量
}

.box2 {
    border-#{$side}-radius: 5px; // 在选择器名称上使用变量
}
```



### 混入

使用混合可以将一系列样式从一个规则引入（“混合”）到另一个样式规则集，充分发挥复用性。

**不含参数的混合：**

```scss
@mixin mystyle {      //定义一个混合
  // css样式
}

.box {
  @include mystyle;    //使用混合样式
}
```

**含有参数的混合：** 可以指定参数传值，这样参数顺序未知也可以正确传值；也可以设置默认值

```scss
@mixin mystyle ($param1，$param2， ...) {
    // css样式
}
```

```scss
// 定义一个有默认参数的mixin
@mixin box ($width: 50px, $height: 50px) {
    width: $width;
    height: $height;
}
// 使用
.box {
    // 直接将css样式写入,也可以传入定义的变量
    @include box(200px, 200px);
}
```



### 继承

如果一个元素的样式和另一个元素的样式完全一样，可以使用  **@extend**  就可以把另一个元素的所有样式全部继承过来，继承的本质就是使用并集运算符。

```scss
// .scss文件
%box1 {
    width: 200px;
    height: 200px;
}

// box2 继承 box1的样式
.box2 {
    @extend %box1;
}
```

**但继承要慎用，被继承相关的元素的子元素的样式也会继承。**

**混入与继承的主要区别就是：混入可以传递参数实现动态生成样式，而继承只能提前写好重复样式，复用性较差。**



### 条件判断与循环

Sass可以根据条件判断给出特定的样式：

```scss
@if 条件语句 {
  // css样式1
} @else if 条件语句 {
  // css样式2
} @else {
  // css样式3
}
```

Sass也支持**for循环**，重复输出css，有两种语法：

```scss
@for $index from 开始值 through 结束值 {
}
//从开始值开始，到结束值结束，包含结束值  index表示 1， 2，...结束值

@for $index from 开始值 to 结束值 {
}
//从开始值开始，到结束值结束，不包含结束值  index表示 1， 2，...结束值-1

// .scss文件
@for $i from 1 to 5 {
    .col-#{$i} {
        width: 50px * $i;
    }
}
```

Sass同样支持**each循环**：

遍历一个以空格分隔的 **列表**，然后依次从列表中取出对应值。

```scss
@each $index in $list {
}

// .scss文件
$icons: success fail warning; // 列表
@each $i in $icons {
    .icon-#{$i} {
        background-image: url(../images/icons/#{$icon}.png);
    }
}
```

以及还有**while循环：**

```scss
@while 条件 {
}

// .scss文件
$index: 6;
@while $index> 0 {
    .box-#{$index} {
        width: 5px * $index;
    }

    $index: $index - 2;
}
```



### 自定义函数

语法：

```scss
@function name($param1，$param2，...) {
}

// .scss文件
$index: 6;
@function get-color($key) {
    @if $key > 5 {
        @return #000;
    } @else {
        @return #fff;
    }
}

body {
    background-color: get-color($index);
}
```



### 导入

sass的导入同样使用  **@import**  完成。

```scss
@import url('./reset.scss');
```



*如果像让sass文件不进行编译，如只用来定义全局变量的sass文件，只需要对文件重命名在前面加上一个 **下划线 _** 即可。*

