---
title: Hooks 进阶
icon: react
date: 2023-05-02
category:
  - React
  - 前端框架
tag:
  - react
---

## Hooks

Hooks 是react官方为了推行函数式组件而提出的，就是一些封装好的方法，帮助我们更好的使用函数式组件。事实上函数式组件搭配Hooks确实相比类式组件来说使用更加容易，与vue3有异曲同工之妙。

### useState

State Hook让函数组件也可以有state状态，并进行状态数据的读写操作

1. 语法：`const [xxx,setXxx] = useState(initvalue)` （需要提前import引入，声明在相关逻辑的顶层）

2. useState()说明：

参数：第一次初始化指定的值在内部作缓存（所以更新时组件重新渲染不会再次初始化）   
返回值：包合2个元素的数组，第1个为内当前状态值，第2个为更新状态值的函数

4. setXxx()2种写法：**（注意此方法是异步的，且修改引用值时注意不可变状态，要返回全新的引用值）**

setXxx(newValue)：参数为非函数值，直接指定新的状态值，内部用其覆盖原来的状态值   
setXxx(value=>newValue):参数为函数，接收上一次的状态值，返回新的状态值，内部用其覆盖原来的状态值



### useEffect

Effect Hook可以让你在函数组件中执行副作用操作（用于模拟类组件中的生命周期钩子）

React中的副作用操作： 发ajax请求数据获取；设置订阅/启动定时器；手动更改直实D0M

语法和说明：

```js
useEffect(()=> {
  // 在此可以执行任何带副作用操作
  
  return()=>{ // 可以返回一个函数，会在组件卸载前执行，也可以不返回
		//在此做一些收尾工作，比如清除定时器/取消订阅等
	}
},[statevalue])
// 传递的函数会在此数组内值变化时调用 如果指定的是[]，回调函数只会在第一次render(）后执行
```

可以把useEffect Hook看做如下三个函数的组合
componentDidMount()   componentDidUpdate()   componentWillUnmount()



### useRef

Ref Hook可以在函数组件中存储/查找组件内的标签 或 任意其它状态，并且让这个状态脱离react控制，即使变化也不引起组件的渲染。

语法：`const refContainer = useRef(null)` 确保变量名和你模板中ref绑定的值一致

保存标签对象，获取时只需要通过 `.current` 即可获取到相应Dom节点。



### useContext

一种组件通信方式，主要用于父组件和孙/后代组件之间的通信

1. 创建Context容器对象  `const XxxContext = createContext()`

2. 在父组件中渲染子组件时，外层包裹一个 Provider 组件，并通过 value 属性传递数据

   ```jsx
   import { createContext } from 'react'
   
   const XxxContext = createContext()
   
   <XxxContext.Provider value={ data }>
   	<son />
   </XxxContext.Provider>
   ```

3. 后代组件读取数据：`useContext(XxxContext)`

   ```jsx
   // 仅适用于类组件的方式
   static contextType = xxxContext  // 声明接受context
   this.context.data  // 读取context里的数据
   
   //第二种方式：函数式组件,需要引入创建的上下文
   import { useContext } from 'react'
   const data = useContext(XxxContext);
   ```
   
   
   

### useReducer

当我们使用`useState`创建了一个响应式变量后，我们可能会在不同环境下使用setxxxx来写下不同的逻辑去修改它，但是这样会导致创建的变量和对应的修改方法变得四处拆分，每次维护都变得很困难，所以我们希望对于有复杂处理逻辑的变量可以让其修改方法集中管理，`useReducer`孕育而生。

语法： `const [state, dispatch] = useReducer(reducer, initialArg, init?)`

第一个参数是reducer函数，第二个参数是state初始值。返回一个state值和dispatch派发器。  

具体使用方式为通过调用dispatch派发器，传递处理逻辑的类型，reducer拿到后调用相关代码并返回处理好的state即可。

