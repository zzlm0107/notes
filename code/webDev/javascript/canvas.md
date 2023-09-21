## 初识canvas

`<canvas>`元素用于生成图像。它本身就像一个画布，JavaScript 通过操作它的 API，在上面生成图像。它的底层是一个个像素，基本上`<canvas>`是一个可以用 JavaScript 操作的位图（bitmap）。

它与 SVG 图像的区别在于，`<canvas>`是脚本调用各种方法生成图像，SVG 则是一个 XML 文件，通过各种子元素生成图像。

使用 Canvas API 之前，需要在网页里面新建一个`<canvas>`元素。

```html
<canvas id="canvas" width="400" height="250">
  您的浏览器不支持 Canvas
</canvas>
```

如果浏览器不支持这个 API，就会显示`<canvas>`标签中间的文字：“您的浏览器不支持 Canvas”。

每个`<canvas>`元素都有一个对应的`CanvasRenderingContext2D`对象（上下文对象）。Canvas API 就定义在这个对象上面。

```js
// 获取canvas标签对象
var canvas = document.getElementById('myCanvas');
// 调用标签上的方法，获取 2d 实例对象
var ctx = canvas.getContext('2d');
```

按照用途，Canvas API 分成两大部分：绘制图形和图像处理。





## 绘制基本图形

Canvas 画布提供了一个作图的平面空间，该空间的每个点都有自己的坐标。原点`(0, 0)`位于图像左上角，`x`轴的正向是原点向右，`y`轴的正向是原点向下。

下面是一些绘制时常见的方法：

```js
// 开始绘制路径
CanvasRenderingContext2D.beginPath()
// 结束路径，返回到当前路径的起始点，会从当前点到起始点绘制一条直线
CanvasRenderingContext2D.closePath()
// 设置路径的起点，即将一个新路径的起始点移动到(x，y)坐标
CanvasRenderingContext2D.moveTo()
// 使用直线从当前点连接到(x, y)坐标
CanvasRenderingContext2D.lineTo()

// 在路径内部填充颜色（默认为黑色）
CanvasRenderingContext2D.fill()
// 指定路径填充的颜色和样式（默认为黑色）
CanvasRenderingContext2D.fillStyle

// 路径线条着色（默认为黑色），即显示路径
CanvasRenderingContext2D.stroke()
// 指定路径线条的颜色和样式（默认为黑色）
CanvasRenderingContext2D.strokeStyle
```

**绘制矩形**

```js
// 都接受四个参数，分别是矩形左上角的横坐标和纵坐标、矩形的宽和高。

ctx.rect(x, y, width, height)			// 绘制矩形路径
ctx.fillRect(x, y, width, height)		// 绘制填充矩形
ctx.strokeRect(x, y, width, height)		// 绘制矩形边框
ctx.clearRect(x, y, width, height)		// 清除矩形范围内的像素
```

**绘制圆弧**

```js
// 绘制圆弧路径
// 参数： 起点x坐标，起点y坐标，半径，开始角度，结束角度，是否逆时针绘制
ctx.arc(x, y, radius, startAngle, endAngle, anticlockwise)
// 通过指定两根切线和半径绘制弧形
ctx.arcTo()

// 实例
ctx.arc(50, 50, 30, 0, 2 * Math.PI, true)
ctx.stroke()
```

**绘制二次贝塞尔曲线**

```js
// 确定绘制起点
ctx.moveTo(100, 100)
// 给定两点坐标，确定基线轨迹
ctx.quadraticCurveTo(200, 200, 0, 200)
ctx.stroke()
```



**封装路径path2D**

封装路劲就相当于将一段路径的绘制方法提取出来，然后我们就可以实现完整的移动路径，提高效率。

```js
// 创建一个2d路径对象
let chatPath = new Path2D()

// 为该路径对象添加路径的绘制方法
chatPath.arc(50, 50, 30, 0, 2 * Math.PI, true)
//...

// 绘制封装的路径
ctx.stroke(chatPath)
```





## 样式控制

样式控制主要是控制画笔的颜色，宽度等，从而实现更丰富的绘制效果。

**线条控制**

```js
// 指定线条的宽度，默认为1.0
CanvasRenderingContext2D.lineWidth = 2
// 指定显示路径的颜色，默认为黑色，可以使用rgba等
CanvasRenderingContext2D.strokeStyle = "#ff00ff"
// 指定填充的颜色
CanvasRenderingContext2D.fillStyle = "skyblue"
```

**颜色渐变**

