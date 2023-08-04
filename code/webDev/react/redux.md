---
title: Redux 笔记
icon: state
date: 2023-04-25
category:
  - React
  - 工具库
tag:
  - 状态管理
---



1. redux是一个专门用于做`状态管理`的JS库(不是react插件库)。可以用在react, angular, vue等项目中, 但基本与react配合使用。
2. 作用: 集中式管理react应用中多个组件`共享`的状态。

中文文档: http://www.redux.org.cn/

```bash
npm i redux
```



**redux原理图**

<img src="https://img-blog.csdnimg.cn/20210418003347155.png"  width="600px" >



### 核心概念

**action**

1. 动作的**对象**

2. 包含 2 个属性
   - `type`：标识属性, 值为字符串, 唯一, 必要属性
   - `data`：数据属性, 值类型任意, 可选属性

3. action文件中一般包含多个暴露出去的函数，每个函数接收对应的data，返回包含 type 和 data 的对象

   ```js
   export const addNum = (num) => ({
     type: 'ADD_NUM',
     num
   })
   ```

   



**reducer**

1. 用于初始化状态、加工状态
2. 加工时，根据旧的`state`和`action`， 产生新的`state`.本质就是一个**函数**

```js

// 初始化会被调用一次，由store自动触发，此时state是undefined
function Reducer(state={value: 0}, action){
  const { type, data } = action
  switch (type) {
    case 'ADD_NUM':
      // 必须要返回一个新的state对象，redux会将其替换掉旧的state
      return { value: state.value + data }
    default:
      return state
  }
}
```



**store**

1. 将`state`、`action`、`reducer`联系在一起的**对象**
2. 如何得到此对象?

```js
import { createStore } from 'redux'
import reducer from './reducers'
export default createStore(reducer)
```

暴露的 store对象 的功能：

1. 获取状态： `getState()`: 得到 `state`
2. 修改状态： `dispatch(action)`: 分发`action`, 触发`reducer`调用, 产生新的`state`
3. 更新视图： `subscribe(listener)`: 注册监听, 当产生了新的`state`时, 自动调用，在这里调用`render()`就可以重新渲染视图（redux没办法操控react更新视图，所以只能由我们自己调用render函数）



### 基本使用

```js
import { createStore } from 'redux'

/**
 * 这是一个 reducer 函数：接受当前 state 值和描述“发生了什么”的 action 对象，它返回一个新的 state 值。
 * reducer 函数签名是 : (state, action) => newState
 *
 * Redux state 应该只包含普通的 JS 对象、数组和原语。
 * 根状态值通常是一个对象。 重要的是，不应该改变 state 对象，而是在 state 发生变化时返回一个新对象。
 * 
 */
function counterReducer(state = { value: 0 }, action) {
  switch (action.type) {
    case 'counter/incremented':
      return { value: state.value + 1 }
    case 'counter/decremented':
      return { value: state.value - 1 }
    default:
      return state
  }
}

// 接收reducer对象，创建仓库。它的API 有 { subscribe, dispatch, getState }.
let store = createStore(counterReducer)

// 你可以使用 subscribe() 来更新 UI 以响应 state 的更改。
// 通常你会使用视图绑定库（例如 React Redux）而不是直接使用 subscribe()。
store.subscribe(() => console.log(store.getState()))

// 改变内部状态的唯一方法是 dispatch 一个 action。
// 这些 action 可以被序列化、记录或存储，然后再重放。
store.dispatch({ type: 'counter/incremented' })
// {value: 1}
store.dispatch({ type: 'counter/incremented' })
// {value: 2}
store.dispatch({ type: 'counter/decremented' })
// {value: 1}
```



### redux toolkit (RTK)

在我们使用原生redux时，我们会发现一些问题：

1. 整个应用的共享数据都放到了一个state里，如果state过于复杂，那么就会难以维护。

2. 对象形式的state，每次操作都需要先进行复制，再用新数据去替换旧数据部分。

