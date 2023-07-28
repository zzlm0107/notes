---
title: Vue2 笔记
icon: vue
date: 2022-12-20
category:
  - Vue
  - 前端框架
tag:
  - vue
---

**vue2官网** <https://v2.cn.vuejs.org/>

**菜鸟教程** <https://www.runoob.com/vue2/vue-tutorial.html>


## vue框架简介

1. 遵循 MVVM 模式
2. 编码简洁，体积小，运行效率高，适合 移动/PC 端开发
3. 它本身只关注 UI，可以轻松引入 vue 插件或其它第三方库开发项目
4. 采用**组件化**模式，提高代码复用率、且让代码更好维护
5. **声明式**编码，让编码人员无需直接操作DOM，提高开发效率
6. 使用**虚拟DOM**和**Diff算法**，尽量复用DOM节点



## 基础使用

1. 想让Vue工作，就必须创建一个Vue实例，且要传入一个配置对象；
2. root容器里的代码依然符合html规范，只不过混入了一些特殊的Vue语法；
3. root容器里的代码被称为【Vue模板】；
4. Vue实例和容器是一一对应的；
5. 真实开发中只有一个Vue实例，并且会配合着组件一起使用；
6. {{xxx}}中的xxx要写js表达式，且xxx可以自动读取到data中的所有属性；
7. 一旦data中的数据发生改变，那么页面中用到该数据的地方也会自动更新；

```js
//创建Vue实例
new Vue({
	el:'#root', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串。
	data:{ //data中用于存储数据，数据供el所指定的容器去使用，值我们暂时先写成一个对象。
		name:'andy',
		age: 18
	}
})
```



**el有两种写法：** new Vue实例时就指定，或者实例创建完通过 `$mount('#root')` 指定。

**data有两种写法：** 对象式 和 函数式（组件必须）



## MVVM 模型

MVVM 本质上是 MVC （Model-View- Controller）的改进版。即模型-视图-视图模型。

`模型`model指的是后端传递的数据，`视图`view指的是所看到的页面。

`视图模型`viewModel是 mvvm 模式的核心，它是连接 view 和 model 的桥梁。它有两个方向：

将`模型`转化成`视图`，即将后端传递的数据转化成所看到的页面。实现的方式是：**数据绑定**

将`视图`转化成`模型`，即将所看到的页面转化成后端的数据。实现的方式是：**DOM 事件监听**

这两个方向都实现的，我们称之为**数据的双向绑定**



## 模板语法

### 插值语法

- 功能：用于解析标签体内容，向页面输出数据
- 写法：`{{xxx}}`，xxx是js表达式，且可以直接读取到data中的所有属性，可以调用对象的方法
- 备注：里面写**js表达式**：有返回值的js代码，而不是**js语句**

### 指令语法

- 功能：用于解析标签（包括：标签属性、标签体内容、绑定事件.....）
- 举例：`v-bind:href="xxx"` 或 简写为 `:href="xxx"`，xxx同样要写js表达式，且可以直接读取到data中的所有属性
- 备注：Vue中有很多的指令，且形式都是：`v-xxx`

**常用指令语法如下：**

`v-bind`: 单项数据绑定，数据只能从 data 流向页面。可以简写为 `:`

`v-model`: 双项数据绑定，适用于表单标签。数据不仅能从 data 流向页面，还能从页面流向 data

`v-on`: 绑定指定事件名的回调函数，可以简写为 `@click='xxx'`

`v-text`: 向其所在的节点中渲染文本内容, 注意`v-text`会替换掉节点中的内容，`{{xx}}`则不会。

`v-html`: 向指定节点中渲染包含html结构的内容。会替换掉节点中所有的内容，`{{xx}}`则不会。

`v-if v-else-if v-else`: 判断节点是否被渲染，需要连续使用

`v-show`: 判断节点的显示与隐藏

`v-for` : 遍历数组/对象

`v-cloak` : 使用它防止闪现表达式, 与css配合: [v-cloak] { display: none }



## 计算属性与侦听属性

**计算属性 computed**

> 在`computed`属性对象中定义计算属性的方法，在页面中使用`{{方法名}}`来显示计算的结果

