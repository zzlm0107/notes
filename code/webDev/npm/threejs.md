---
title: Three.js 笔记
icon: model
date: 2023-06-18
category:
  - 第三方库
tag:
  - 模型
---


## 初识Three.js

在认识Three.js前，先简单了解一下Web 3D的一些前置概念：

- **OpenGL：** OpenGL是开放式图形标准，跨编程语言、跨平台，Javascript、Java 、C、C++ 、 python 等都能支持OpenGL ，OpenGL的Javascript实现就是 。OpenGL ES 2.0是OpenGL的子集，针对手机、游戏主机等嵌入式设备而设计。
- **WebGL：** WebGL是一种Javascript的3D图形接口，把JavaScript和OpenGL ES 2.0结合在一起。
- **Canvas：** Canvas是HTML5的画布元素，在使用Canvas时，需要用到Canvas的上下文，可以用2D上下文绘制二维的图像，也可以使用3D上下文绘制三维的图像，其中3D上下文就是指WebGL。



在了解到这些知识后，我们就可以简单定义 Three.js ：

**Three.js是基于webGL的封装的一个易于使用且轻量级的3D库，Three.js对WebGL提供的接口进行了非常好的封装，简化了很多细节，大大降低了学习成本，极大地提高了性能，功能也非常强大，用户不需要详细地学习 WebGL，就能轻松创作出三维图形，是前端开发者研发3D绘图的主要工具。**