```js
// 定义线性渐变样式, 参数分别是两点的坐标
// 方法会在两个位置之间，根据起点终点的颜色，生成一段渐变的颜色设置
CanvasRenderingContext2D.createLinearGradient(x1, y1, x2, y2)

// 实例
let gradient = ctx.createLinearGradient(0, 0, 200, 0) // 设置线性渐变范围
gradient.addColorStop(0, 'pink') // 起点颜色
gradient.addColorStop(1, 'skyblue') // 终点颜色
ctx.strokeStyle = gradient // 给需要渐变的地方赋值


// 定义径向渐变样式, 参数分别是两个圆的参数
CanvasRenderingContext2D.createRadialGradient(x0, y0, r0, x1, y1, r1)
gradient.addColorStop(0, 'white');
gradient.addColorStop(1, 'green');
ctx.fillStyle = gradient;


// 定义扇形渐变，参数：开始渐变的角度，渐变扇形原点
CanvasRenderingContext2D.createConicGradient(Math.PI / 2, 200, 200)
gradient.addColorStop(0, 'white');
gradient.addColorStop(1, 'green');
ctx.fillStyle = gradient;
```

**阴影**

```js
// 阴影的水平位移，默认为0
CanvasRenderingContext2D.shadowOffsetX = 5
// 阴影的垂直位移，默认为0
CanvasRenderingContext2D.shadowOffsetY = 5
// 阴影的模糊程度，默认为0
CanvasRenderingContext2D.shadowBlur = 8
// 阴影的颜色，默认为black
CanvasRenderingContext2D.shadowColor = 'red'

```





## 文字绘制

下面是一些常用的绘制文字的方法：

```js
// 在指定位置绘制实心字符
CanvasRenderingContext2D.fillText(text, x, y, [, maxWidth])
// 在指定位置绘制空心字符
CanvasRenderingContext2D.strokeText(text, x, y, [, maxWidth])

// 指定字型大小和字体
CanvasRenderingContext2D.font = '10px sans-serif'
// 指定文字对齐方式  start/center/end/left/right
CanvasRenderingContext2D.textAlign = 'start'
// 文本的方向 ltr/rtl
CanvasRenderingContext2D.direction = 'inherit'
// 文本基线对齐方向  top/middle/bottom ...
CanvasRenderingContext2D.textBaseline = 'alphabetic'
```





## 图像变换

以下方法用于图像变换。

- `CanvasRenderingContext2D.rotate()`：图像旋转
- `CanvasRenderingContext2D.scale()`：图像缩放
- `CanvasRenderingContext2D.translate()`：图像平移
- `CanvasRenderingContext2D.transform()`：通过一个变换矩阵完成图像变换
- `CanvasRenderingContext2D.setTransform()`：取消前面的图像变换

**旋转**

`CanvasRenderingContext2D.rotate()`方法用于图像旋转。它接受一个弧度值作为参数，表示顺时针旋转的度数。

```js
ctx.rotate(Math.PI / 4);
ctx.fillRect(70, 0, 100, 30);
```

上面代码会显示一个顺时针倾斜45度的矩形。注意，`rotate()`方法必须在`fillRect()`方法之前调用，否则是不起作用的。

旋转中心点始终是画布左上角的原点。如果要更改中心点，需要使用`translate()`方法移动画布。

**缩放**

`CanvasRenderingContext2D.scale()`方法用于缩放图像。它接受两个参数，分别是`x`轴方向的缩放倍率和`y`轴方向的缩放倍率。

```js
ctx.scale(10, 3);
ctx.fillRect(10, 10, 10, 10);
```

上面代码中，原来的矩形是 10 x 10，缩放后展示出来是 100 x 30。

如果缩放因子为1，就表示图像没有任何缩放。如果为-1，则表示方向翻转。`ctx.scale(-1, 1)`为水平翻转，`ctx.scale(1, -1)`表示垂直翻转。

**平移**

`CanvasRenderingContext2D.translate()`方法用于平移图像。它接受两个参数，分别是 x 轴和 y 轴移动的距离（单位像素）。

```js
ctx.fillRect(0, 0, 100, 100)

ctx.translate(50, 50)
ctx.fillStyle = 'red'
```

**组合操作**

`CanvasRenderingContext2D.transform()`方法接受一个变换矩阵的六个元素作为参数，完成缩放、旋转、移动和倾斜等变形。

它的使用格式如下。

```
ctx.transform(a, b, c, d, e, f);
/*
a:水平缩放(默认值1，单位倍数)
b:水平倾斜(默认值0，单位弧度)
c:垂直倾斜(默认值0，单位弧度)
d:垂直缩放(默认值1，单位倍数)
e:水平位移(默认值0，单位像素)
f:垂直位移(默认值0，单位像素)
*/
```