```jsx
import {useReducer, useState} from 'react';

// 为了避免每次渲染时都创建reducer，可以放在组件外面
const reducer = (state, action) => {
    switch(action.type){
        case 'add':
            return state + 1;
        case 'sub':
            return state - 1;
      	default:
        		return state;
    }
};

function App() {
    const [count, countDispath] = useReducer(reducer,1);
    return (
        <div className="App">
            {count}
            <div>
                <button onClick={()=>countDispath({type:'sub'})}>-</button>
                <button onClick={()=>countDispath({type:'add'})}>+</button>
            </div>
        </div>
    );
}
export default App;
```





### useCallback

因为组件每次重新渲染都会让其内部引用值被重新构建，`useCallback`将函数创建后的引用保存，当函数组件下一次重新渲染时，只要依赖项不变就会直接返回之前保存的引用，而不是重新创建引用。（重新创建引用会消耗react性能）

语法： `const cachedFn = useCallback(fn, dependencies)`

第一个参数是保存引用的函数；第二个参数是一个数组，表示重新创建引用的依赖项。



### useMemo

`useMemo`会执行传入的函数并拿到返回值，并且在依赖项变化时重新执行函数拿到性的返回值。

语法：`const cachedValue = useMemo(calculateFun, dependencies)`

第一个参数是返回计算值的函数，第二个参数是依赖项数组。



### useImperativeHandle

这个hook一般是搭配 **forwardRef** 使用的，父组件传递 ref 给子组件后，子组件就可以用这个函数建立依赖项赋值关系，只有依赖项变化时才会重新赋值（如用到随机数等）。

语法：`useImperativeHandle(ref, createHandleFn, dependencies?)`

第一个参数是ref；第二个参数是函数，函数的返回值会赋值给ref的current属性上；第三个参数是依赖项，只要依赖项不变，那么`ref.current`就不会被重新赋值。



### useLayoutEffect

其功能与 useEffect 基本一致，并且我们主要使用 `useEffect` 。

`useEffect` 是在组件首次渲染完成生成真实dom到页面上后，将回调函数推入**异步队列**等待执行

`useLayoutEffect` 是在组件首次渲染完成生成真实dom到页面上后，将回调函数推入**同步队列**等待执行





### 自定义hooks

在遇到一些通用形式的功能时，即依靠变量实现某些功能，且在全局有多处用到，那么我们就可以封装一个自定义hooks来使用。(感觉上就是封装自定义函数)

```jsx
import {useState} from "react";

//自定义hooks一定要以use开头
export default function useRequestLoading(){
	const [loading,setLoading] = useState(false);
  
	const executeRequest = async (promiseFn) => {
		setLoading(true);
		const result await promiseFn();
		if (result.code !==200){
			alert(result.message);
    }
		setLoading(false);
  }
  
	return {
    loading,
		executeRequest
  }
}
```





## 函数式组件其他API

### forwardRef

如果我们想获取到jsx中的标签，我们可以使用`useRef`这个hook去绑定，但是如果我们想获取到其他函数式组件的dom，我们就需要使用react提供的`forwardRef`这个高阶函数。

语法： `const SomeComponent = forwardRef(render)`

`forwardRef`接收一个组件作为参数，并对这个组件进行改造后返回，返回的组件扩展了一个ref属性作为第二个参数，此时的ref可以用来传递数据，即子组件传递数据或dom节点给父组件。  一般会搭配 `useRef` 使用。

```jsx
import { forwardRef, useRef } from 'react';

// 子组件  forwardRef接收一个组件，扩展了第二个ref参数，用来传递数据
export default MyInput = forwardRef(function MyInput(props, myRef) {
  const inputDom = useRef(null)
  return (
    <label>
      <input ref={myRef} />
    </label>
  );
});
```

```jsx
// 父组件
function Form() {
  const myRef = useRef(null);

  function handleClick() {
    ref.current.focus();
  }

  return (
    <form>
      {/* 向子组件传递了一个ref，子组件拿到可以绑定dom节点 或 用 .current 传递数据 */}
      <MyInput ref={myRef} />
      <button type="button" onClick={handleClick}>
        Edit
      </button>
    </form>
  );
}
```

