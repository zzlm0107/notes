---
title: NUXT3
icon: view
date: 2023-09-16
category:
  - SSR
tag:
  - ssr
---



## 初识nuxt

[官方网站](https://nuxt.com.cn/)

nuxt使用了vue作为框架语法，所以我们只需要像平时用vue一样书写组件即可。

nuxt一个重要特点就是 **约定大于配置**，即官方已经规定好了每个特殊文件夹的命名，开发者只需要遵守这些默认的约定，即可不用再书写过多的配置。

```txt
|- .nuxt      			非工程代码，存放运行的编译结果
|- node_modules
|- public
|- server				接口服务器目录
|- .gitignore
|- .npmrc
|- app.vue				根组件
|- nuxt.config.ts		nuxt配置文件
...
```





## 路由

Nuxt的一个核心特性是文件系统路由器，即**约定项目页面都放置于pages/文件夹下**，这样nuxt就会依靠文件路径，自动帮你创建好路由，而无需再使用路由配置文件。

```txt
|-	pages
|	|-	index.vue			默认主页
|	|-	video/index.vue		/video/ 的默认页面
|	|-	video/hello.vue		/video/hello 对应页面
```

搭配路由出口和跳转链接即可实现完整的路由模块，**当然也支持编程式跳转方式。**

```vue
<template>
  <!-- 路由链接 -->
  <nuxt-link to="/">首页</nuxt-link>
  <nuxt-link to="/video">视频默认页</nuxt-link>
  <nuxt-link to="/video/hello">视频详情页</nuxt-link>

  <!-- 页面路由出口 -->
  <nuxt-page />
</template>
```



此外对于路由传参方面：

- query参数，直接在相应页面里使用 `useRoute()` 获取到route实例对象后通过 `route.query` 获取；

- params参数，由于现在我们不再需要书写路由表，所以无法像像以前的写法那样为动态的params参数指定公共组件；但现在nuxt允许我们直接创建动态params参数的公共组件，只需将组件名用 `[]` 中括号包裹即可，里面就是params的变量名称。

  ```vue
  //  pages/video/[id].vue
  //  这样的话，路由路径 /video/12 会自动匹配到这个组件，且将params参数 12 传递到这个组件中
  <template>
    <div>
      这是动态params参数页,参数是{{ route.params.id }}
    </div>
  </template>
  
  <script setup lang="ts">
  const route = useRoute()
  console.log(route.params.id)
  </script>
  ```





## 组件

对于pages下面的组件，我们按照常规的写法创建组件，然后在需要的地方导入使用即可；

对于全局组件，nuxt约定 **将全局组件都放到根目录下面的components文件夹下**，nuxt会帮你进行自动全局注册，我们直接使用即可；如果在components文件夹下有多个层级，则nuxt会根据层级文件夹名自动生成新的组件名。

如果你在嵌套目录中有一个组件，例如:

```
| components/
--| base/
----| foo/
------| Button.vue
```

... 然后组件的名称将基于它自己的路径、目录和文件名，删除重复的段。因此，组件的名称将是:

```
<BaseFooButton />
```





## SEO

由于nuxt没有提供根html，那我们如何书写网页相关的标题描述等信息呢，事实上在nuxt中这些可以通过配置对象来实现。我们可以在在`nuxt.config.ts`中提供[app.head](https://nuxt.com.cn/docs/api/configuration/nuxt-config#head)属性（这个方法不允许你提供响应式数据）,或在`app.vue` 中的方法的配置对象中实现全局的配置。

```vue
<script setup lang="ts">
useHead({
  // 配置网站标题
  title: 'nuxt3-app',
  // 配置meta标签数组
  meta: [
    { name: 'description', content: 'My amazing site.' }
  ]
})
</script>
```

不仅是在`app.vue` 中，我们可以在任何一个页面中都使用 `usehead` 方法配置单独页面的元数据，并且单独配置的优先级更高。

更多细节参考 [官网文档](https://nuxt.com.cn/docs/getting-started/seo-meta)





## 静态资源

Nuxt使用两个目录来处理像stylesheets, fonts 或 images这样的资产。

- [`public/`目录](https://nuxt.com.cn/docs/guide/directory-structure/public)内容原样提供给服务器根目录。

  例如，引用`public/img/`目录下的图片文件，可在静态URL `/img/nuxt.png` 中获得（是绝对路径不是相对路径哦）

- [`assets/`目录](https://nuxt.com.cn/docs/guide/directory-structure/assets)包含你想要构建工具处理的静态资源。

  此外，对于全局声明的css变量等文件，nuxt也约定将其放到assets文件夹下；

  ```ts
  export default defineNuxtConfig({
    vite: {
      css: {
        preprocessorOptions: {
          scss: {
            additionalData: '@use "@/assets/_colors.scss" as *;'
          }
        }
      }
    }
  })
  ```

  



## 配置与访问

`runtimeConfig` 向应用程序的其余部分公开了诸如环境变量之类的值。默认情况下，这些键只在服务器端可用，并且可以使用环境变量重写。`runtimeConfig.public`中的键也可以在客户端使用。

```ts
export default defineNuxtConfig({
  runtimeConfig: {
    // 在此配置的变量只能在服务端访问
    apiSecret: '123',
    // public对象下的变量客户端也能访问到
    public: {
      apiBase: '/api'
    }
  }
})


// .env文件中 这将覆盖apiSecret的值
NUXT_API_SECRET = token
```

使用配置的相关变量：

```vue
<script setup>
const runtimeConfig = useRuntimeConfig()
</script>
```



除了在 `nuxt.config.ts` 中进行配置相关变量外，我们还有另一种方式就是创建一个 `app.config.ts`,然后在这个文件里面配置一些变量使用。

```ts
// app.config.ts
export default defineAppConfig({
  title: 'Hello Nuxt',
  theme: {
    dark: true,
    colors: {
      primary: '#ff0000'
    }
  }
})
```

```vue
<script setup>
const appConfig = useAppConfig()
</script>
```



**区别**

- `nuxt.config.ts` 中配置那些需要控制可访问性的变量，即在服务端或客户端的访问权限不同，以及需要被环境变量替换的一些配置；
- `app.config.ts` 中配置的变量在服务端和客户端都能被访问到，适合一些网站相关的配置，如主题变量，标题和任何不敏感的项目配置。





## mock与请求

### mock数据

nuxt支持在本地进行数据模拟，就相当于内置了mock。前面我们提到 `server` 文件夹下就是存放的接口，事实上，Nuxt会自动扫描`~/server/api`, `~/server/routes`, 和 `~/server/middleware`目录中的文件，以注册具有HMR支持的API和服务器处理程序。

每个文件都应该导出一个用`defineEventHandler()`定义的默认函数。处理程序可以直接返回JSON数据，一个`Promise`或使用`event.node.res.end()`发送响应。所以对于本地模拟数据的需求，只需规范好对应的文件路径，即可自动生成对应路径的api接口进行请求测试。

```ts
// server/api/test/[id].post.ts
// 对应的接口为    /api/test/id   post请求  其中id是params参数
// 这意味着我们可以获取到这次请求的参数，从而返回不同的数据

export default defineEventHandler((event) => {
  // const query = getQuery(event) // 获取query参数
  // const body = await readBody(event) // 获取请求体
  return {
    api: 'works',
    params: event.context.params.name
  }
})
```

然后就可以通过`useFetch` 方法去请求这个模拟接口，当然此方法也支持去请求真实的接口：

```ts
async function aaa(params: number) {
  const res = await useFetch('/api/test/12', {
    method: 'post'
  })
  console.log(res)
}
```

此外，全覆盖路由有助于处理备用路由。例如，创建一个名为`~/server/api/foo/[...].ts` 的文件，将为所有不匹配任何路由处理程序的请求注册一个catch-all路由.



此外，当然也支持利用 **axios** 获取其他服务器上的请求，但需要注意一个问题：

- 使用axios发起的请求通过服务端渲染编译一次后，获取到了数据填充到模板上形成了html文件返回给了客户端，但是客户端拿到的这个html里依然包含组件中相关的js逻辑，也就是说，客户端解析js时会再一次发起请求，这样数据就被获取了两次，并且是没有必要的。**所以nuxt推荐使用他们提供的useFecth等函数（只会在服务端渲染时执行一次）获取数据而不是使用第三方工具库。**



### 请求api

**nuxt提供的相关请求api：**

- `useFetch()`

  封装的请求函数，会自动生成基于URL和fetch选项的键。

  ```ts
  useFetch('/api/test', {
      method: 'post',
      baseURL: 'http://example.com',
      body: {}
      //...  参考https://nuxt.com.cn/docs/api/composables/use-fetch
  }).then(res => {
      console.log(res)
  })
  ```

  返回值：

  - **data**：传入的异步函数的结果。
  - **pending**：一个布尔值，指示数据是否仍在获取中。
  - **refresh**/**execute**：一个函数，用于刷新`handler`函数返回的数据。
  - **error**：如果数据获取失败，则为错误对象。

- `useAsyncData()`

  同样是获取数据，但需要传递一个key，并且可以在请求前处理一些逻辑

  ```ts
  const { data } = await useAsyncData('count', () => {
      // 在这我可以处理一些逻辑
      return $fetch('/api/count')
  })
  ```

  该方法相比于`useFetch()`几乎一致，只是该方法需要手动提供key而后者会自动生成，返回值也基本相同。

- `useLazyFetch()`

  该方法与带有`lazy: true`选项集的`useFetch`相同。该方法发起请求并不会阻塞页面渲染，也就是说该方法发起请求在数据还未返回时页面就会更新，这意味着我们需要处理返回数据暂时为null的情况。 而`useFetch` 请求时会先展示之前的数据，直到结果返回时才更新数据，中间没有数据暂时为null的情况。

- `useLazyAsyncData()`

  方法与`useAsyncData`具有`lazy: true`选项集相同。



### 封装与拦截器

根据官方的方法，我们完全可以自己封装一个公共的请求方法，并通过拦截器实现与封装axios相同的功能函数：

```ts
const config = {
    baseURL: 'http://example.com',
    // 请求拦截器
    onRequest({ request, options }) {
        // 设置请求头
        options.headers = options.headers || {}
        options.headers.authorization = '...'
    },
    // 相应拦截器
    onResponse({ request, response, options }) {
    	
    },
}

function myUseFecth(url, requestConfig) {
    return new Promise(resolve => {
        useFetch(url, {
            ...config
            ...requestConfig     
        }).then(res => {
             resolve(res)
         })
    })
}
```





## cookie和token

由于cookie是存储在客户端的，使用ssr实际上是获取不到的，所以nuxt提供了一个函数用以从服务器端访问并代理cookie到api。

`useRequestHeaders`方法可以在客户端像服务端请求这个组件html时拿到这次请求的headers里面的一些字段内容，由于这次请求时客户端发起的，所以里面会有一些客户端字段，这样拿到后再添加到服务端这边的请求方法，就实现了cookie的代理操作

```vue
<script setup>
const headers = useRequestHeaders(['cookie'])
const { data } = await useFetch('/api/me', { headers })
</script>
```

正对token，由于是服务端渲染，当然也就没有本地存储等相关内容，所以携带token一种方式就是把token放到cookie里，然后再从cookie中获取到添加到请求头中。





## 渲染模式

nuxt允许我们为不同的路径下的页面配置不同的路由渲染规则，从而实现更好的性能优化： [更多细节](https://nuxt.com.cn/docs/guide/concepts/rendering#%E8%B7%AF%E7%94%B1%E8%A7%84%E5%88%99)

```ts
export default defineNuxtConfig({
  routeRules: {
    // Static page generated on-demand, revalidates in background
    '/blog/**': { swr: true },
    // Static page generated on-demand once
    '/articles/**': { static: true },
    // 配置该路由下的响应头信息
    '/_nuxt/**': { headers: { 'cache-control': 's-maxage=0' } },
    // 该路由下关闭服务端渲染，使用客户端渲染
    '/admin/**': { ssr: false },
    // Add cors headers
    '/api/v1/**': { cors: true },
    // 该路由下实现重定向
    '/old-page': { redirect: '/new-page' },
    '/old-page2': { redirect: { to: '/new-page', statusCode: 302 } }
  }
})
```

