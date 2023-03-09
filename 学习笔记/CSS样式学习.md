## 重置样式

**前言：**因为目前主流浏览器都有一些各自默认的样式，例如默认情况下页面都有一个很小的padding等，所以就需要在每个项目开始时都清除掉它们。

Normalize.css 只是一个很小的 CSS 文件，但它在默认的HTML元素样式上提供了跨浏览器的高度一致性，十分推荐使用。

安装后在 main.js 引入即可

```
npm install normalize.css
```

```
import 'normalize.css'
```



## 创建项目时适配不同屏幕尺寸

pc端常用适配方法是使用vw和vh单位，rem曾经热门但现在已经逐步被替换。一般我们就可以使用less或scss在开发过程中就按照设计图的px单位计算出相应的vw单位；或者我们也可以先按px开发，打包时使用 **postcss-px-to-viewport** 这个包一步将px单位转为vw单位。

移动端可以使用rpx单位，固定将移动端宽度看为375 px，等效为750 rpx，也就是说1 rpx = 0.5 px，以此进行适配。

 **postcss-px-to-viewport** 需要在项目根目录下创建一个postcss.config.js:

```javascript
module.exports = {
  plugins: {
    'postcss-px-to-viewport': {
      unitToConvert: 'px', // 要转化的单位
      viewportWidth: 1920, // UI设计稿的宽度
      unitPrecision: 6, // 转换后的精度，即小数点位数
      propList: ['*'], // 指定转换的css属性的单位，*代表全部css属性的单位都进行转换
      viewportUnit: 'vw', // 指定需要转换成的视窗单位，默认vw
      fontViewportUnit: 'vw', // 指定字体需要转换成的视窗单位，默认vw
      selectorBlackList: [], // 指定不转换为视窗单位的类名
      minPixelValue: 1, // 默认值1，小于或等于1px则不进行转换
      mediaQuery: true, // 是否在媒体查询的css代码中也进行转换，默认false
      replace: true, // 是否转换后直接更换属性值
      exclude: [/node_modules/] // 设置忽略文件，用正则做目录名匹配
    }
  }
}
```



## 页面滚动条相关设置

```css
/*滚动条宽度*/
::-webkit-scrollbar {
	width: 8px;
	height: 8px;
}

/*滚动条轨道样式*/
::-webkit-scrollbar-track {
	background: rgb(239, 239, 239);
	border-radius: 2px;
}

/*滚动条滑轮样式*/
::-webkit-scrollbar-thumb {
	background: #c9c9c9;
	border-radius: 8px;
}

::-webkit-scrollbar-thumb:hover {
	background: rgb(156, 156, 156);
}

/*横纵滚动条拐角处样式*/
::-webkit-scrollbar-corner {
	background: #838383;
}
```



## 固定定位因滚动条的抖动问题

在开发过程中，遇到了固定定位的决头部导航栏在切换不同页面时，因为不同页面是否需要滚动的状态不同，页面间的滚动条展示状态也不同，这就导致了切换页面导航条位因滚动条的出现隐藏而出现的抖动问题。

我们可以用下面的样式解决：

```css
/* 解决头部导航栏因为滚动条出现隐藏的抖动问题 */
margin-right: calc(100% - 100vw);
```

给最右侧的盒子添加padding-right或margin-right即可。

calc(100% - 100vw)中  100vw是浏览器视口的宽度，而固定定位的100%是html页面的宽度，一减就是负的滚动条的宽度啦。当没用滚动条时值就是0 。当有滚动条时，就让右侧元素向右移动滚动条宽度。感观下来就是滚动条出现与否元素位置都没用变化。不过这样会导致右侧元素会被滚动条遮住一小部分。



## Gird网格布局

```css
.container {
      display: grid;
      /* 行列的分布 */
      grid-template-columns: 150px 150px 150px;
      grid-template-rows: repeat(4,100px);
      /* 单元格间距 */
      /* gap: 10px; */
      /* 默认横向排列，可以修改为纵向排列 */
      grid-auto-flow: column;

      /* 设置整个内容区域在大容器中的排布方式 */
      justify-content: center;
      align-content: center;

      /* 设置所有的小盒子在划定网格里的排布方式 */
      justify-items: center;
      align-items: end;


      width: 600px;
      height: 600px;
      background-color: rgb(219, 224, 225);
    }
    .item {
      width: 80px;
      height: 80px;
    }
    .item1 {
      /* 控制每个小盒子占据的网格范围 */
      grid-area: 1/1/3/3;
      background-color: rgb(220, 88, 88);
    }
    .item2 {
      /* 控制单个小盒子在所在网格的排布方式，可以覆盖掉在容器设置的整体排布 */
      justify-self: end;
      align-self: end;
      background-color: rgb(104, 201, 62);
    }
    .item3 {
      background-color: rgb(50, 175, 200);
    }
    .item4 {
      background-color: rgb(168, 71, 225);
    }
    .item5 {
      grid-area: 4/2/5/4;
      background-color: rgb(164, 147, 151);
    }
```



## 设置盒子宽高比达到页面缩放自适应

给盒子一个随页面变化自适应的百分比值，再使用宽高比属性 **aspect-ratio** 即可让高度随变化宽度自适应。

```css
.tools {
	box-sizing: border-box;
	width: 20%;
	aspect-ratio: 1.6875 / 1 ;
	padding: 0px 30px;
	margin-bottom: 30px;
}
```



## 多个选择器选中时权重值计算

单个选择器优先级： **行内样式 >  id 选择器 > 类，伪类，属性选择器 > 元素，伪元素选择器 > 通配符选择器**

多个选择器选中时权重值计算：

每一个选择器都能计算出一个【权重值】，格式为：(a, b, c)
a 表示：一个选择器中【ID】选择器的个数。
b 表示：一个选择器中【类、伪类、属性】选择器的个数。
c 表示：一个选择器中【元素、伪元素】选择器的个数。