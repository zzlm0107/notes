---
title: Vue Router 笔记
icon: router
date: 2023-01-08
category:
  - Vue
  - 工具库
tag:
  - 路由
---

**vue-router** 是vue官方提供的用来实现SPA 路由跳转的插件。

**官方文档** <http://router.vuejs.org/zh-cn/>



## 路由基本使用

1. 安装vue-router，命令：`npm i vue-router`
2. 应用插件：`Vue.use(VueRouter)`

```js
import Vue from 'vue'
import Router from 'vue-router'
Vue.use(Router)

export default new Router({
  // 多个配置项
  routes: [
    { // 一般路由
      name: home,
      path: '/home',
      component: Home，
      children： [ // 嵌套路由
      	{
      		path: 'about',
      		component: About，
    		}
      ]
    },
    { // 自动跳转路由
      path: '/',
      redirect: '/home'
    }
  ]
})
```

```js
import router from './router'
new Vue({
  // 注册路由
	router
})
```



`<router-link>`: 用来生成路由链接； `<router-view>`形成路由出口

```vue
<router-link to="/xxx">Go to XXX</router-link>

<router-view></router-view>
```



## 路由传参

**路由的query参数**

1. 传递参数

```vue
 <!-- 跳转并携带query参数，to的字符串写法 -->
 <router-link :to="/home/message/detail?id=666&title=你好">跳转</router-link>

 <!-- 跳转并携带query参数，to的对象写法 -->
 <router-link 
  :to="{
    path:'/home/message/detail',
    query:{
       id:666,
       title:'你好'
    }
  }"
 >跳转</router-link>
```

2. 接收参数：

```js
   this.$route.query.id
   this.$route.query.title
```

在路由表里提前给对应路由指明 name属性，可以形成命名路由，并简化跳转方式

```vue
<!--简化前，需要写完整的路径 -->
<router-link to="/demo/test/welcome">跳转</router-link>

<!--简化后，直接通过名字跳转 -->
<router-link :to="{name:'hello'}">跳转</router-link>

<!--简化写法配合传递参数 -->
<router-link 
  :to="{
    name:'hello',
    query:{
       id:666,
       title:'你好'
    }
  }"
>跳转</router-link>
```



**路由的params参数**

```js
// 提前在路由表声明接收params参数
{
  name:'home',
  path:'home/:id/:title', //使用占位符声明接收params参数
  component:Home
}
```

传递参数

```vue
 <!-- 跳转并携带params参数，to的字符串写法 -->
 <router-link :to="/home/message/detail/666/你好">跳转</router-link>

 <!-- 跳转并携带params参数，to的对象写法 -->
 <router-link 
  :to="{
    name:'xiangqing',
    params:{
       id:666,
       title:'你好'
    }
  }"
 >跳转</router-link>
```

> 特别注意：路由携带params参数时，若使用to的对象写法，则不能使用path配置项，必须使用name配置！

1. 接收参数：

```js
this.$route.params.id
this.$route.params.title
```



## 编程式路由导航

```js
 //$router的两个API
 this.$router.push({
  // name:'xiangqing',
  path: '/detail',
  query:{
    id:xxx,
    title:xxx
  }
 })

 this.$router.replace({
  name:'xiangqing',
  params:{
    id:xxx,
    title:xxx
  }
 })
 this.$router.forward() //前进
 this.$router.back() //后退
 this.$router.go() //可前进也可后退
```

> 特别注意：编程式路由导航跳转时，path不能与params一起使用，否则会忽略params选项；我们可以通过模板字符串拼接出完整path或通过name搭配params使用！


## 缓存路由组件

1. 作用：让不展示的路由组件保持挂载，不被销毁。
2. 具体编码：

```vue
<keep-alive include="News"> 
    <router-view></router-view>
</keep-alive>
```



## 路由组件的生命周期

1. 作用：路由组件所独有的两个钩子，用于捕获路由组件的激活状态。
2. 具体名字：
   1. `activated`路由组件被激活时触发。
   2. `deactivated`路由组件失活时触发。



## 路由守卫

1. 作用：对路由进行权限控制
2. 分类：全局守卫、独享守卫、组件内守卫

**全局守卫:**

```js
 //全局前置守卫：初始化时执行、每次路由切换前执行
 router.beforeEach((to,from,next)=>{
  if(to.meta.isAuth){ //判断当前路由是否需要进行权限控制
		//...
  }else{
    next() //放行
  }
 })

 //全局后置守卫：初始化时执行、每次路由切换后执行
 router.afterEach((to,from)=>{
  if(to.meta.title){ 
    document.title = to.meta.title //修改网页的title
  }else{
    document.title = 'vue_test'
  }
 })
```

**独享守卫:** 写在路由表里，和path等平级

```js
 beforeEnter(to,from,next){
   	if(to.meta.isAuth){ //判断当前路由是否需要进行权限控制
				// ...
   			next()
   		}else{
   			alert('暂无权限查看')
   			// next({name:'guanyu'})
   		}
   	}else{
   		next()
   	}
  }
```

**组件内守卫**

```js
 //进入守卫：通过路由规则，进入该组件时被调用
 beforeRouteEnter (to, from, next) {
 },
 //离开守卫：通过路由规则，离开该组件时被调用
 beforeRouteLeave (to, from, next) {
 }
```



## 路由模式

1. 对于一个url来说，什么是hash值？—— #及其后面的内容就是hash值。
2. hash值不会包含在 HTTP 请求中，即：hash值不会带给服务器。
3. hash模式：
   1. 地址中永远带着#号，不美观 。
   2. 若以后将地址通过第三方手机app分享，若app校验严格，则地址会被标记为不合法。
   3. 兼容性较好。
4. history模式：
   1. 地址干净，美观 。
   2. 兼容性和hash模式相比略差。
   3. 应用部署上线时需要后端人员支持，解决刷新页面服务端404的问题。

