---
title: Pinia 笔记
icon: state
date: 2023-07-24
category:
  - Vue
  - 工具库
tag:
  - 状态管理
---

**Pinia 官网** <https://pinia.vuejs.org/zh/>



## Pinia简介

pinia是vue官方提出的新一代状态管理工具，主要是为了适配vue3推行的组合式api，所以pinia也同样采用了组合式api的写法，并兼容vuex的选项式写法，成为目前vue3项目最热门的状态管理工具。

下载安装

```bash
yarn add pinia
```

然后在项目src目录下创建 `store/index.ts` 创建仓库，在 `main.ts` 里注册仓库即可

```ts
import { createPinia } from 'pinia'

const store = createPinia()
export default store

// main.ts
import store from '@/store/index'
createApp(App).use(store).mount('#app')
```

之后就可以在 `store/modules/` 下创建分类小仓库供我们使用了；具体使用方法参考 [pinia官方文档](https://pinia.vuejs.org/zh/)

```ts
// src/store/modules/user.ts

import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', () => {
  // 仓库存取用户token
  const token = ref<string | null>(localStorage.getItem('token'))

  /**
   * 用户登录方法
   */
  function userLogin(form: loginForm) {
    // ...
  }

  return { token, userLogin }
})
```

此外的一个问题就是如果页面刷新会导致仓库初始化，数据都变为初始值。对于一些简单数据我们可以直接用 `localStorage` 存储（注意对象的json转化），初始化时直接读取。若想要大范围持久存储，可以使用pinia持久化插件 [pinia-plugin-persistedstate](https://prazdevs.github.io/pinia-plugin-persistedstate/zh/guide/) 具体使用方式查看官方文档。