---
title: Bootstrap5 笔记
icon: bootstrap
date: 2023-04-09
category:
  - 第三方库
tag:
  - css相关
---


## 什么是 Bootstrap？

Bootstrap 是一个用于快速开发 Web 应用程序和网站的前端框架。 Bootstrap是前端开发中比较受欢迎的框架，简洁且灵活。它基于HTML、CSS和JavaScript，HTML定义页面元素，CSS定义页面布局，而JavaScript负责页面元素的响应。Bootstrap将HTML、CSS和JavaScript封装成一个个功能组件，用起来简洁灵活，使得 Web 开发更加快捷。

Bootstrap5 目前是 Bootstrap 的最新版本，利用提供的 Sass 变量和大量 mixin、响应式栅格系统、可扩展的预制组件、基于 jQuery 的强大的插件系统，能够快速为你的想法开发出原型或者构建整个 app 。

**浏览器兼容性**：Bootstrap5 兼容所有主流浏览器（Chrome、Firefox、Edge、Safari 和 Opera）。 如果您需要支持 IE11 及以下版本，请使用 Bootstrap4 或 Bootstrap3。

## **Bootstrap安装**

### 官网下载 Bootstrap5 资源库

下载下来的文件是压缩包，解压之后可以看到文件的结构。下载的文件包括：

编译并压缩过的 CSS 集成包
编译并压缩过的 JavaScript 插件

下载并解压后，将看到文件夹中包含如下文件： 

```
bootstrap/
├── css/
│   ├── bootstrap-grid.css
│   ├── bootstrap-grid.css.map
│   ├── bootstrap-grid.min.css
│   ├── bootstrap-grid.min.css.map
│   ├── bootstrap-grid.rtl.css
│   ├── bootstrap-grid.rtl.css.map
│   ├── bootstrap-grid.rtl.min.css
│   ├── bootstrap-grid.rtl.min.css.map
│   ├── bootstrap-reboot.css
│   ├── bootstrap-reboot.css.map
│   ├── bootstrap-reboot.min.css
│   ├── bootstrap-reboot.min.css.map
│   ├── bootstrap-reboot.rtl.css
│   ├── bootstrap-reboot.rtl.css.map
│   ├── bootstrap-reboot.rtl.min.css
│   ├── bootstrap-reboot.rtl.min.css.map
│   ├── bootstrap-utilities.css
│   ├── bootstrap-utilities.css.map
│   ├── bootstrap-utilities.min.css
│   ├── bootstrap-utilities.min.css.map
│   ├── bootstrap-utilities.rtl.css
│   ├── bootstrap-utilities.rtl.css.map
│   ├── bootstrap-utilities.rtl.min.css
│   ├── bootstrap-utilities.rtl.min.css.map
│   ├── bootstrap.css
│   ├── bootstrap.css.map
│   ├── bootstrap.min.css
│   ├── bootstrap.min.css.map
│   ├── bootstrap.rtl.css
│   ├── bootstrap.rtl.css.map
│   ├── bootstrap.rtl.min.css
│   └── bootstrap.rtl.min.css.map
└── js/
    ├── bootstrap.bundle.js
    ├── bootstrap.bundle.js.map
    ├── bootstrap.bundle.min.js
    ├── bootstrap.bundle.min.js.map
    ├── bootstrap.esm.js
    ├── bootstrap.esm.js.map
    ├── bootstrap.esm.min.js
    ├── bootstrap.esm.min.js.map
    ├── bootstrap.js
    ├── bootstrap.js.map
    ├── bootstrap.min.js
    └── bootstrap.min.js.map
```

下载了预编辑的Bootstrap解压出来得到了Bootstrap文件夹，只要把html与Bootstrap文件夹放在一起，然后用相对路径引用bootstrap.bundle.min.js和 bootstrap.min.css 文件即可。 

1、下载已编译版js和css文件，解压缩后将目录改名称为bootstrap5，放在你的网站目录。

2、在网页`<head> </head>`之间，添加 `<link href="/static/bootstrap5/css/bootstrap.min.css" >` 

3、在网页 `</body>`之前，添加 `<script src="/static/bootstrap5/js/bootstrap.bundle.min.js" ></script>`

 Bootstrap 自带的大部分组件都需要依赖 JavaScript 才能起作用。将 `<script>` 标签 粘贴到页面底部，并且是在 `</body>` 标签之前，就能起作用了。 

注意：要确保bootstrap.min.css和bootstrap.bundle.min.js确实在这个路径。

**Bootstrap5的html模板**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="keywords" content="">
    <meta name="description" content="">
      <!-- Bootstrap 的 CSS 文件 -->
    <link href="./css/bootstrap.min.css" rel="stylesheet">
    <title></title>
  </head>
  <body>
 
    <!-- 包含 Popper 的 Bootstrap 集成包 -->
    <script src="./js/bootstrap.bundle.min.js" ></script>
  </body>
</html>
```

响应式布局相关的 标签

Bootstrap 采用的是 *移动设备优先（mobile first）* 的开发策略，因此，我们首先为移动设备优化代码，然后根据需要并利用 CSS 媒体查询功能来缩放组件。为了确保所有设备都能支持正确的渲染和触屏缩放，请务必在标签中 **添加让 viewport（视口）支持响应式布局的 标签**。

```
<meta name="viewport" content="width=device-width, initial-scale=1">
```

### **Bootstrap 5 CDN**

```html
<!-- 新 Bootstrap5 核心 CSS 文件 -->
<link rel="stylesheet" href="https://cdn.staticfile.org/twitter-bootstrap/5.1.1/css/bootstrap.min.css">
 