下面是一个例子。

```
var canvas = document.getElementById('myCanvas');
var ctx = canvas.getContext('2d');

ctx.transform(2, 0, 0, 1, 50, 50);
ctx.fillRect(0, 0, 100, 100);
```



上面代码中，原始图形是 100 x 100 的矩形，结果缩放成 200 x 100 的矩形，并且左上角从`(0, 0)`移动到`(50, 50)`。

注意，多个`transform()`方法具有叠加效果。

**取消变换**

`CanvasRenderingContext2D.setTransform()`方法取消前面的图形变换，将画布恢复到该方法指定的状态。该方法的参数与`transform()`方法完全一致。

```
ctx.translate(50, 50);
ctx.fillRect(0, 0, 100, 100);

ctx.setTransform(1, 0, 0, 1, 0, 0);
ctx.fillRect(0, 0, 100, 100);
```

上面代码中，第一个`fillRect()`方法绘制的矩形，左上角从`(0, 0)`平移到`(50, 50)`。`setTransform()`方法取消了这个变换（已绘制的图形不受影响），将画布恢复到默认状态（变换矩形`1, 0, 0, 1, 0, 0`），所以第二个矩形的左上角回到`(0, 0)`。





## 图像合成

在默认情况之下，如果在Canvas之中将某个物体（源）绘制在另一个物体（目标）之上，那么浏览器就会简单地把源特体的图像叠放在目标物体图像上面。

简单点讲，在Canvas中，把图像源和目标图像，通过Canvas中的`globalCompositeOperation`操作，可以得到不同的效果。

```js
ctx.globalCompositeOperation = 'source-over' // 默认，上层图片覆盖下层
```

具体的模式细节参考 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/globalCompositeOperation)



## 图片控制

Canvas API 允许将图像文件写入画布，做法是读取图片后，使用`drawImage()`方法将这张图片放上画布。

`CanvasRenderingContext2D.drawImage()`有三种使用格式。

```
ctx.drawImage(image, dx, dy);
ctx.drawImage(image, dx, dy, dWidth, dHeight);
ctx.drawImage(image, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight);
```



各个参数的含义如下。

- image：图像元素
- sx：图像内部的横坐标，用于映射到画布的放置点上。
- sy：图像内部的纵坐标，用于映射到画布的放置点上。
- sWidth：图像在画布上的宽度，会产生缩放效果。如果未指定，则图像不会缩放，按照实际大小占据画布的宽度。
- sHeight：图像在画布上的高度，会产生缩放效果。如果未指定，则图像不会缩放，按照实际大小占据画布的高度。
- dx：画布内部的横坐标，用于放置图像的左上角
- dy：画布内部的纵坐标，用于放置图像的右上角
- dWidth：图像在画布内部的宽度，会产生缩放效果。
- dHeight：图像在画布内部的高度，会产生缩放效果。



我们也可以利用canvas绘制图片的功能实现视频的展现控制，添加水印的操作。

```js
let video = document.querySelector('video')
let btn = document.querySelector('.btn')

// 按钮控制视频播放
btn.onclick = () => {
    if(video.paused) {
        video.play()
        render()
    } else {
        video.pause()
    }
}

// 
let img = new Image()
img.src = './images/logo.jpeg'
function render() {
    ctx.drawImage(video, 0, 0) // 绘制视频的当前帧
    ctx.drawImage(img, 0, 0) // 绘制水印图片
    requestAnimationFrame(render) // 浏览器每渲染一帧调用一次
}
```



此外，调用 `ctx.clip(path2D)`,传入设定的path2D路径，即可实现将画布裁剪。



## 保存与恢复

```js
// 将当前画布上的画笔等各种状态进行保存,放入一个栈中
ctx.save()

// 将栈中最新一次的状态恢复出来
ctx.restore()
```





## 像素操作

通过调用`ctx.getImageData(0,0,200,200)` 方法即可获取指定区域内的像素数据，数据是一个数组，每四个元素指定了当前像素的rgba值。

```js
let imgData = getImageData(0,0,200,200)

// 一些像素处理操作
for(let i=0, i<imageData.data.length, i+=4) {
    imageData.data[i] = 255 - imageData.data[i]
    imageData.data[i+1] = 255 - imageData.data[i+1]
    imageData.data[i+2] = 255 - imageData.data[i+2]
    imageData.data[i+3] = 255
}

// 将数据重新绘制到画布上
ctx.putImageData(imgData, 0, 0)
```

