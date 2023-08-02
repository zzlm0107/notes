---
title: React Router 笔记
icon: router
date: 2023-04-20
category:
  - React
  - 工具库
tag:
  - 路由
---

**什么是路由?**

1. 一个路由就是一个映射关系(key: value)
2. key为路径, value可能是function或component

**后端路由**

1. 理解： **value是function**, 用来处理客户端提交的请求。
2. 注册路由： `router.get(path, function(req, res))`
3. 工作过程：当`node`接收到一个请求时, 根据请求路径找到匹配的路由, 调用路由中的函数来处理请求, 返回响应数据

**前端路由**

1. 浏览器端路由，**value是component**，用于展示页面内容。
2. 注册路由: `<Route path="/test" component={<Test/>}>`
3. 工作过程：当浏览器的path变为`/test`时, 当前路由组件就会变为Test组件

> 前端路由主要是通过操作BOM中的history来操作路径



**react路由安装**

```bash
# 现在已经默认安装6版本
npm i react-router-dom  
```

> 由于 react-router-dom 6版本的更新属于破坏式更新，其中对5版本的不少组件都进行了废弃，所以这里重点介绍6版本的相关api。5版本的基础知识可以学习一下，例如各种参数的传递方式，这是不变的。



## 5版本的使用

### 基础使用

1. 明确好界面中的导航区、展示区
2. 导航区的a标签改为Link标签 `<Link to="/xxxxx">Demo</Link>`
3. 展示区写`Route`标签进行路径的匹配 `<Route path='/xxxx' component={Demo}/>`
4. `<App>`的最外侧包裹了一个`<BrowserRouter>`或`<HashRouter>`
5. 嵌套路由需要带上父路由的路径，即写成一个完整路径，嵌套路由父路由不可开启严格匹配（**exact**）

```jsx
import React, { Component } from 'react'
import { Link,Route } from 'react-router-dom'
import Home from './Home'
import About from './About'

export default class App extends Component {
  render() {
    return (
      <div>
        {/* 路由标签 可以将Link标签替换为NavLink，其可以实现路由链接的高亮，通过activeClassName属性指定样式名，默认是"active" */}
        <NavLink activeClassName="demo" className="list-group-item" to="/home">Home</NavLink>
        <Link className="list-group-item" to="/home">Home</Link>
        
        {/* 注册路由,路径匹配上就展示相关组件，否则就不展示 */}
        <Route path='/about' component={About} />
        <Route path='/home' component={Home} />
    </div>
    )
  }
}
```

### 匹配与重定向

通常情况下，在`<Route path='/home' component={Home} />`中，path和component是对应的关系。不过也可以多写几个这样的标签，每个都是相同path但匹配不同的component,这样所以的component都会展示。这是因为在一组连续Route标签中当其中一个标签的path匹配到当前路径时不会停止，它会继续向下匹配，这样就会导致效率问题。并且这是不合常理的，为什么不让多个组件组合成一个呢

`Switch标签`可以提高路由匹配效率(匹配到一个就停止)。另外 `Redirect标签`可以重定向路由。

```jsx
<Switch>
  <Route path="/about" component={About}/>
  <Route path="/home" component={Home}/> 
  <Route path="/home" component={Test}/>
  {/* 当前面的路由都没匹配到时，就重定向到指定路径 */}
  <Redirect to="/about"/>
</Switch>
```

### 路由传参与获取

**路由传参之params参数：**

路由链接(携带参数)：`<Link to='/demo/test/tom/18'}>详情</Link>`

注册路由(声明接收)：`<Route path="/demo/test/:name/:age" component={Test}/>`

接收参数：`this.props.match.params`



**路由传参之search参数：**

路由链接(携带参数)：`<Link to='/demo/test?name=tom&age=18'}>详情</Link>`

注册路由(无需声明，正常注册即可)：`<Route path="/demo/test" component={Test}/>`

接收参数：`this.props.location.search`

备注：获取到的search是urlencoded编码字符串，需要借助`querystring`解析

```js
import qs from 'querystring'
let obj = {name:'tom', age:18}
console.log(qs.stringify(obj)) // name=tom&age=18
let str = 'carName=Benz&price=199'
console.log(qs.parse(str)) // {carName: 'Benz', price: 199}
```



**路由传参之state参数：**

路由链接(携带参数)：`<Link to={{ pathname:'/demo/test', state:{name:'tom',age:18} }}>详情</Link>`

注册路由(无需声明，正常注册即可)：`<Route path="/demo/test" component={Test}/>`

接收参数：`this.props.location.state`

备注：刷新也可以保留住参数【history对象记录着】



**编程式路由导航**

上述的导航传参方式是在路由标签里面书写传递参数。但我们也可以调用相关方法实现路由跳转传参，此即编程式路由导航。

```jsx
// push跳转，携带parmas参数
this.props.history.push(`/home/message/${id}`)

// replace跳转，携带search参数
this.props.history.replace(`/home/message?id=123`)

// push跳转，携带state参数
this.props.history.push(`/home/message`, {id: 123})

this.props.history.goBack()  // 后退
this.props.history.goForward()   // 前进
this.props.history.go()  // 前进或后退指定步数
```



**总结下来，路由组件上存在这些重要属性：**

```yaml
history:
    go: ƒ go(n)
    goBack: ƒ goBack()
    goForward: ƒ goForward()
    push: ƒ push(path, state)
    replace: ƒ replace(path, state)
location:
    pathname: "/about"
    search: ""
    state: undefined
match:
    url: "/about"
    params: {}
    path: "/about"
```



### 路由刷新样式丢失

根本原因在history路由模式下，嵌套路由更改了页面路径，刷新导致再次**请求index.html里通过相对路径引入的样式资源请求时路径错误**，从而丢失样式。