<!-- 最新的 Bootstrap5 核心 JavaScript 文件 -->
<script src="https://cdn.staticfile.org/twitter-bootstrap/5.1.1/js/bootstrap.bundle.min.js"></script>
```

### Bootstrap5 容器

容器是Bootstrap一个基本的构建块, 它包含、填充和对齐给定设备或视口中的内容。 

Bootstrap 需要一个容器元素来包裹网站的内容 

我们可以使用以下两个容器类：

- **.container** 类用于固定宽度并支持响应式布局的容器。
- **.container-fluid** 类用于 100% 宽度，占据全部视口（viewport）的容器。

 **固定宽度**

**.container** 类用于创建固定宽度的响应式页面。

**注意：**宽度 (max-width) 会根据屏幕宽度同比例放大或缩小。

|           | 超级小屏幕 <576px | 小屏幕 ≥576px | 中等屏幕 ≥768px | 大屏幕 ≥992px | 特大屏幕 ≥1200px | 超级大屏幕 ≥1400px |
| :-------- | :---------------- | :------------ | :-------------- | :------------ | :--------------- | ------------------ |
| max-width | 100%              | 540px         | 720px           | 960px         | 1140px           | 1320px             |

 **100% 宽度**

**.container-fluid** 类用于创建一个全屏幕尺寸的容器，容器始终跨越整个屏幕宽度（width 始终为 100%）：

二者之间的共同点为，两者都可以将高度设置成auto，即自动模式。最大的不同就是宽度的设定上。

**container**根据屏幕宽度利用媒体查询，已经设定了固定的宽度，作用为阶段性的改变宽度，所以在改变浏览器的大小时，页面是一个阶段一个阶段变化的。

container-fluid则是将宽度设定为auto，所以当缩放浏览器时，它会保持全屏大小，始终保持100%的宽度。

**响应式容器**

可以使用 **.container-sm|md|lg|xl** 类来创建响应式容器。

容器的 max-width 属性值会根据屏幕的大小来改变。

| Class            | 超小屏幕 <576px | 小屏幕 ≥576px | 中等屏幕 ≥768px | 大屏幕 ≥992px | 特大屏幕 ≥1200px | 超级大屏幕 ≥1400px |
| :--------------- | :-------------- | :------------ | :-------------- | :------------ | :--------------- | :----------------- |
| `.container-sm`  | 100%            | 540px         | 720px           | 960px         | 1140px           | 1320px             |
| `.container-md`  | 100%            | 100%          | 720px           | 960px         | 1140px           | 1320px             |
| `.container-lg`  | 100%            | 100%          | 100%            | 960px         | 1140px           | 1320px             |
| `.container-xl`  | 100%            | 100%          | 100%            | 100%          | 1140px           | 1320px             |
| `.container-xxl` | 100%            | 100%          | 100%            | 100%          | 100%             | 1320px             |

```html
	<div class="container-sm">.container-sm</div>
    <div class="container-md">.container-md</div>
    <div class="container-lg">.container-lg</div>
    <div class="container-xl">.container-xl</div>
    <div class="container-xxl">.container-xxl</div>
```

### Bootstrap5 表格

**创建一个简单的表格**

Bootstrap5 通过 **.table** 类来设置基础表格的样式，

```html
<div class="container">
    <table class="table">
        <thead>
            <tr>
                <th>编号</th>
                <th>姓名</th>
                <th>年龄</th>
                <th>手机号</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>1</td>
                <td>张三</td>
                <td>22</td>
                <td>13333445566</td>
            </tr>
            <tr>
                <td>2</td>
                <td>李四</td>
                <td>21</td>
                <td>13211223344</td>
            </tr> 
            <tr>
                <td>3</td>
                <td>王五</td>
                <td>22</td>
                <td>13555667788</td>
            </tr>                   
        </tbody>
     </table>
</div>
```

**表格颜色**

通过指定意义的颜色类名可以为表格的行或者单元格设置颜色。表格颜色类的说明:

| 类名                 | 描述                             |
| :------------------- | :------------------------------- |
| **.table-primary**   | 蓝色: 指定这是一个重要的操作     |
| **.table-success**   | 绿色: 指定这是一个允许执行的操作 |
| **.table-danger**    | 红色: 指定这是可以危险的操作     |
| **.table-info**      | 浅蓝色: 表示内容已变更           |
| **.table-warning**   | 橘色: 表示需要注意的操作         |
| **.table-active**    | 灰色: 用于鼠标悬停效果           |
| **.table-secondary** | 灰色: 表示内容不怎么重要         |
| **.table-light**     | 浅灰色，可以是表格行的背景       |
| **.table-dark**      | 深灰色，可以是表格行的背景       |

```html
<table class="table">
    <thead>
        <tr>
            <th>编号</th>
            <th>姓名</th>
            <th>年龄</th>
            <th>手机号</th>
        </tr>
    </thead>
    <tbody>
        <tr class="table-primary">
            <td>1</td>
            <td>张三</td>
            <td>22</td>
            <td>13333445566</td>
        </tr>
        <tr class="table-secondary">
            <td>2</td>
            <td>李四</td>
            <td>21</td>
            <td>13211223344</td>
        </tr>
        <tr class="table-success">
            <td>3</td>
            <td>王五</td>
            <td>22</td>
            <td>13555667788</td>
        </tr>                   
    </tbody>
</table>
```

**创建带条纹行的表格**

通过添加 **.table-striped** 类，可在 `<tbody>` 内的行上看到条纹，即在基础表格的代码上为标签`<table>` 添加 **.table-striped** 类

**带边框表格**

通过将类 .table-bordered添加到 .table类创建的表格上，就可以在表格和单元格的所有边上添加边框

**无边框表格**

通过添加 **.table-borderless** 类可以取消表格和单元格所有边的边框

**鼠标悬停状态表格**

**.table-hover** 类可以为表格的每一行添加鼠标悬停效果（灰色背景）：

**创建较小的表格**

.table-sm 类用于通过减少内边距来设置较小的表格，使表格更紧凑并节省空间

**响应式表格**

要使任何表格具有响应性，只需将 .table 元素包裹在 .table-responsive 元素内，即可创建响应式表格。 还可以使用类 .table-responsive{-sm|-md|-lg|-xl} 根据视口宽度指定表格何时应具有滚动条。

注意.table-responsive类是在div外包裹一个div实现

```html
<div class="table-responsive">
     <table class="table">
     .......
     </table>