1. 定义：要用的属性不存在，要通过已有属性**计算**得来。
2. 原理：底层借助了`Objcet.defineproperty`方法提供的`getter`和`setter`。
3. get函数什么时候执行？
   1. 初次读取时会执行一次。
   2. 当依赖的数据发生改变时会被再次调用。
4. 优势：与methods实现相比，内部有**缓存**机制（复用），效率更高，调试方便。

```js
//完整写法
fullName:{
    get(){
        console.log('get被调用了')
        return this.firstName + '-' + this.lastName
    },
    set(value){
        console.log('set',value)
        const arr = value.split('-')
        this.firstName = arr[0]
        this.lastName = arr[1]
    }
} 
```



```js
//简写
fullName(){
    console.log('get被调用了')
    return this.firstName + '-' + this.lastName
}
```



**监视属性 watch**

> 通过vm对象的`$watch()`或`watch配置`来监视指定的属性，当属性变化时, 回调函数**自动调用**, 在函数内部进行计算

1. 当被监视的属性变化时, 回调函数自动调用, 进行相关操作
2. 监视的属性必须存在，才能进行监视！！

```js
watch:{
    // 完整写法
    isHot:{
        immediate:true, //初始化时让handler调用一下
        deep： true， // 开启深度监视
        //handler什么时候调用？当isHot发生改变时。
        handler(newValue,oldValue){
            console.log('isHot被修改了',newValue,oldValue)
        }
    }，
    //简写
    isHot(newValue,oldValue){
        console.log('isHot被修改了',newValue,oldValue,this)
    } 
}
```



**二者区别**

1. 计算属性变量在computed中定义，监视属性监听的是已经在 data 中定义的变量, 当该变量变化时，会触发 watch 中的方法.
2. computed 具有缓存功能，可以监听对象某个具体属性。
3. watch可以进行深度监听，监听对象的变化。
4. 计算属性是声明式的描述一个值依赖了其他值，依赖的值改变后重新计算结果更新DOM。监视属性的是定义的变量，当定义的值发生变化时，执行相对应的函数。
5. computed能完成的功能，watch都可以完成。watch能完成的功能，computed不一定能完成，例如：watch可以进行异步操作。



**补充：**

1. 所被Vue管理的函数，最好写成普通函数，这样this的指向才是vm 或 组件实例对象。
2. 所有不被Vue所管理的函数（定时器的回调函数、ajax的回调函数等、Promise的回调函数），最好写成箭头函数，这样this的指向才是vm 或 组件实例对象。



## 样式绑定

### class类名绑定

```js
:class='xxx' // xxx可以是字符串、对象、数组。
```

1. 当是字符串时：适用于**类名不确定，要动态指定**

2. 当是数组时：适用于**要绑定多个样式，个数不确定，名字也不确定**

3. 当是对象时： 适用于**要绑定多个样式，个数确定，名字也确定，但不确定用不用**，对象里类名对应为布尔值



### style样式绑定

```js
:style="{ color: activeColor, fontSize: fontSize + 'px' }"
```



## 列表渲染

`v-for` 指令

1. 用于展示列表数据
2. 语法：`v-for="(item, index) in xxx" :key="yyy"`
3. 可遍历：数组、对象、字符串（用的很少）、指定次数（用的很少）

- 数组: `(item, index)`
- 对象: `(value, key)`
- 字符串：`(char, index)`
- 数字：`(number, index)`



**需要指定key的原因**

1. 虚拟DOM中`key`的作用：

> key是虚拟DOM对象的标识，当数据发生变化时，Vue会根据【新数据】生成【新的虚拟DOM】, 随后Vue进行【新虚拟DOM】与【旧虚拟DOM】的差异比较，比较规则如下：

1. 对比规则：
   1. 旧虚拟DOM中找到了与新虚拟DOM相同的`key`：
      - 若虚拟DOM中内容没变, 直接使用之前的真实DOM
      - 若虚拟DOM中内容变了, 则生成新的真实DOM，随后替换掉页面中之前的真实DOM
   2. 旧虚拟DOM中未找到与新虚拟DOM相同的`key`创建新的真实DOM，随后渲染到到页面。