3. switch case 后面的常量维护较为麻烦。

   

所以我们需要使用RTK来帮助解决使用redux中的一些重复性工作和痛点。

安装：需要react-redux搭配使用，两个都要安装。（RTK中已经包含了redux核心库）

```bash
npm install react-redux @reduxjs/toolkit
```

使用

```jsx
// store/modules/student.js
import { createSlice } from '@reduxjs/toolkit'

// 创建仓库切片，传入配置项
const studentSlice = createSlice({
  name: 'student', // 仓库切片名，生成action中的type
  initialState: { // 初始化state值
    name: 'andy',
    age: 18
  },
  reducers: { // 对state操作的各种方法
    setName(state, action){
      // 通过相关库，相比redux现在可以直接修改state，内部会进行对比创建新的state覆盖旧的，依然维持state不可变状态
      // 两个参数，state：切片仓库代理对象，可以直接修改值，不用每次复制state替换
      // action.payload：触发dispatch时传递的参数
      state.name = action.payload
    }
  }
})

export default studentSlice.reducers;

// 切片对象会自动生成action，存储在actions中，向外暴露以供在合适时触发
export const {setName} = studentSlice.actions
```

```js
// store/index.js
// 汇总仓库切片，构建出完整的仓库
import { configureStore } from '@reduxjs/toolkit'
import studentSliceReducer from './studentSlice'

export default const store = configureStore({
  reducer: {
    student: studentSliceReducer
  }
})
```

**最后还需要向全局挂载上store对象**

```jsx
// main.js
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App'
import store from './store'
import {Provider} from 'react-redux'

ReactDOM.render(
	<Provider store={store}>
		<App/>
	</Provider>,
	document.getElementById('root')
)
```



如果我们想使用仓库数据，可以利用react-redux中的两个钩子。

```jsx
import { useSelector, useDispatch } from 'react-redux'
import { setName } from 'store/modules/student'

export default App = () => {
  // 获取整体state中其中的仓库切片
  const stu = useSelector(state => state.student)
  // 获取整体的派发器,触发action
  const dispatch = useDispatch()
  dispatch(setName('lucy'))
  return (
    <div>{stu.name}</div>
  )
}
```



### RTK Query (RTKQ)

在我们使用RTK的时候，如果我们想要与服务器进行数据交互，我们就需要发送异步请求，请求数据返回时需要存储到仓库中并且要在页面更新。在原生redux中实现这一逻辑较为麻烦。并且**“数据获取和缓存” 实际上是一组不同于 “状态管理” 的关注点**。因此最好有一个工具专门完成这个任务。

RTKQ是一个强大的数据获取和缓存工具,旨在简化在 Web 应用程序中加载数据的常见情况，**无需自己手动编写数据获取和缓存逻辑**。

*但实际上目前还是用于axios较多，因为其可以方便的配置请求头，借助拦截器完成一些数据处理等操作，RTKQ在这里可以简单理解一下。*

获取：RTKQ已经集成在了RTK库中，需要时直接引入即可

```js
// createApi 用来创建RTKQ中的api对象，需要一个配置对象作为参数
import { createApi, fetchBaseQuery  } from '@reduxjs/toolkit/query/react'

const studentApi = createApi({
  reducerPath: 'studentApi', // Api对象标识，避免与其他Api或reducer对象重复
  baseQuery: fetchBaseQuery({ baseUrl: 'https://localhost:8080/api' }), // 发送请求方式和信息
  // 指定此api对象的各种操作功能,builder是请求构建器，用来设置具体的请求信息
  endpoints: builder => ({
    getStudents: builder.query({
      query: () => '/students', // 指定请求子路径
      transformRespnse: (res) => return res.data // 处理请求返回数据
    }),
    getStudentsById: builder.query({
      query: (id) => `/student/${id}`,
      keepUnusedDataFor: 0 // 数据缓存时间，秒 请求数据可以控制缓存时间 默认60s
    })
  })
})

// 从api对象中解构出对应方法，方法名是根据builder里的方法自动生成的
export const { useGetStudentsQuery, sueGetStudentsByIdQuery } = studentApi
export default studentApi
```