</div>
```

可以通过以下类设定在指定屏幕宽度下显示滚动条：

| 类名                      | 屏幕宽度 |
| :------------------------ | :------- |
| **.table-responsive-sm**  | < 576px  |
| **.table-responsive-md**  | < 768px  |
| **.table-responsive-lg**  | < 992px  |
| **.table-responsive-xl**  | < 1200px |
| **.table-responsive-xxl** | < 1400px |

**总结**

| 类名                  | 作用                        |
| :-------------------- | :-------------------------- |
| .table                | 普通表格                    |
| .table-sm             | 小表格                      |
| .table-bordered       | 表格边框                    |
| .table-borderless     | 无边框                      |
| .table-striped        | 条纹型表格                  |
| .table-hover          | 光标悬浮时，行样式变化      |
| .table-primary        | 背景色为主色调的表格        |
| .table-secondary      | 背景色为次要色调的表格      |
| .table-success        | 边框颜色为success颜色的表格 |
| .table-info           | 背景色为info颜色的表格      |
| .table-warning        | 背景色为警告颜色的表格      |
| .table-danger         | 背景色为危险颜色的表格      |
| .table-light          | 背景色为亮色的表格          |
| .table-dark           | 背景色为深色的表格          |
| .table-active         | 背景色为活跃的颜色的表格    |
| .thead-dark           | 深色的表头                  |
| .thead-light          | 亮色的表头                  |
| .table-responsive[-*] | *可选 sm、md、lg 、xl或xxl  |

### Bootstrap5 图像

**圆角图片**

.rounded类为图像添加圆角：

```html
<img src="1.png" class="rounded">
```

**圆形**

.rounded-circle类可以设置椭圆形图片：

```html
<img src="1.png" class="rounded-circle">
```

**缩略图**

.img-thumbnail类用于设置图片缩略图(图片有边框)

```html
<img src="1.png" class="img-thumbnail">
```

**对齐图像**

使用 .float-start类将图像向左浮动，或使用 .float-end向右浮动：

```html
<img src="1.png" class="float-start">
<img src="2.png" class="float-end">
```

**图片居中**

使用 **.mx-auto (margin:auto)** 和 .**d-block (display:block)** 类来设置图片居中对齐：注意，是两个一起使用

```html
<img src="1.png" class="mx-auto d-block">
```

**响应式图片**

图像有各种各样的尺寸，我们需要根据屏幕的大小自动适应。

我们可以通过在 **<img>** 标签中添加 **.img-fluid** 类来设置响应式图片。

此类主要将样式 max-width: 100%;和 height: auto;应用于图像，以便它很好地缩放以适合包含元素。

```html
<img src="1.png" class="float-start">
```

### Bootstrap5 按钮

**Bootstrap 按钮样式**

Bootstrap 内置了几种预定义的按钮样式，每种样式都有自己的语义目的，并添加了一些额外的按钮。

任何带有 class **.btn** 的元素都会继承圆角按钮的默认外观

```html
<button type="button" class="btn">基本按钮</button>
<button type="button" class="btn btn-primary">主要按钮</button>
<button type="button" class="btn btn-secondary">次要按钮</button>
<button type="button" class="btn btn-success">成功</button>
<button type="button" class="btn btn-info">信息</button>
<button type="button" class="btn btn-warning">警告</button>
<button type="button" class="btn btn-danger">危险</button>
<button type="button" class="btn btn-dark">黑色</button>
<button type="button" class="btn btn-light">浅色</button>
<button type="button" class="btn btn-link">链接</button>
```

按钮类可用于 `<a>`、`<button>` 或 `<input>` 元素：

```html
<a href="#" class="btn btn-success">链接按钮</a>
<button type="button" class="btn btn-success">按钮</button>
<input type="button" class="btn btn-success" value="输入按钮">
<input type="submit" class="btn btn-success" value="提交按钮">
<input type="reset" class="btn btn-success" value="重置按钮">
```

**按钮设置边框**

Bootstrap 5 还提供了八个轮廓/边框按钮。鼠标移动到按钮上添加突出到效果：

```html
<button type="button" class="btn btn-outline-primary">主要</button>
<button type="button" class="btn btn-outline-secondary">次要</button>
<button type="button" class="btn btn-outline-success">成功</button>
<button type="button" class="btn btn-outline-info">信息</button>
<button type="button" class="btn btn-outline-warning">警告</button>
<button type="button" class="btn btn-outline-danger">危险</button>
<button type="button" class="btn btn-outline-dark">深色</button>
<button type="button" class="btn btn-outline-light text-dark">浅色</button>
```

**按钮尺寸**

Bootstrap 5 可以设置按钮的大小，使用 **.btn-lg** 类设置大按钮，使用 **.btn-sm** 类设置小按钮：

```html
<button type="button" class="btn btn-primary btn-lg >大号按钮</button>
<button type="button" class="btn btn-primary btn-sm">小号按钮</button>
```

**块级按钮**

如需创建跨越父元素整个宽度的块级按钮，通过添加 **.btn-block** 类可以设置块级按钮，**.d-grid** 类设置在父级元素中：

```html
<div class="d-grid">
   <button type="button" class="btn btn-primary btn-block">100% 宽度的按钮</button>
</div>
```

| Class      | 描述                                         |
| :--------- | :------------------------------------------- |
| .btn-lg    | 这会让按钮看起来比较大。                     |
| .btn-sm    | 这会让按钮看起来比较小。                     |
| .btn-block | 这会创建块级的按钮，会横跨父元素的全部宽度。 |

**活动/禁用按钮**

按钮可设置为激活或者禁止点击的状态。

**.active** 类可以设置按钮是可用的， **disabled** 属性可以设置按钮是不可点击的。 注意 `<a>` 元素不支持 disabled 属性，你可以通过添加 **.disabled** 类来禁止链接的点击。

```html
<button type="button" class="btn btn-primary active">点击后的按钮</button>
<button type="button" class="btn btn-primary" disabled>禁止点击的按钮</button>
<a href="#" class="btn btn-primary disabled">禁止点击的链接</a>
```

 .disabled类只会使链接在视觉上看起来像已禁用，但是除非从中删除 href属性，否则该链接将保持可点击状态。

### Bootstrap5 按钮组

要创建一个按钮组，只需将一系列具有 .btn类的按钮包装在`<div>`元素中，然后在其上应用 .btn-group类。 还可以在单个按钮上应用 .active类以指示活动状态。 

**基本的按钮组**

```html
<div class="btn-group">
      <button type="button" class="btn btn-success active">按钮一</button>
      <button type="button" class="btn btn-warning">按钮二</button>
      <button type="button" class="btn btn-danger">按钮三</button>
  </div>