2. 用`index`作为`key`可能会引发的问题：
   1. 若对数据进行：逆序添加、逆序删除等破坏顺序操作: 会产生没有必要的真实DOM更新 `==>` 界面效果没问题, 但效率低
   2. 如果结构中还包含输入类的DOM： 会产生错误DOM更新 `==> `界面有问题
3. 开发中如何选择`key`:
   1. 最好使用每条数据的唯一标识作为`key`, 比如id、手机号、身份证号、学号等唯一值。
   2. 如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，仅用于渲染列表用于展示，使用`index`作为`key`是没有问题的。



## 监视数据原理

1. vue会监视`data`中所有层次的数据。
2. 如何监测对象中的数据？

> 通过setter实现监视，且要在new Vue时就传入要监测的数据。

① 对象中后追加的属性，Vue默认不做响应式处理

② 如需给后添加的属性做响应式，请使用如下API：`Vue.set(target，propertyName/index，value)` 或 `vm.$set(target，propertyName/index，value)`

1. 如何监测数组中的数据？ 通过包裹数组更新元素的方法实现，本质就是做了两件事：
   1. 调用原生对应的方法对数组进行更新。
   2. 重新解析模板，进而更新页面。
2. 在Vue修改数组中的某个元素一定要用如下方法：
   1. 使用这些API: `push()`、`pop()`、`shift()`、`unshift()`、`splice()`、`sort()`、`reverse()`
   2. `Vue.set()` 或 `vm.$set()`

特别注意：`Vue.set()` 和 `vm.$set()` 不能给 `vm` 或 `vm` 的根数据对象 添加属性！！！



## 事件处理

**事件的基本使用：**

1. 使用 `v-on:xxx` 或 `@xxx` 绑定事件，其中`xxx`是事件名；
2. 事件的回调需要配置在`methods`对象中，最终会在`vm`上；
3. `methods`中配置的函数，不要用箭头函数！否则`this`就不是`vm`了；
4. `methods`中配置的函数，都是被`Vue`所管理的函数，`this`的指向是`vm` 或 组件实例对象；
5. `@click="demo" `和 `@click="demo($event)" `效果一致，但后者可以**传参**；`$event` 就是当前触发事件的元素，当有其他参数时即使不传 `$event`，在回调函数中也可以使用 `event` 这个参数。

**事件修饰符**

1. `prevent`：阻止默认事件（常用）；`event.preventDefault()`
2. `stop`：阻止事件冒泡（常用）；`event.stopPropagation()`
3. `once`：事件只触发一次（常用）；
4. `capture`：使用事件的捕获模式；
5. `self`：只有`event.target`是当前操作的元素时才触发事件；
6. `passive`：事件的默认行为立即执行，无需等待事件回调执行完毕；

**按键修饰符**

```txt
 回车 => enter
 删除 => delete (捕获“删除”和“退格”键)
 退出 => esc
 空格 => space
 换行 => tab (特殊，必须配合keydown去使用)
 上 => up
 下 => down
 左 => left
 右 => right
```

1. `Vue`未提供别名的按键，可以使用按键原始的`key`值去绑定，但注意要转为`kebab-case`（短横线命名）
2. 系统修饰键（用法特殊）：`ctrl`、`alt`、`shift`、`meta`

- (1). 配合`keyup`使用：按下修饰键的同时，再按下其他键，随后释放其他键，事件才被触发。
- (2). 配合`keydown`使用：正常触发事件。

1. 也可以使用`keyCode`去指定具体的按键（不推荐）



## 生命周期

<img src="https://v2.cn.vuejs.org/images/lifecycle.png">

1. 初始化显示

- `beforeCreate()`   创建实例之前，此时仅完成相关初始化操作
- `created()`   实例创建完毕，可以访问到 data数据和 methods方法
- `beforeMount()`  挂载之前，页面展现的是未经vue编译的DOM结构
- `mounted()`   挂载完毕， 页面展现出编译后的DOM结构，在此可以完成发请求，定时器等操作

1. 更新状态

