## Less 学习

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
  h1&{
    width:200px;
    height:300px;
  }
}

/* css */
h1.contain {
  width: 200px;
  height: 300px;
}
```



### 运算

less中的计算可以自动转换并推断数值单位，单位无法转换时以左侧第一个单位为准。

```less
.contain{
  font-size:3px+20*2;
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

sass中变量的定义语法为 **$变量名：值** 。需要使用时直接在css值的位置写 **$变量名** 即可。

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



### 混合

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

遍历一个以空格分隔的列表，然后依次从列表中取出对应值。

```scss
@each $index in $list {
}

// .scss文件
$icons: success fail warning;
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

sass的导入同样使用  **@import**  完成，与less用法相同。

*如果像让sass文件不进行编译，如只用来定义全局变量的sass文件，只需要对文件重命名在前面加上一个 **下划线 _** 即可。*