```

**按钮组的大小**

以使用 **.btn-group-lg|sm|xs** 类来设置按钮组的大小，可应用到整个按钮组的大小调整，而不需要对每个按钮进行大小调整。

```html
<div class="btn-group btn-group-lg">
  <button type="button" class="btn btn-success">按钮一</button>
  <button type="button" class="btn btn-warning">按钮二</button>
  <button type="button" class="btn btn-danger">按钮三</button>
</div>
```

**垂直按钮组**

可以使按钮组显示为垂直堆叠而不是水平堆叠。 为此，只需将类 .btn-group替换为类 .btn-group-vertical，

```html
<div class="btn-group-vertical">
        <button type="button" class="btn btn-primary">标题一</button>
        <button type="button" class="btn btn-primary">标题二</button>
        <button type="button" class="btn btn-primary">标题二</button>
</div>
```

### Bootstrap5 加载器

使用Bootstrap读取图标以表示元件加载状态，这些读取图标完全使用HTML，CSS。要创建 spinner/加载器，可以使用 .spinner-border 类：

```html
<div class="spinner-border"></div>
```

可以使用文本颜色类设置不同的颜色：

```html
<div class="spinner-border text-muted"></div>
<div class="spinner-border text-primary"></div>
<div class="spinner-border text-success"></div>
<div class="spinner-border text-info"></div>
<div class="spinner-border text-warning"></div>
<div class="spinner-border text-danger"></div>
<div class="spinner-border text-secondary"></div>
<div class="spinner-border text-dark"></div>
<div class="spinner-border text-light"></div>
```

**闪烁的加载效果**

使用 .spinner-grow 类来设置闪烁的加载效果

```html
<div class="spinner-grow text-muted"></div>
<div class="spinner-grow text-primary"></div>
<div class="spinner-grow text-success"></div>
<div class="spinner-grow text-info"></div>
<div class="spinner-grow text-warning"></div>
<div class="spinner-grow text-danger"></div>
<div class="spinner-grow text-secondary"></div>
<div class="spinner-grow text-dark"></div>
<div class="spinner-grow text-light"></div>
```

**设置加载效果大小**

使用.spinner-border-sm 或 .spinner-grow-sm 类来创建加载效果的大小:

```html
<div class="spinner-border spinner-border-sm"></div>
<div class="spinner-grow spinner-grow-sm"></div>
```

**加载按钮**

```html
<button class="btn btn-primary">
  <span class="spinner-border spinner-border-sm"></span>
  加载..
</button>
```

```html
<button class="btn btn-primary" disabled>
  <span class="spinner-border spinner-border-sm"></span>
  禁用..
</button>
```

### Bootstrap5 进度条

进度条可用于向用户显示任务或操作的进度。进度条（progress bar）支持堆叠、动画背景和文本标签。

工作原理:

- 我们使用 .progress作为最外层元素，用于指示进度条（progress bar）的最大值。
- 我们在内部使用 .progress-bar来指示到目前为止的进度。
- .progress-bar 需要通过内联样式、工具类或自定义 CSS 属性来设置其宽度。

下面的示例将展示如何创建一个带有垂直渐变的简单进度条。

```html
<div class="progress">
    <div class="progress-bar" style="width: 50%"></div>
</div>
```

**进度条的高度**

进度条的高度默认为 1rem（通常为 16px)，但我们也可以根据需要通过在 **.progress** 元素上设置 CSS height 属性来设置其高度

注意，必须为进度容器和进度条设置相同的高度：

```html
<!-- 2px 高度进度条-->
<div class="progress" style="height: 2px;">
    <div class="progress-bar" style="width: 50%;"></div>
</div>
<!-- 20px 高度进度条 -->
<div class="progress" style="height: 20px;">
    <div class="progress-bar" style="width: 50%;"></div>
</div>
```

**进度条标签**

通过在 .progress-bar元素内添加文本，就可以为进度条（progress bar）添加标签，以显示可见的百分比。

```html
<div class="progress">
        <div class="progress-bar" style="width: 60%">
            60%
        </div>
</div>
```

**进度条颜色**

可以使用背景颜色实用程序类来创建各种颜色的进度条，以便通过不同颜色传达不同的含义。默认情况下，进度条为蓝色（主要）。

```html
<div class="progress">
    <div class="progress-bar bg-info" style="width: 20%"></div>
</div>
<div class="progress">
    <div class="progress-bar bg-success" style="width: 40%"></div>
</div>
<div class="progress">
    <div class="progress-bar bg-warning" style="width: 80%"></div>
</div>
<div class="progress">
    <div class="progress-bar bg-danger" style="width: 90%"></div>
</div>
```

**条纹的进度条**

要创建条纹的进度条，只需向 .progress-bar元素添加一个额外的类 .progress-bar-striped

条纹是通过进度条背景颜色上的 CSS 渐变生成的。与纯色类似，还可以使用相同的背景色实用程序类创建不同颜色的带条纹的进度条

```html
<div class="progress">
   <div class="progress-bar progress-bar-striped" style="width: 40%;"></div>
</div>
<!-- Orange -->
<div class="progress">
   <div class="progress-bar bg-warning progress-bar-striped" style="width: 50%;"></div>
</div>
<!-- Red -->
<div class="progress">
    <div class="progress-bar bg-danger progress-bar-striped" style="width:60%"></div>
</div>
```

**进度条动画**

 将类 .progress-bar-animated添加到带有类 .progress-bar的元素上可以为条纹的进度条设置动画，它将通过 CSS3 动画从右到左为条纹设置动画。

```html
<div class="progress">
    <div class="progress-bar progress-bar-striped progress-bar-animated" style="width: 60%"></div>