- `beforeUpdate()`   数据更新前，数据是新的，但DOM是旧的，需要经过Diff算法对比
- `updated()`   更新完成

1. 销毁 vue 实例: `vm.$destory()`

- `beforeDestory()`   卸载前，数据方法都还可以访问到，在此可以完成清除定时器等操作
- `destoryed() `   卸载完成

  

**父子组件生命周期流程**

1. 加载渲染过程

*父beforeCreate -> 父created -> 父beforeMount -> 子beforeCreate->子created->子beforeMount->子mounted->父mounted*

2. 更新过程

*父beforeUpdate->子beforeUpdate->子updated->父updated*

3. 销毁过程

*父beforeDestroy->子beforeDestroy->子destroyed->父destroyed*



## 过渡动画

作用：在插入、更新或移除 DOM元素时，在合适的时候给元素添加样式类名。

写法：

1. 准备好样式：
   - 元素进入的样式：
     1. v-enter：进入的起点
     2. v-enter-active：进入过程中
     3. v-enter-to：进入的终点
   - 元素离开的样式：
     1. v-leave：离开的起点
     2. v-leave-active：离开过程中
     3. v-leave-to：离开的终点
2. 使用`<transition>`包裹要过度的元素，并配置name属性：

```
<transition name="hello">
    <h1 v-show="isShow">你好啊！</h1>
</transition>
```

1. 备注：若有多个元素需要过渡，则需要使用：`<transition-group>`，且每个元素都要指定`key`值。



## 自定义指令

```js
// 全局指令
Vue.directive(指令名,配置对象) 

// 局部指令
new Vue({
    directives:{指令名:配置对象}	
}) 
```



指令配置对象的三个回调

- `bind`：指令与元素成功绑定时调用。
- `inserted`：指令所在元素被插入页面时调用。
- `update`：指令所在模板结构被重新解析时调用。



```js
// 局部指令
directives : {
    'my-directive' : {
        bind (el, binding) {
            // el是绑定的元素，binging是传递的值
            el.innerHTML = binding.value.toupperCase()
        }
    }
}
```

1. 指令定义时不加`v-`，但使用时要加`v-`；
2. 指令名如果是多个单词，要使用`kebab-case`命名方式，不要用`camelCase`命名。



## 组件

vue组件分为**非单文件组件（基本不用）**和**单文件组件**两种，重点介绍单文件组件。

单文件组件包括 **template模板，script脚本，style样式 三大标签**



## 脚手架