RTKQ中的api对象本质上和仓库切片一样，也需要汇总到整体仓库中

```js
// 汇总仓库切片，构建出完整的仓库
import { configureStore } from '@reduxjs/toolkit'
import studentApi from './studentApi'

export default const store = configureStore({
  reducer: {
    [studentApi.reducerPath]: studentApi.reducer,
  },
  // 还要将全局的中间件拼接上构建出的api对象的中间件
   middleware: getDefaultMiddleware =>
    	getDefaultMiddleware().concat(studentApi.middleware)
})
```

向全局挂载上store对象后就是具体的使用

```jsx
import { useGetStudentsQuery } from './store/studentApi'

export default const App = () => {
  // 调用api查询方法，获取返回对象
  const { data, isSuccess, isLoading } = useGetStudentsQuery()
  return (
  	<div>
    	{ isLoading && <p>数据加载中。。。</p> }
      { isSuccess && <p>这是数据 {data}</p> }
    </div>
  )
}
```



### RTK + Axios

在开发中常见的一种操作是在仓库中发送请求获取数据然后存储起来，虽然redux toolkit提供了RTKQ发送请求，但常见的依然是使用axios。

由于请求的异步性，仓库切片中的reducers的异步操作会修改state，但此时内部state的代理对象已经被撤销，无法进行修改赋给原仓库，导致报错问题，所以我们需要使用 `createAsyncThunk` 钩子来进行异步操作。

[官网相关描述](https://cn.redux.js.org/tutorials/essentials/part-5-async-logic#%E4%BD%BF%E7%94%A8-createasyncthunk-%E8%AF%B7%E6%B1%82%E6%95%B0%E6%8D%AE)

```ts
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit'

import type { loginForm, userInfoType } from '@/api/user/type'
import { reqLogin } from '@/api/user/index'

// 定义仓库切片初始值与类型
const initialState: {
  token: string | null
  menuRoutes: Array<object>
  userInfo: userInfoType | null
} = {
  token: localStorage.getItem('token'),
  menuRoutes: [],
  userInfo: null
}

/**
 * 用户登录方法
 */
// 创建异步 thunk,相当于是一个异步的action
export const userLogin = createAsyncThunk('userLogin', async (loginForm: loginForm) => {
  const res = await reqLogin(loginForm)
  if (res.data.code === 200) {
    return res.data.data
  } else {
    throw new Error(res.data.msg)
  }
})

const userSlice = createSlice({
  name: 'user',
  initialState,
  reducers: {},
  // 根据异步action的状态进行不同的操作
  extraReducers: builder => {
    builder.addCase(userLogin.fulfilled, (state, action) => {
      state.token = action.payload
      localStorage.setItem('token', state.token)
    })
  }
})

export default userSlice.reducer
```

接下来在相关组件触发那个异步的action即可

```tsx
import { useState } from 'react'
import style from './style.module.scss'

import type { loginForm } from '@/api/user/type'
import { useDispatch } from 'react-redux'
import { ThunkDispatch, AnyAction } from '@reduxjs/toolkit'
import { userLogin } from '@/store/modules/user'

import { useNavigate } from 'react-router-dom'

export default function AppLogin() {
  // 定义接收异步action的类型
  const dispatch: ThunkDispatch<string, loginForm, AnyAction> = useDispatch()
  const navigate = useNavigate()

  const [loading, setLoading] = useState<boolean>(false)

  /**
   * 登录按钮点击回调
   */
  const onFinish = (values: loginForm) => {
    setLoading(true)
    try {
      // 触发异步action
      dispatch(userLogin(values))
      navigate('/')
    } catch (error) {
      console.log(error)
    } finally {
      setLoading(false)
    }
  }

  return (
    <div className={style['app-login']}></div>
  )
}

```