</div>
```

**混合色彩进度条**

可以在一个进度组件中放置多个进度条来使它们并排在一起，进度条也可以堆叠

```html
<div class="progress">
        <div class="progress-bar bg-success" style="width: 40%">
            空闲空间  (40%)
        </div>
        <div class="progress-bar bg-warning" style="width: 25%">
            警告 (25%)
        </div>
        <div class="progress-bar bg-danger" style="width: 15%">
            危险 (15%)
        </div>
</div>  
```

### Bootstrap5 小工具

Bootstrap 5 提供了很多有用的类来帮助我们快速实现效果，不需要重复写一些 CSS 代码，可以在不使用任何 CSS 代码的情况下快速设置元素样式。

#### 边框类

使用边框类为元素添加或删除边框：

```html
<div class="border bg-light border-primary"></div>  边框颜色
<div class="border border-3"></div>           边框宽度
<div class="border border-top-0"></div>       顶部无边框
<div class="border border-right-0"></div>     右侧无边框
<div class="border border-bottom-0"></div>    底部无边框 
<div class="border border-left-0"></div>      左侧无边框

<div class="border-top"></div>        顶部边框
<div class="border-end"></div>        右侧边框
<div class="border-bottom"></div>     底部边框
<div class="border-start"></div>      左侧边框
```

**边框圆角**

使用 rounded类为元素添加圆角：

```html
<div class="rounded bg-dark"></div>
<div class="rounded-top bg-dark"></div>
<div class="rounded-end bg-dark"></div>
<div class="rounded-bottom bg-dark"></div>
<div class="rounded-start bg-dark"></div>
<div class="rounded-circle bg-dark"></div>
<div class="rounded-pill bg-dark" style="width:130px"></div>
<div class="rounded-0 bg-dark"></div>
<div class="rounded-1 bg-dark"></div>
<div class="rounded-2 bg-dark"></div>
<div class="rounded-3 bg-dark"></div>
```

| 类名              | 作用                             |
| :---------------- | :------------------------------- |
| .border           | 在元素的所有边添加边框           |
| .border-top       | 顶部边框                         |
| .border-end       | 右侧边框                         |
| .border-bottom    | 底部边框                         |
| .border-start     | 左侧边框                         |
| .border-*         | *取值为0~5                       |
| .border-top-0     | 顶部无边框                       |
| .border-right-0   | 右侧无边框                       |
| .border-bottom-0  | 底部无边框                       |
| .border-left-0    | 左侧无边框                       |
| .border-primary   | 主色调边框 蓝色                  |
| .border-secondary | 次要色调边框 灰色                |
| .border-success   | 成功边框 绿色                    |
| .border-info      | 信息边框 蓝绿色                  |
| .border-warning   | 警告边框 黄色                    |
| .border-danger    | 危险边框 红色                    |
| .border-light     | 浅色边框  浅灰色                 |
| .border-dark      | 深灰色边框                       |
| .border-white     | 白色边框                         |
| .rounded          | 边框圆角                         |
| .rounded-top      | 圆形元素的左上角和右上角。       |
| .rounded-end      | 圆形元素的右上角和右下角。       |
| .rounded-bottom   | 圆形元素的左下角和右下角。       |
| .rounded-start    | 圆形元素的左上角和左下角。       |
| .rounded-circle   | 椭圆形边框                       |
| .rounded-pill     | 胶囊型边框                       |
| .rounded-0        | 从元素中删除圆角。               |
| .rounded-1        | 将元素的边界半径设置为 0.2rem。  |
| .rounded-2        | 将元素的边界半径设置为 0.25rem。 |
| .rounded-3        | 将元素的边界半径设置为 0.3rem。  |

#### 浮动与清除浮动

元素向右浮动使用 .float-end 类，向左浮动使用 .float-start 类， .clearfix 类用于清除浮动

```html
<div class="clearfix">
        <span class="float-start">向左浮动</span>
        <span class="float-end">向右浮动</span>
</div>
```

响应式浮动

可以根据屏幕尺寸来设置浮动效果.float-*-left|right - * 表示

- sm(> = 576px)
- md (> = 768px)
- lg(> = 992px)
- xl (> = 1200px)
- xxl (> = 1400px)

```html
<div class="float-sm-end">在小型屏幕或更宽的屏幕上向右浮动</div>
<div class="float-md-end">在中型屏幕或更宽的屏幕上向右浮动</div>
<div class="float-lg-end">在大型屏幕或更宽的屏幕上向右浮动</div>
<div class="float-xl-end">在超大型屏幕或更宽的屏幕上向右浮动</div>
<div class="float-xxl-end">在特大型屏幕或更宽的屏幕上向右浮动</div>
```

#### 居中对齐

使用 .mx-auto 类来设置元素居中对齐 (添加 margin-left 和 margin-right 为 auto)

```html
<div class="mx-auto bg-primary" style="width:150px">居中对齐</div>
```

#### 文本对齐类

| Class                         | 描述                                         |
| ----------------------------- | -------------------------------------------- |
| .text-start                   | 文本左对齐。                                 |
| .text-center                  | 文本居中对齐。                               |
| .text-end                     | 文本右对齐。                                 |
| .text-wrap                    | 隐藏溢出的文本                               |
| .text-nowrap                  | 防止文本换行                                 |
| .text-truncate                | 使用省略号截断文本。                         |
| .text-break                   | 将长文本截断，防止溢出。                     |
| .text-lowercase               | 将文本转换为小写                             |
| .text-uppercase               | 将文本转换为大写                             |
| .text-capitalize              | 将每个单词的第一个字母转换成大写。           |
| .fw-bold                      | 将元素的字体设置为粗体                       |
| .fw-bolder                    | 将元素的字体粗细设置为更粗（相对于父元素）。 |
| .fw-normal                    | 将元素的字体设置为正常。                     |
| .fw-light                     | 将元素的字体设置为细体。                     |
| .fw-lighter                   | 将元素的字体粗细设置为更细（相对于父元素）。 |
| .fst-italic                   | 将元素的字体样式设置为斜体。                 |
| .fst-normal                   | 将元素的字体样式设置为正常。                 |
| .text-decoration-none         | 从文本中删除文本修饰，例如下划线。           |
| .text-decoration-underline    | 给文本添加下划线。                           |
| .text-decoration-line-through | 在文本中间添加一行。                         |

#### 宽度

使用 w-* 类（.w-25、.w-50、.w-75、.w-100、.mw-auto、.mw-100）设置元素的宽度

```html
<div class="w-25 bg-primary">宽度为 25%</div>
<div class="w-50 bg-primary">宽度为 50%</div>
<div class="w-75 bg-primary">宽度为 75%</div>
<div class="w-100 bg-primary">宽度为 100%</div>
<div class="w-auto bg-primary">自动设置宽度</div>
<div class="mw-100 bg-primary">最大宽度为 100%</div>
```

#### 高度

使用 h-* 类（.h-25、.h-50、.h-75、.h-100、.mh-auto、.mh-100）设置元素的高度

```html
<div class="bg-light" style="height:300px">
     <div class="h-25 bg-warning">高度为 25%</div>
     <div class="h-50 bg-warning">高度 50%</div>
     <div class="h-75 bg-warning">高度 75%</div>
     <div class="h-100 bg-warning">高度 100%</div>
     <div class="h-auto bg-warning">自动高度</div>