vue-cli是Vue官方提供的脚手架工具 [中文网址](https://cli.vuejs.org/zh/)

全局安装：`npm install -g vue-cli`

脚手架项目文件结构：

```
├── node_modules 
├── public
│   ├── favicon.ico: 页签图标
│   └── index.html: 主页面
├── src
│   ├── assets: 存放静态资源
│   │   └── logo.png
│   │── component: 存放组件
│   │   └── HelloWorld.vue
│   │── App.vue: 汇总所有组件
│   │── main.js: 入口文件
├── .gitignore: git版本管制忽略的配置
├── babel.config.js: babel的配置文件
├── package.json: 应用包配置文件 
├── README.md: 应用描述文件
├── package-lock.json：包版本控制文件
```



## 组件相关

### ref属性

1. 被用来给元素或子组件注册引用信息（id的替代者）
2. 应用在html标签上获取的是真实DOM元素，应用在组件标签上是组件实例对象（vc）
3. 使用方式：
   1. 打标识：`<h1 ref="xxx">.....</h1>`或 `<School ref="xxx"></School>`
   2. 获取：`this.$refs.xxx`



### props

1. 功能：让组件接收外部传过来的数据
2. 传递数据：`<Demo name="xxx"/>`
3. 接收数据：
   1. 第一种方式（只接收）：`props:['name'] `
   2. 第二种方式（限制类型）：`props:{name:String}`
   3. 第三种方式（限制类型、限制必要性、指定默认值）：

```js
props:{
    name:{
        type: String, //类型
        required: true, //必要性
        default: '老王' //默认值
    }
}
```

> 备注：props是只读的，Vue底层会监测你对props的修改，如果进行了修改，就会发出警告，若业务需求确实需要修改，那么请复制props的内容到data中一份，然后去修改data中的数据。



### mixin

1. 功能：可以把多个组件共用的数据方法提取成一个混入对象，*重名优先以组件内的为准*

2. 使用方式：

   第一步定义混合：

```
{
    data(){....},
    methods:{....}
    ....
}
```

第二步使用混入：

- 全局混入：`Vue.mixin(xxx)`
- 局部混入：`mixins:['xxx']	`



### 插件

1. 功能：用于增强Vue
2. 本质：包含install方法的一个对象，install的第一个参数是Vue，第二个以后的参数是插件使用者传递的数据。
3. 定义插件：

```js
对象.install = function (Vue, options) {
    // 1. 添加全局过滤器
    Vue.filter(....)

    // 2. 添加全局指令
    Vue.directive(....)

    // 3. 配置全局混入(合)
    Vue.mixin(....)

    // 4. 添加实例方法
    Vue.prototype.$myMethod = function () {...}
    Vue.prototype.$myProperty = xxxx
}
```

4. 使用插件：`Vue.use(xxx)`



### 插槽

1. 作用：让父组件可以向子组件指定位置插入html结构，也是一种组件间通信的方式，适用于 **父组件 ===> 子组件**

2. 分类：默认插槽、具名插槽、作用域插槽

   

**默认插槽**

父组件中：

```vue
<Category>
   <div>html结构1</div>
</Category>
```

子组件中：

```vue
<template>
    <div>
       <!-- 定义插槽 -->
       <slot>插槽默认内容...</slot>
    </div>
</template
```



**具名插槽**

父组件中：

```vue
<Category>
    <template slot="center">
      <div>html结构1</div>
    </template>

    <template v-slot:footer>
       <div>html结构2</div>
    </template>
</Category>
```

子组件中：

```vue
<template>
    <div>
       <!-- 定义插槽 -->
       <slot name="center">插槽默认内容...</slot>
       <slot name="footer">插槽默认内容...</slot>
    </div>
</template>
```

**作用域插槽**

理解：数据在组件的自身，但根据数据生成的结构需要组件的使用者来决定。（games数据在Category组件中，但使用数据所遍历出来的结构由App组件决定）

父组件中：

```vue
<Category>
    <template scope="scopeData">
        <!-- 生成的是ul列表 -->
        <ul>
            <li v-for="g in scopeData.games" :key="g">{{g}}</li>
        </ul>
    </template>
</Category>

<Category>
    <template slot-scope="scopeData">
        <!-- 生成的是h4标题 -->
        <h4 v-for="g in scopeData.games" :key="g">{{g}}</h4>
    </template>
</Category>
```

子组件中：

```vue
<template>
    <div>
        <slot :games="games"></slot>
    </div>
</template>

<script>
    export default {
        name:'Category',
        props:['title'],
        //数据在子组件自身
        data() {
            return {
                games:['红色警戒','穿越火线','劲舞团','超级玛丽']
            }
        },
    }
</script>
```



## 组件通信

1. **props**

   父传子，子传父（父传子函数，子调用传参）

2. **自定义事件**

   子传父（父给子绑定自定义事件，子组件`emit`触发传参）

3. **全局事件总线**

   重要关系：`VueComponent.prototype.__proto__ === Vue.prototype`

   **也就是说我们找组件上的相关属性和方法如果找不到会向上继续找，并且能找到vue原型对象那去，那么我们在vue原型对象上挂载一些属性方法就可以让每个组件都能访问到。**

   ```js
   new Vue({
   	render: (h) => h(App),
   	beforeCreate() {
   		Vue.prototype.$bus = this //安装全局事件总线
   	},
   	//注册路由,vuex仓库
   	router,
   	store
   }).$mount('#app')
   
   
   // A 组件向全局事件总线绑定事件   
   this.$bus.$on('myEvent',this.func)
   // B 组件触发事件，传递参数
   this.$bus.$emit('myEvent',data)
   // A 组件注意解绑
   beforeDestroy(){
     this.$bus.$off('myEvent',this.func)
   }
   ```

4. slot

5. vuex

6. pubsub-js 消息订阅与发布
