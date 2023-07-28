---
title: Less 预处理器
icon: less
date: 2022-12-18
category:
  - 前端基础
tag:
  - css
---

Less是一个css预处理器，利用提供的特定语法帮助我们更方便的书写css代码。

学习网站参考：

- **Less中文网** <https://less.bootcss.com/>


## Less学习

### 注释

```less
/* 这种类型的注释会保留到编译的css文件中 */
// 而这种类型则不会出现在编译的css文件中
```



### 嵌套

less的嵌套语法可以在父元素的样式里继续书写子元素的样式，一级一级向下嵌套，简化了css选择器每次从上向下选中的过程，使用起来非常方便。

```less
.dad {
  width:30px;
  background-color: #fff;
  .son{
    border-radius: 40px;
  }
}
```



### 父元素选择器&

less的&符号用来指代 **当前代码块里 **作用的选择器，通常配合伪类和伪元素使用。

（指代的是此代码块前的所有父元素）

```less
a{
  color:#333;
  &:hover{
    color:blue;
  }
}
```

如果在当前的选择器名字后面写&，就会将当前的选择器提取到父级。

```less
/* less */
.contain{
  h1 &{
    width:200px;
    height:300px;
  }
}

/* css */
h1 .contain {
  width: 200px;
  height: 300px;
}
```



### 运算

less中的计算可以自动转换并推断数值单位，单位无法转换时以左侧第一个单位为准。

```less
.contain{
  font-size:3px+20*2; // 43px
}
```



### 变量

**变量的加载：**变量存在声明提升，less中定义的变量也可以在使用之后声明；并且变量存在作用域，查找规则从内到外。

less中变量的定义语法为 **@变量名：值** 。需要使用时直接在css值的位置写 **@变量名** 即可

```less
@bgColor:white;
.contain{
  background-color: @bgColor;
}
```

而对于选择器名称、属性名称、url和@import使用变量时，变量必须以插值即  **@{变量名}**  的形式使用。

```less
/* less */ 
@myChoose:one;
@myColor:color;
@images: "../img";

.@{myChoose} {
  @{myColor}: #333;
  background: url("@{images}/bg.png");
}

/* css */ 
.one {
  color: #333;
  background: url("../img/bg.png");
}
```



### 混合

使用混合可以将一系列样式从一个规则引入（“混合”）到另一个样式规则集，充分发挥复用性。
可以直接将一个样式集混合到另一个样式集中，也可以在混合的类名后面加上一个**小括号**可以阻止混合类样式的编译。

```less
.bor() {     //不编译此样式块                
  background-color: aqua;
  width: 32rpx;
}
.poop {
  color: white;
  color:white;
  .bor();
}
```

**带参数的混合**：可以在混合时传递参数，当然参数可以设定一个默认值。

```less
.son(@width:200px){
    width:@width;
}
.dad{
    .son();
}
```

多个形参要以分号分隔，当一个形参里有几个部分时就以逗号分隔，不过还是多写几个形参比较好。

**arguments变量 **表示可变参数，意思是形参从先到后的顺序。

```less
/* less */ 
.son(@dd1:20px;@dd2:solid;@dd3:white){
    border:@arguments;
}
.father {
  .son();
}

/* css */
.father {
  border: 20px solid white;
}
```



### 导入与导出

使用 **@import** 导入我们需要的全局less文件。

```less
@import: '../assets/style/less/common.less'
```

在less文件的第一行通过添加一行注释实现导出为css文件。

```less
// out:../assets/style/css/             //导出编译的css文件到指定文件夹
// out: false                           //禁止导出
```