</div>
```

| 类名        | 作用          |
| :---------- | :------------ |
| .w-25       | 宽度25%       |
| .w-50       | 宽度50%       |
| .w-75       | 宽度75%       |
| .w-100      | 宽度100%      |
| .w-auto     | 宽度自动      |
| .mw-100     | 最大宽度100%  |
| .min-vw     | 最小宽度100vw |
| .vw-100     | 宽度100vw     |
| .h-25       | 高度25%       |
| .h-50       | 高度50%       |
| .h-75       | 高度75%       |
| .h-100      | 高度100%      |
| .h-auto     | 高度自动      |
| .mh-100     | 最大高度100%  |
| .min-vh-100 | 最小高度100vh |
| .vh-100     | 高度100vh     |

#### 间距

在元素中涉及使用内间距或者外间距时，p-内间距，这个Class属性会设定 padding值，m-外间距，这个Class属性会设定 margin值

- `m` - 用来设置 `margin`
- `p` - 用来设置 `padding`

**间距的方向**

t - 用来设置 margin-top或 padding-top
b- 用来设置 margin-bottom或 padding-bottom
s- 用来设置 margin-left或 padding-left
e- 用来设置 margin-right或 padding-right
x - 用来设置 left和 right
y - 用来设置 top和 bottom
blank - 用来设置元素在四个方向的 margin或 padding

**间距的距离**

0 - 把 margin 或 padding 设置为 0
1 - 把 margin 或 padding 设置为 .25rem
2 - 把 margin 或 padding 设置为 .5rem
3 - 把 margin 或 padding 设置为 1rem
4 - 把 margin 或 padding 设置为 1.5rem
5 - 把 margin 或 padding 设置为 3rem
auto - 把 margin 设置为 auto

```html
<div class="pt-4 bg-warning">我只有上内边距 (1.5rem)</div>
<div class="p-5 bg-success">我在所有边都有内边距 (3rem)</div>
<div class="m-5 pb-5 bg-info">我在所有边都有外边距 (3rem) 和下内边距 (3rem)</div>
```

| .m-*     | *可取值为0~5，外边距取值分别为为 0, 0.25rem, 0.5rem, 1rem, 1.5rem, 3rem |
| -------- | ------------------------------------------------------------ |
| .mt-*    | *可取值为0~5，顶部外边距取值分别为为 0, 0.25rem, 0.5rem, 1rem, 1.5rem, 3rem |
| .me-*    | *可取值为0~5，右侧外边距取值分别为为 0, 0.25rem, 0.5rem, 1rem, 1.5rem, 3rem |
| .mb-*    | *可取值为0~5，底部外边距取值分别为为 0, 0.25rem, 0.5rem, 1rem, 1.5rem, 3rem |
| .ms-*    | *可取值为0~5，左侧外边距取值分别为为 0, 0.25rem, 0.5rem, 1rem, 1.5rem, 3rem |
| .mx-*    | *可取值为0~5，左右外边距取值分别为为 0, 0.25rem, 0.5rem, 1rem, 1.5rem, 3rem |
| .mx-auto | 居中对齐                                                     |
| .my-*    | *可取值为0~5，上下外边距取值分别为为 0, 0.25rem, 0.5rem, 1rem, 1.5rem, 3rem |
| .p-*     | *可取值为0~5，内边距取值分别为为 0, 0.25rem, 0.5rem, 1rem, 1.5rem, 3rem |
| .pt-*    | *可取值为0~5，顶部内边距取值分别为为 0, 0.25rem, 0.5rem, 1rem, 1.5rem, 3rem |
| .pb-*    | *可取值为0~5，右侧内边距取值分别为为 0, 0.25rem, 0.5rem, 1rem, 1.5rem, 3rem |
| .ps-*    | *可取值为0~5，底部内边距取值分别为为 0, 0.25rem, 0.5rem, 1rem, 1.5rem, 3rem |
| .pe-*    | *可取值为0~5，左侧内边距取值分别为为 0, 0.25rem, 0.5rem, 1rem, 1.5rem, 3rem |
| .px-*    | *可取值为0~5，左右内边距取值分别为为 0, 0.25rem, 0.5rem, 1rem, 1.5rem, 3rem |
| .py-*    | *可取值为0~5，上下内边距取值分别为为 0, 0.25rem, 0.5rem, 1rem, 1.5rem, 3rem |

响应式 margin x 实用工具，调整浏览器窗口大小以查看效果：

```html
<div class="mx-sm-5 bg-primary">在 SM 屏幕上的 Margin x 5</div>
<div class="mx-md-5 bg-primary">在 MD 屏幕上的 Margin x 5</div>
<div class="mx-lg-5 bg-primary">在 LG 屏幕上的 Margin x 5</div>
<div class="mx-xl-5 bg-primary">在 XL 屏幕上的 Margin x 5</div>
```

#### 阴影

可以使用 shadow-类为元素添加阴影

| 类             | 说明                   |
| :------------- | :--------------------- |
| `.shadow`      | 为元素添加阴影。       |
| `.shadow-sm`   | 为元素添加一个小阴影。 |
| `.shadow-lg`   | 为元素添加更大的阴影。 |
| `.shadow-none` | 从元素中移除阴影。     |

```html
<div class="shadow p-4 mt-4">没有阴影</div>
```

### Bootstrap 5 Flex

弹性盒子是 CSS3 的一种新的布局模式，更适合响应式的设计。

**创建一个弹性盒子容器**

使用 d-flex类，创建 flexbox 容器并将直接子项转换为 flex 项

```html
<div class="d-flex p-3 bg-info text-white">
    <div class="p-2 bg-secondary">弹性项目 1</div>
    <div class="p-2 bg-secondary">弹性项目 2</div>
    <div class="p-2 bg-secondary">弹性项目 3</div>