Pulbic文件夹就是根目录`/`

1. public/index.html 中 引入样式时不写 `./` 写 `/` （常用）【绝对路径】
2. public/index.html 中 引入样式时不写 `./` 写 `%PUBLIC_URL%` （react常用）
3. 使用`HashRouter`



### BrowserRouter与HashRouter的区别

1. 底层原理不一样： `BrowserRouter`使用的是H5的history API，不兼容IE9及以下版本。 `HashRouter`使用的是URL的哈希值。

2. path表现形式不一样 `BrowserRouter`的路径中没有#,例如：`localhost:3000/demo/test` `HashRouter`的路径包含#,例如：`localhost:3000/#/demo/test`

3. 刷新后对路由`state`参数的影响 (1) `BrowserRouter`没有任何影响，因为`state`保存在history对象中。 (2) `HashRouter`刷新后会导致路由`state`参数的丢失！！！

备注：`HashRouter`可以用于解决一些路径错误相关的问题。



## 6版本的使用

6版本官方明确指明**推荐函数式组件**，同时部分原来的api进行了废弃修改。



### 更新部分

1. 原先路由组件标签进行了调整： `<Route path='/xxxx' element={<Demo/>}/>`，可以添加 caseSensitive 属性开启不区分大小写匹配。
2. 删除了 `Switch标签`，改成用 `Routes标签` 替换，并且**必须使用 Routes 标签包裹路由标签**。
3. 对 `<Redirect to='/home' />` 进行删除, 改为 `<Navigate to='/home' />`标签，并且需要在 Route标签 的管控下。
   ```jsx
   <Route path='/' element={<Navigate to='/home' />}/>
   ```



4. NavLink标签的高亮,默认是加上 active 类名 
   ```jsx
   <NavLink className={ ({isActive}) => isActive ? 'class1 activeClass' : 'class1'} to='/home'></NavLink>
   ```



### 路由表

相比于原来的使用组件的方式表现整个路由，现在更加推荐使用路由表的方式

```tsx
// /router/routes.tsx  路由表文件
import {Suspense,lazy} from 'react'
import { Navigate } from 'react-router-dom'

// 路由懒加载,外面需要包裹一层Suspense缓冲组件
const Home = lazy(()=> import('./home'))
const About = lazy(()=> import('./about'))

// 封装懒加载loading组件
const withLoadingComponent = (comp: JSX.Element) => (
	<Suspense fallback={<h2>Loading...</h2>}>
    {comp}
  </Suspense>
)

export default = [
  {
    path: '/home',
    element: withLoadingComponent(<Home />),
    children: [
    	{
    		path: 'about',
    		element: withLoadingComponent(<About />),
      }
    ]
  },
  {
    path: '/',
    element: <Navigate to="/home" />
  }
]
```

```jsx
import { useRoutes } from 'react-router-dom'
import routes from './routes'

export default function App () {
  {/* 注册路由表文件 */}
  const Outlet = useRoutes(routes)
  return (
    {/* 路由出口 */}
    { Outlet }
  )
}
```

由于路由表的存在，在嵌套路由中我们并不知道子路由具体放在哪里，所以需要一个**路由出口组件**`<Outlet />`,此外 NavLink标签 还有一个 end属性， 表示如果子级路由匹配则结束自己的高亮效果。



以上路由表写法等价于下面的组件形式写法：

```tsx
// /router/index.tsx  路由组件文件
import App from './app'
import Home from './home'
import About from './about'

import {BrowerRouter,Routes,Route,Navigate} from 'react-router-dom'

const baseRouter = () => (
	<BrowerRouter>
    <Routes>
      <Route path='/' element={<App />}>
        <Route path='/' element={<Navigate to='/home' />}></Route>
        <Route path='/home' element={<Home />}></Route>
        <Route path='/about' element={<About />}></Route>
      </Route>
    </Routes>
  </BrowerRouter>
)

export default baseRouter
```

```tsx
// app.tsx
import Router from './router/index.tsx'

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <Router />
  </React.StrictMode>,
)
```



### 参数获取

**params参数**的**传递接收**依然需要在路由路径上声明，不过由于官方推荐使用函数式组件，所以现在想到拿到函数式组件里的params参数要借助一个钩子 `useParams`

```jsx
import { useParams } from 'react-router-dom'

export default function Detail() {
  let params = useParams()
  console.log(params)
}
```

**search参数**的**传递**依然需要在路由路径上声明，拿到参数需要使用 `useSearchParams`

```jsx
import { useSearchParams } from 'react-router-dom'

export default function Detail() {
  const [search, setSearch] = useSearchParams() // 解构出search对象和setSearch方法
  let id = search.get('id')
}
```

**state参数**的获取需要使用 `useLocation`

```jsx
import { useLocation } from 'react-router-dom'

export default function Detail() {
  const {state} = useLocation() // 解构出state对象
  let id = search.get('id')
}
```



### 编程式路由导航

6版本的编程式路由导航不再依赖路由组件 props 里的相关对象，而是使用官方提供的 `useNavigate` 钩子

```jsx
import { useNavigate } from 'react-router-dom'

export default function Detail() {
  const navigate = useNavigate() // 获取到navigate方法
  function goHome () {
    navigate('/home',{ // 传递参数，传递数值就是前进后退相关
      replace: false,
      state: {id:123}
    })
  }
}
```



### 其他相关hooks

`useInRouterContext()` 判断当前组件是否被路由所管理

`useNavigateType()` 返回当前路径的导航跳转来源类型 `PCP` | 刷新页面 `PUSH`| `REPLACE`

`useOutlet()` 返回当前路由中的路由出口（即嵌套路由）

`useResolvePath()` 给定一个URL值，解析其中的：path、search、hash值。