[Three.js官网](https://threejs.org/docs/index.html#manual/zh/introduction/Installation)  官方文档，全面，适合查阅，新手阅读会摸不着头脑

[中文电子书](http://www.webgl3d.cn/pages/aac9ab/)  第三方文档，适合从0开始学习，熟悉各种常用 api 后再食用官方文档

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e4b7894fc49f4563959c41b410cf3149~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp" width="600px">



## 基础使用

使用three.js时，我们需要知道一个3D场景大致由几部分组成：场景，相机，渲染器 和 物体。

下面代码就是创建场景,透视相机,渲染器和物体的简单使用:

```js
import * as THREE from 'three' // 导入全部
//import { Scene } from 'three' // 分别导入

// 创建场景,场景是显示3D画面的基础
const scene = new THREE.Scene();

// 创建透视相机, 参数: 视野角度FOV,显示长宽比,近截面和远截面
const camera = new THREE.PerspectiveCamera( 75, window.innerWidth / window.innerHeight, 0.1, 1000 );
camera.position.z = 5; // 设置相机位置
camera.lookAt(0, 0, 0) // 设置相机朝向，默认为原点位置

// 创建渲染器,实现渲染物体的功能
const renderer = new THREE.WebGLRenderer();
renderer.setSize( window.innerWidth, window.innerHeight ); // 设置渲染器尺寸
document.body.appendChild( renderer.domElement ); // 添加渲染器dom元素,展示3D Canvas

// 创建物体并添加到场景中
const geometry = new THREE.BoxGeometry( 1, 1, 1 ); // 创建立方体(透明),参数:长宽高
const material = new THREE.MeshBasicMaterial( { color: 0x00ff00 } ); // 创建材质
const cube = new THREE.Mesh( geometry, material ); // 创建网格对象,是立方体和材质的组合
scene.add( cube ); // 添加到场景中

renderer.render(scene, camera); // 最后,调用渲染器渲染函数,渲染场景和相机
```

此时会发现我们的屏幕上出现了一个小方块,这就是我们创建物体的正面,但我们并没法滑动查看,滑动查看需要借助*轨道控制器* 来实现,当然我们可以添加坐标轴更方便调试:

```js
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'; // 引入轨道控制器

// 创建轨道控制器
const controls = new OrbitControls( camera, renderer.domElement );
controls.enableDamping = true; // 设置控制器阻尼

// 创建坐标轴
const axesHelper = new THREE.AxesHelper( 4 );
scene.add( axesHelper );

// 渲染循环,在每一帧都渲染场景和相机
function animate() {
  requestAnimationFrame(animate); // 让浏览器下一帧再次执行此渲染函数
  controls.update(); // 开启控制器更新
  renderer.render(scene, camera);
}
animate();
```



## 物体移动,旋转与缩放

**物体移动**

创建的网格对象即可见物体实例 cube 的 **position对象** 控制着物体的位置,我们可以通过 `cube.position.set(x,y,z)`方法控制物体的位置; 也可以直接修改属性 `cube.position.x = 3`.动态修改位置也只需要再 animate() 中随渲染变化值即可.

```js
function animate() {
  requestAnimationFrame(animate); // 让浏览器下一帧再次执行此渲染函数
  // controls.update();
  if (cube.position.x >= 0 && cube.position.x <= 4 ) {
    cube.position.x += 0.01
  } else {
    cube.position.x = 0
  }
  renderer.render(scene, camera);
}
animate();
```



**物体旋转**

物体旋转也可以直接使用 `cube.rotation.set(x,y,z, order)`方法控制物体的旋转角度和顺序; 也可以直接修改属性 `cube.rotation.x = Math.PI / 4`来控制某一个轴的旋转.

其中前三个参数是弧度，180°是 Π，计算即可，例如 Math.*PI* / 4 表示45°；

最后一个值是旋转顺序，如 ‘XZY’ 表示先旋转X轴，再旋转Z轴，最后旋转Y轴。



**物体缩放**

物体缩放也可以直接使用 `cube.scale.set(x,y,z)`方法控制物体的缩放大小; 也可以直接修改属性 `cube.scale.x = 3`来控制某一个轴的缩放.



## requestAnimationFrame跟踪时间

requestAnimationFrame(Fun) 是浏览器原生提供的一种方法，可以在浏览器下一帧渲染的时候再执行传入的函数。理论上60帧刷新的屏幕1s渲染60张画面，即约每16ms渲染1张画面。但实际上浏览器并不是每次都是准时渲染的，由于各种计算压力等原因，渲染速度可快可慢，而此时在其中的各种向量数据的加减就变得不可控起来。

例如希望原点位置的物块1s走到（1，0，0） 的位置，理论上应该是 1 * 60 = 0.016；即每一次渲染帧时向前走0.016；但由于渲染速度的不固定，也就是帧数的不固定，导致可能存在误差。

如果想要精准的随时间移动，则需要在传入的 Fun 里进行计算，利用一个time参数：

```js
function animate(time) { // 参数time就是执行此帧的时间，单位 ms
  requestAnimationFrame(animate); // 让浏览器下一帧再次执行此渲染函数

  let t = (time / 1000) // 转为 s 单位
  cube.position.x = t * 1
  
  renderer.render(scene, camera);
}
animate();
```



## Clock跟踪时间

可以通过库里的 [Clock时钟对象](https://threejs.org/docs/index.html?q=Clock#api/zh/core/Clock) 来跟踪处理时间。

创建： `const Clock = new THREE.Clock()`

- `Clock.getDelta()`  获取自 [.oldTime](https://threejs.org/docs/index.html#api/zh/core/Clock.oldTime) 设置后到当前的秒数。 同时将 `.oldTime` 设置为当前时间。即渲染时间间隔。

- `Clock.getElapsedTime()`  获取自时钟启动后的秒数，同时将 `.oldTime` 设置为当前时间。

此外还有许多其他属性和方法，参见官方文档。



## 自适应画面

现在如果我们窗口变化，我们会发现显示画面并不会自适应。这是因为我们在初始化的时候就已经设定好的透视相机和渲染器的尺寸是启动时窗口尺寸，即使我们后面窗口变化，这些设定也不会改变。

```js
// 监听画面变化，更新渲染画面
window.addEventListener('resize', () => {
  camera.aspect = window.innerWidth / window.innerHeight // 重新设置相机视锥长宽比
  camera.updateProjectionMatrix() // 更新摄像机投影矩阵。在任何参数被改变以后必须被调用
  renderer.setSize(window.innerWidth, window.innerHeight); // 重新设置渲染器尺寸
  renderer.setPixelRatio(window.devicePixelRatio) // 设置设备像素比,主要用于适配手机
})
```



## 画布全屏与退出

实现双击屏幕进入全屏：

```js
// 双击全屏
window.addEventListener('dblclick', () => {
  if (document.fullscreenElement) { // 判断当前全屏的元素
    document.exitFullscreen()
  } else {
    renderer.domElement.requestFullscreen()
  }
})
```



## 变量控制库dat.gui

我们可以使用 **dat.gui** 这个插件库很方便的调试我们的参数变量，实现动态预览。

下载：`yarn add dat.gui`

```js
import * as dat from 'dat.gui' // 导入 dat.gui
const gui = new dat.GUI();

// 控制变量
let mycube = gui.addFolder('控制物体') // 文件夹归类
mycube.add(cube, 'visible').name('显示物体') // 布尔值
mycube.add(cube.material, 'wireframe').name('边框线') // 布尔值
mycube.add(cube.position, 'x', 0, 4, 0.01).name('移动x轴') // 数值
mycube.add(cube.position, 'y', 0, 4, 0.01)
  .name('移动y轴')
  .onChange((val) => {
    console.log('cube.position.y值在变化', val);
  })
  .onFinishChange((val) => {
    console.log('cube.position.y不变了', val);
  })

// 控制颜色 需要调用set方法，所以要提前准备好参数列表
const params = {
  color: '#ffffff',
  fun: () => {
    console.log('111');
  }
}
gui.addColor(params, 'color').onChange((val) => {
  cube.material.color.set(val)
})
gui.add(params, 'fun') // 按钮
```



## 几何体和材质

此部分详细参见官网： [几何体](https://threejs.org/docs/index.html#api/zh/geometries/BoxGeometry)  [材质](https://threejs.org/docs/index.html#api/zh/materials/MeshBasicMaterial)



## 纹理/贴图

纹理即为贴合在几何体上的展示画面，也叫贴图，可以用图片等作为纹理展示：**图片素材放在 piblic 文件夹下**

```js
// 创建纹理加载器加载纹理图片
const texture = new THREE.TextureLoader().load( 'textures/A.jpg' );

// 立即使用纹理进行材质创建
const material = new THREE.MeshBasicMaterial( { map: texture } );
```



### 纹理加载事件

可以通过纹理加载事件调控相关进度和完成情况：

load事件可以传递其他参数： [load](https://threejs.org/docs/index.html#api/zh/loaders/TextureLoader.load) ( url : String, onLoad : Function, onProgress : Function, onError : Function )

```js
const texture = new THREE.TextureLoader().load(
  'textures/A.jpg',
  (texture) => { console.log('纹理加载完成', texture) },
  (xhr) => { console.log('纹理加载中...',xhr) },
  (err) => { console.log('纹理加载失败',err) }
)
```

以上是实现单个纹理的加载事件，如果我们希望一次侦听多个，等所有纹理都加载完才展示场景，则可以借助 **加载管理器 LoadingManager** 来实现。

```js
// 创建一个加载管理器，传入加载事件
// onProgress 有三个参数 url itemsLoaded itemsTotal
const manager = new THREE.LoadingManager(onLoad : Function, onProgress : Function, onError : Function)
// 将加载管理器传给纹理加载器，纹理加载器的每次加载都挂上了相应事件
const texture = new THREE.TextureLoader(manager) 
```







### 纹理偏移，旋转与重复

```js
// 纹理偏移
// texture.offset.x = 0.5
texture.offset.set(0.5, 0.5) // 二维参数

// 纹理旋转 默认（0，0）左下角
texture.cnter = (0.5, 0.5) // 设置中心点为旋转原点
texture.rotation = Math.PI / 4  // 旋转45°

// 纹理重复
texture.repeat.set(2, 3) //水平和垂直重复次数
// 默认重复是纹理边缘将被推到外部边缘的纹素 THREE.ClampToEdgeWrapping
// THREE.RepeatWrapping 重复包裹 | THREE.MirroredRepeatWrapping 镜像重复包裹
texture.wrapS = THREE.RepeatWrapping // 水平重复模式
texture.wrapT = THREE.RepeatWrapping // 垂直重复模式
```



### 透明材质与透明纹理

实现材质的部分透明可以使用灰度纹理：

alpha贴图是一张灰度纹理，用于控制整个表面的不透明度。（黑色：完全透明；白色：完全不透明）。

```js
// 创建纹理加载器加载纹理和灰度纹理
const myTexture = new THREE.TextureLoader().load('./tie.png')
const alphaTexture = new THREE.TextureLoader().load('./alpha.png')

// 创建物体并添加到场景中
const geometry = new THREE.BoxGeometry(1, 1, 1); // 创建立方体(透明),参数:长宽高
const material = new THREE.MeshBasicMaterial({
  map: myTexture, // 给材质添加颜色贴图（纹理）
  alphaMap: alphaTexture, // 再覆盖一层alpha贴图，即灰度纹理
  transparent: true, // 开启材质可透明化，就可以让alpha贴图黑色部分完全透明
  // opacity: 0.5 控制整个材质的透明度，需要开启材质可透明化
}); // 创建材质
const cube = new THREE.Mesh(geometry, material); // 创建网格对象,是立方体和材质的组合
scene.add(cube); // 添加到场景中
```



## 标准网格材质与光源

之前我们使用的都是 *基础网格材质 MeshBasicMaterial*，这种材质都是直接展现的。如果我们想模拟光源下物体的反射折射等场景，则需要使用 **标准网格材质 MeshStandardMaterial** 来展示。

```js
const standardMaterial = new THREE.MeshStandardMaterial({ // 创建标准材质
  map: myTexture,
  transparent: true,
  opacity: 1.0
})
```

但此时发现我的的物体变黑了，这是因为 标准网格材质 需要有光源才能展现并且模拟光照效果：

```js
// 添加光源，使物体显示出来
const ambientLight = new THREE.AmbientLight( 0xffffff, 0.4 ); // 环境光，参数为光源颜色和强度
scene.add( ambientLight );
const directionalLight = new THREE.DirectionalLight( 0xffffff, 1.0 ); // 平行光
directionalLight.position.set(10, 10, 10) // 设置光源位置
scene.add( directionalLight );
```



## 位移，粗糙度，金属和法线贴图

贴图素材下载网址：

https://www.poliigon.com/textures?credit=0

https://www.textures.com/free

https://3dtextures.me/ 

https://www.unrealengine.com/zh-CN/bridge （下载软件，用epic账号登录）等

这些不同的贴图都是灰度贴图，借助不同的贴图颜色实现物体局部的状态改变。

如果我们想实现创建的物体表面有凸起等效果，则需要借助 **位移贴图 [displacementMap](https://threejs.org/docs/index.html#api/zh/materials/MeshStandardMaterial.displacementMap)** 来实现。

位移贴图会影响网格顶点的位置，移位的顶点可以投射阴影，阻挡其他对象，以及充当真实的几何体。位移效果是根据位移贴图的颜色来实现的，黑色是无位移，白色是最大位移。

**粗糙度贴图** [roughnessMap](https://threejs.org/docs/index.html#api/zh/materials/MeshStandardMaterial.roughnessMap) 搭配 `roughness`属性

**金属贴图** [metalnessMap](https://threejs.org/docs/index.html#api/zh/materials/MeshStandardMaterial.metalnessMap) 搭配 `metalness`属性

**法线贴图** [normalMap](https://threejs.org/docs/index.html#api/zh/materials/MeshStandardMaterial.normalMap) 用于创建法线贴图的纹理。RGB值会影响每个像素片段的曲面法线，并更改颜色照亮的方式。

```js
const displaceTexture = new THREE.TextureLoader().load('./B.png')

// 创建几何体，并增加可选参数 分段数；分段数影响顶点个数，而位移贴图就是要依据顶点实现
const geometry = new THREE.BoxGeometry(1, 1, 1,200,200,200);
const standardMaterial = new THREE.MeshStandardMaterial({
  map: myTexture,
  displacementMap: displaceTexture, // 贴上位移贴图，实现凸起效果
  displacementScale: 0.1, // 位移贴图对网格的影响程度 默认值为1
  // roughness: 0 // 直接设置粗糙度，0 光滑，1 粗糙
}) 
```



## 环境贴图

前面的都是在物体上贴上纹理，然后相机在远处观察。如果我们想做出 相机在一个场景内部，是从内部进行观察，则需要使用环境贴图来实现：其原理就是用一个巨大的盒子包裹住相机，然后再盒子的六个面上贴上贴图。

我们可以先实现一个相机在远处观察，一个金属球反射周围场景的效果：

``` js
// 创建立方体纹理加载器加载立方体每个面的贴图
const cubeTexture = new THREE.CubeTextureLoader()
	.load([
    'px.png', // p 为正方向， n 为负方向
		'nx.png',
		'py.png',
		'ny.png',
		'pz.png',
		'nz.png'
  ])
// 创建球几何体
const sphereGeometry = new THREE.SphereGeometry( 2, 32, 16 )
// 创建标准材质，贴上纹理
const standardMaterial = new THREE.MeshStandardMaterial({
  envMap: cubeTexture, // 贴上环境贴图
  metalness: 0.7, // 金属度
  roughness: 0.1 // 光滑度
})
// 创建网格对象,是球体和材质的组合
const cube = new THREE.Mesh(sphereGeometry, standardMaterial);
scene.add(cube); // 添加到场景中，注意标准材质需要光源
```

接下来我们实现让整个场景都变化：主要用到的就是最开始创建的场景对象 **[Scene](https://threejs.org/docs/index.html#api/zh/scenes/Scene)**

只需 `Scene.background = cubeTexture` 即将立方体纹理赋给场景对象的背景属性。我们也可以设置 `Scene.environment = cubeTexture` 这样该纹理贴图将会被设为场景中所有物理材质的环境贴图。

如果我们获取到的不是六张图片，而是一个类似全景图的hdr格式的单图，则需要更改成 **RGBELoader** 加载。

```js
import { RGBELoader } from 'three/examples/jsm/loaders/RGBELoader'

const rgbeLoader = new RGBELoader()
rgbeLoader.loadAsync('xxx/1.hdr').then((texture) => {
  texture.mapping = THREE.EquirectangularReflectionMapping // 注意要更改纹理映射方式
  Scene.background = texture
})
```



## 阴影

如果我们希望开启阴影展示，**注意材质需要对光照有反应**，且必须要有如下步骤

```js
// 1.允许渲染器渲染阴影
// 2.开启光源投射阴影
// 3. 材质物体需要开启阴影投射
// 4. 需要有物体接收展示阴影

renderer.shadowMap.enabled = true // 开启允许在场景中使用阴影贴图
directionalLight.castShadow = true // 开启平行光的阴影投射
sphere.castShadow = true // 开启球的阴影
plane.receiveShadow = true // 开启平面接收阴影
```

我们可以设置光源的shadow对象来调控光打下去的阴影效果：查看阴影的基类 [LightShadow](https://threejs.org/docs/index.html#api/zh/lights/shadows/LightShadow) 了解更多，例如

```js
directionalLight.shadow.radius = 20 // 设置光照阴影的模糊度
directionalLight.shadow.mapSize.set(2048, 2048) // 设置阴影贴图宽高,即精细度
```