</div>
```

使用d-inline-flex类创建行内 flexbox 容器，

```html
<div class="d-inline-flex p-3 bg-info text-white">
    <div class="p-2 bg-secondary">弹性项目 1</div>
    <div class="p-2 bg-secondary">弹性项目 2</div>
    <div class="p-2 bg-secondary">弹性项目 3</div>
</div>
```

**水平方向**

**.flex-row** 可以设置弹性子元素水平显示，这是默认的。

使用 **.flex-row-reverse** 类用于设置右对齐显示，即与 **.flex-row** 方向相反。

```
<div class="d-flex p-3 bg-info text-white flex-row-reverse">
    <div class="p-2 bg-secondary">弹性项目 1</div>
    <div class="p-2 bg-secondary">弹性项目 2</div>
    <div class="p-2 bg-secondary">弹性项目 3</div>
</div>
```

**垂直方向**

使用 .flex-column垂直显示 flex 项目（彼此堆叠），或使用 .flex-column-reverse反转垂直方向

```html
<div class="d-flex flex-column">
     <div class="p-2 bg-danger">弹性项目 1</div>
     <div class="p-2 bg-warning">弹性项目 2</div>
     <div class="p-2 bg-primary">弹性项目 3</div>
</div>

<div class="d-flex mt-3 flex-column-reverse">
      <div class="p-2 bg-danger">弹性项目 1</div>
      <div class="p-2 bg-warning">弹性项目 2</div>
      <div class="p-2 bg-primary">弹性项目 3</div>
</div>
```

**换行**

默认情况下，项目都排在一条线（又称”轴线”）上。flex-wrap属性定义，如果一条轴线排不下，如何换行。

flex-nowrap（默认）：不换行。
flex-wrap：换行，第一行在上方。
flex-wrap-reverse：换行，第一行在下方。

```html
<div class="d-flex mt-3 flex-wrap bg-info">
  <div class="p-2 bg-danger">弹性项目 1</div>
  <div class="p-2 bg-warning">弹性项目 2</div>
  <div class="p-2 bg-primary">弹性项目 3</div>
  <div class="p-2 bg-danger">弹性项目 1</div>
  <div class="p-2 bg-warning">弹性项目 2</div>
  <div class="p-2 bg-primary">弹性项目 3</div>
</div>
```

**对齐内容**

使用 .justify-content-* 类可改变弹性项目的对齐方式。***** 号允许的值有

- start（默认）
- end
- center
- between
- around

```html
<div class="d-flex mt-3 justify-content-center">
  <div class="p-2 bg-danger">弹性项目 1</div>
  <div class="p-2 bg-warning">弹性项目 2</div>
  <div class="p-2 bg-primary">弹性项目 3</div>
</div>
```

使用 .align-items-* 类控制单行弹性项目的垂直对齐方式，***** 号允许的值有

- start
- end
- center
- baseline
- stretch（默认值）

```html
<div class="d-flex mt-3 align-items-center bg-info" style="height: 300px">
  <div class="p-2 bg-danger">弹性项目 1</div>
  <div class="p-2 bg-warning">弹性项目 2</div>
  <div class="p-2 bg-primary">弹性项目 3</div>
</div>
```

使用 .align-content-* 用于控制多行项目的对齐方式，***** 号允许的值有

- start
- end
- center
- between
- around
- stretch（默认值）

```html
<div class="d-flex mt-3 flex-wrap bg-info align-content-between" style="height:300px; width:500px">
  <div class="p-2 bg-danger">弹性项目 1</div>
  <div class="p-2 bg-warning">弹性项目 2</div>
  <div class="p-2 bg-primary">弹性项目 3</div>
  <div class="p-2 bg-danger">弹性项目 1</div>
  <div class="p-2 bg-warning">弹性项目 2</div>
  <div class="p-2 bg-primary">弹性项目 3</div>
  <div class="p-2 bg-danger">弹性项目 1</div>
  <div class="p-2 bg-warning">弹性项目 2</div>
  <div class="p-2 bg-primary">弹性项目 3</div>
</div>
```

**顺序**

使用 .order类可更改特定 flex 项的视觉顺序，其中最低的数字具有最高的优先级（order-1 显示在 order-2 之前，以此类推）默认为0。

```html
<div class="d-flex">
  <div class="p-2 bg-secondary">弹性项目 1</div>
  <div class="p-2 bg-warning order-3">弹性项目 2</div>
  <div class="p-2 bg-primary order-1">弹性项目 3</div>
</div>
```

**等宽**

在 flex 项目上使用 .flex-fill可强制它们等宽

```html
<div class="d-flex">
  <div class="p-2 bg-secondary flex-fill">弹性项目 1</div>
  <div class="p-2 bg-warning flex-fill">弹性项目 2</div>
  <div class="p-2 bg-primary flex-fill">弹性项目 3</div>
