---
title: Vuex 笔记
icon: vue
date: 2022-12-25
category:
  - Vue
  - 工具库
tag:
  - 状态管理
---


在Vue中实现集中式状态（数据）管理的一个Vue插件，对vue应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信。

**官方文档** <https://vuex.vuejs.org/zh-cn/>

## 基本使用

```js
	/* store/index.js */
   const actions = {
       //响应组件中加的动作
   	jia(context,value){
   		// console.log('actions中的jia被调用了',miniStore,value)
   		context.commit('JIA',value)
   	},
   }
   
   const mutations = {
       //执行加
   	JIA(state,value){
   		// console.log('mutations中的JIA被调用了',state,value)
   		state.sum += value
   	}
   }
   
   const getters = {
   	bigSum(state){
   		return state.sum * 10
   	}
   }
   
   //初始化数据
   const state = {
      sum:0
   }
   
   //创建并暴露store
   export default new Vuex.Store({
   	actions,
   	mutations,
   	state,
    getters
   })
```

```js
  // main.js
	//引入Vue核心库
   import Vue from 'vue'
   //引入Vuex
   import Vuex from 'vuex'
   //引用Vuex
   Vue.use(Vuex)
   //引入store
   import store from './store'
   ......
   
   //创建vm
   new Vue({
   	el:'#app',
   	render: h => h(App),
   	store
   })
```

1. 组件中读取vuex中的数据：`this.$store.state.sum`

2. 组件中修改vuex中的数据：`this.$store.dispatch('action中的方法名',数据)`或 `this.$store.commit('mutations中的方法名',数据)`

   > 备注：若没有网络请求或其他业务逻辑，组件中也可以越过actions，即不写`dispatch`，直接编写`commit`



## 映射仓库

```js
// 使用时都需要从vuex中引入
import { mapState,.... } from 'vuex'
```



1. **mapState方法：**用于帮助我们映射`state`中的数据为计算属性

```js
 computed: {
       //借助mapState生成计算属性：sum、school、subject（对象写法）
        ...mapState({mysum:'sum',myschool:'school',mysubject:'subject'}),
            
       //借助mapState生成计算属性：sum、school、subject（数组写法）
       ...mapState(['sum','school','subject']),
   },
```

2. **mapGetters方法：**用于帮助我们映射`getters`中的数据为计算属性

```js
   computed: {
       //借助mapGetters生成计算属性：bigSum（对象写法）
       ...mapGetters({bigSum:'bigSum'}),
   
       //借助mapGetters生成计算属性：bigSum（数组写法）
       ...mapGetters(['bigSum'])
   },
```

3. **mapActions方法：**用于帮助我们生成与`actions`对话的方法，即：包含`$store.dispatch(xxx)`的函数

```js
   methods:{
       //靠mapActions生成：incrementOdd、incrementWait（对象形式）
       ...mapActions({incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
   
       //靠mapActions生成：incrementOdd、incrementWait（数组形式）
       ...mapActions(['jiaOdd','jiaWait'])
   }
```

4. **mapMutations方法：**用于帮助我们生成与`mutations`对话的方法，即：包含`$store.commit(xxx)`的函数

```js
   methods:{
       //靠mapActions生成：increment、decrement（对象形式）
       ...mapMutations({increment:'JIA',decrement:'JIAN'}),
       
       //靠mapMutations生成：JIA、JIAN（对象形式）
       ...mapMutations(['JIA','JIAN']),
   }
```

> 备注：mapActions与mapMutations使用时，若需要传递参数需要：在模板中绑定事件时传递好参数，否则参数是事件对象。



## 模块化与命名空间

目的：让代码更好维护，让多种数据分类更加明确。

```js
// 模块化仓库
export default {
     namespaced:true,//开启命名空间
     state:{x:1},
     mutations: { ... },
     actions: { ... },
     getters: {
       bigSum(state){
          return state.sum * 10
       }
     }
   }
      
// store/index.js
const store = new Vuex.Store({
	modules: {
		user,
		platform
	}
})
export default store
```



**开启命名空间后读取数据：**

1. 组件中读取state数据：

```js
   //方式一：自己直接读取
   this.$store.state.personAbout.list
   //方式二：借助mapState读取：
   ...mapState('countAbout',['sum','school','subject']),
```

2. 组件中读取getters数据：

```js
   //方式一：自己直接读取
   this.$store.getters['personAbout/firstPersonName']
   //方式二：借助mapGetters读取：
   ...mapGetters('countAbout',['bigSum'])
```

3. 组件中调用dispatch：

```js
  //方式一：自己直接dispatch
  this.$store.dispatch('personAbout/addPersonWang',person)
  //方式二：借助mapActions：
  ...mapActions('countAbout',{incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
```

4. 组件中调用commit：

```js
   //方式一：自己直接commit
   this.$store.commit('personAbout/ADD_PERSON',person)
   //方式二：借助mapMutations：
   ...mapMutations('countAbout',{increment:'JIA',decrement:'JIAN'}),
```