</div>
```

**伸展**

在 flex 项目上使用 .flex-grow-1可占用多余的空间，

```html
<div class="d-flex bg-info" style="height:300px; width:500px">
  <div class="p-2 bg-secondary">弹性项目 1</div>
  <div class="p-2 bg-warning">弹性项目 2</div>
  <div class="p-2 bg-primary flex-grow-1">弹性项目 3</div>
</div>
```

**指定子元素对齐**

要设置指定子元素对齐对齐可以使用 .align-self-* 类来控制，align-self-*属性允许单个项目有与其他项目不一样的对齐方式

- start
- end
- center
- baseline
- stretch（默认值）

```html
<div class="box d-flex bg-info" style="height:300px; width:500px">
  <div class="p-2 bg-secondary">弹性项目 1</div>
  <div class="p-2 bg-warning align-self-center">弹性项目 2</div>
  <div class="p-2 bg-primary">弹性项目 3</div>
</div>
```

**响应式 flex 类**

可以根据不同的设备设置 flex 类，从而实现页面响应式布局， * 号可以的值有：sm, md, lg 或 xl, 对应的是小型设备、中型设备，大型设备，超大型设备。

| 类                           | 实例                                                   |
| :--------------------------- | :----------------------------------------------------- |
| 弹性容器                     |                                                        |
| `.d-*-flex`                  | 根据不同的屏幕设备创建弹性盒子容器                     |
| `.d-*-inline-flex`           | 根据不同的屏幕设备创建行内弹性盒子容器                 |
| 方向                         |                                                        |
| `.flex-*-row`                | 根据不同的屏幕设备在水平方向显示弹性子元素             |
| `.flex-*-row-reverse`        | 根据不同的屏幕设备在水平方向显示弹性子元素，且右对齐   |
| `.flex-*-column`             | 根据不同的屏幕设备在垂直方向显示弹性子元素             |
| `.flex-*-column-reverse`     | 根据不同的屏幕设备在垂直方向显示弹性子元素，且方向相反 |
| 内容对齐                     |                                                        |
| `.justify-content-*-start`   | 根据不同屏幕设备在开始位置显示弹性子元素 (左对齐)      |
| `.justify-content-*-end`     | 根据不同屏幕设备在尾部显示弹性子元素 (右对齐)          |
| `.justify-content-*-center`  | 根据不同屏幕设备在 flex 容器中居中显示子元素           |
| `.justify-content-*-between` | 根据不同屏幕设备使用 "between" 显示弹性子元素          |
| `.justify-content-*-around`  | 根据不同屏幕设备使用 "around" 显示弹性子元素           |
| 等宽                         |                                                        |
| `.flex-*-fill`               | 根据不同的屏幕设备强制等宽                             |
| 扩展                         |                                                        |
| `.flex-*-grow-0`             | 不同的屏幕设备不设置扩展                               |
| `.flex-*-grow-1`             | 不同的屏幕设备设置扩展                                 |
| 包裹                         |                                                        |
| `.flex-*-nowrap`             | 不同的屏幕设备不设置包裹元素                           |
| `.flex-*-wrap`               | 不同的屏幕设备设置包裹元素                             |
| `.flex-*-wrap-reverse`       | 不同的屏幕设备反转包裹元素                             |
| 内容排列                     |                                                        |
| `.align-content-*-start`     | 根据不同屏幕设备在起始位置堆叠元素                     |
| `.align-content-*-end`       | 根据不同屏幕设备在结束位置堆叠元素                     |
| `.align-content-*-center`    | 根据不同屏幕设备在中间位置堆叠元素                     |
| `.align-content-*-around`    | 根据不同屏幕设备，使用 "around" 堆叠元素               |
| `.align-content-*-stretch`   | 根据不同屏幕设备，通过伸展元素来堆叠                   |
| 排序                         |                                                        |
| `.order-*-*0-12*`            | 在小屏幕尺寸上修改排序                                 |
| 元素对齐                     |                                                        |
| `.align-items-*-start`       | 根据不同屏幕设备，让元素在头部显示在同一行。           |
| `.align-items-*-end`         | 根据不同屏幕设备，让元素在尾部显示在同一行。           |
| `.align-items-*-center`      | 根据不同屏幕设备，让元素在中间位置显示在同一行。       |
| `.align-items-*-baseline`    | 根据不同屏幕设备，让元素在基线上显示在同一行。         |
| `.align-items-*-stretch`     | 根据不同屏幕设备，让元素延展高度并显示在同一行。       |
| 单独一个子元素的对齐方式     |                                                        |
| `.align-self-*-start`        | 据不同屏幕设备，让单独一个子元素显示在头部。           |
| `.align-self-*-end`          | 据不同屏幕设备，让单独一个子元素显示在尾部             |
| `.align-self-*-center`       | 据不同屏幕设备，让单独一个子元素显示在居中位置         |
| `.align-self-*-baseline`     | 据不同屏幕设备，让单独一个子元素显示在基线位置         |
| `.align-self-*-stretch`      | 据不同屏幕设备，延展一个单独子元素                     |

### Bootstrap5 字体图标

字体图标是在 Web 项目中使用的图标字体。

使用字体图标的好处是，可以通过应用 CSS color 属性来创建任何颜色的图标。 此外，要更改图标的大小，只需使用 CSS font-size 属性即可。

**获取字体图标**

在网页中包含 Bootstrap5 图标的最简单方法是使用 CDN 链接。 此 CDN 链接基本上指向一个远程 CSS 文件，其中包含生成字体图标所需的所有类。

我们可以在 Bootstrap 模板以及简单的网页中包含 Bootstrap5 图标，而无需使用 Bootstrap 框架。利用提供的公共 CDN 服务并将图标字体的样式表添加到网站的 `<head>` 标签内

**步骤 一:** 在HTML文档的`<head>`部分包含下面的 Bootstrap CDN 链接。

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.0/font/bootstrap-icons.css">
```

**步骤 二:** 要将图标放置到网页中，使用语法 `<i class="bi-*"></i>` 其中 `*` 代表特定图标的类名。 

```html
<i class="bi-archive-fill"></i>
```

