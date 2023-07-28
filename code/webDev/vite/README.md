---
title: Vite 笔记
icon: vite
date: 2023-07-02
category:
  - 构建工具
tag:
  - vite
---



## vite相较于webpack的优势

**vite官方文档** <https://cn.vitejs.dev/>

然而，当我们开始构建越来越大型的应用时，需要处理的 JavaScript 代码量也呈指数级增长。包含数千个模块的大型项目相当普遍。我们开始遇到性能瓶颈 —— 使用 JavaScript 开发工具（构建工具）通常需要很长时间（甚至是几分钟！）才能启动开发服务器，即使使用 HMR(热更新)，文件修改后的效果也需要几秒钟才能在浏览器中反映出来。如此循环往复，迟钝的反馈会极大地影响开发者的开发效率和幸福感。


起因: 我们的项目越大 ----> 构建工具（webpack）所要处理的js代码就越多 【跟webpack的一个构建过程（工作流程）有关系】

造成的结果: 构建工具需要很长时间才能启动开发服务器
```
yarn start
yarn dev

npm run dev 
npm run start
```

webpack能不能改? 如果一旦要改 那么将会动到webpack的大动脉

webpack支持多种模块化: 你的工程可能不只是跑在浏览器端

```js
// index.js
// 这一段代码最终会到浏览器里去运行
const lodash = require("lodash"); // commonjs 规范
import Vue from "vue"; // es6 module

// webpack是允许我们写多种模块化规范的代码
```

webpack的编译原理, AST 抽象语法分析的工具 分析出你写的这个js文件有哪些导入和导出操作
构建工具是运行在服务端的

```js
// webpack对上述代码的一个转换结果
const lodash = webpack_require("lodash");
const Vue = webpack_require("vue");
```

因为webpack支持多种模块化, 他一开始必须要统一模块化代码, 所以意味着他需要将所有的依赖全部读一遍


vite会不会直接把webpack干翻, vite是基于es modules的, 侧重点不一样, webpack更多的关注兼容性, 而vite关注浏览器端的开发体验



## 搭建vite工程

vite官网搭建工程指南: https://cn.vitejs.dev/guide/

- 使用自选配置去构建vite项目 
  ```bash
  npm create vite@latest
  
  yarn create vite
  ```
  会先安装vite的脚手架 create-vite，然后执行相关配置使用脚手架开启构建。
  
  官网提供了多种预设可供选择：
  ```bash
  # npm 6.x
  npm create vite@latest my-vue-app --template vue
  
  # npm 7+, extra double-dash is needed:
  npm create vite@latest my-vue-app -- --template vue
  ```



- vite是一个构建工具，而create-vite是它的脚手架，脚手架内置了vite

  webpack是构建工具，而webpack-cli是一个内置了webpack的脚手架（相当于）



## vite的模块预加载

当我们使用 `npm init -y` 初始化package.json时，安装各种包后在js文件直接import引入使用浏览器是不会读取到的，因为浏览器并不会去读取 node_modules 里的文件，当然不会找到引入的包。

（原因：如果能读取node_modules 里的包，那包里也会有其他依赖，层层嵌套，导致性能爆炸）

此时我们可以通过 `npm install vite` 安装vite，向package.json里的scripts配置添加命令，命令运行vite，就可以让vite开启本地服务器，能顺利读到node_modules里的包，那vite的模块加载机制是如何实现的呢？

```js
import _ from "lodash"; // lodash可能也import了其他的东西
```

vite在处理的过程中如果说看到了有非绝对路径或者相对路径的引用, 他则会尝试开启路径补全

```js
import _ from "/node_modules/.vite/lodash"; // lodash可能也import了其他的东西

import __vite__cjsImport0_lodash from "/node_modules/.vite/deps/lodash.js?v=ebe57916";
```

找寻依赖的过程是自当前目录依次向上查找的过程, 直到搜寻到根目录或者搜寻到对应依赖为止



因为我们无法得知所引用的库是 commonJS 规范导出还是 ES6 module规范导出，所以vite就需要使用 **依赖预构建** 解决：
**依赖预构建**: 首先vite会找到对应的依赖, 然后调用esbuild(对js语法进行处理的一个库), 将其他规范的代码转换成esmodule规范, 然后放到当前目录下的node_modules/.vite/deps, 同时对esmodule规范的各个模块进行统一集成 

他解决了3个问题: 
1. 不同的第三方包会有不同的导出格式(这个是vite没法约束人家的事情)
2. 对路径的处理上可以直接使用.vite/deps, 方便路径重写
3. 网络多包传输的性能问题(也是原生esmodule规范不敢支持node_modules的原因之一), 有了依赖预构建以后无论他有多少的额外export 和import, vite都会尽可能的将他们进行集成最后只生成一个或者几个模块，而不是每个包的每个依赖都单独引入。



## vite环境变量配置

> 环境变量: 会根据当前的代码环境产生值的变化的变量就叫做环境变量

代码环境:
1. 开发环境
2. 测试环境
3. 预发布环境
4. 灰度环境
5. 生产环境



默认环境文件命名：

.env: 所有环境都需要用到的环境变量

.env.development: 开发环境需要用到的环境变量(默认情况下vite将我们的开发环境取名为development)

.env.production: 生产环境需要用到的环境变量(默认情况下vite将我们的生产环境取名为production)



在vite中的环境变量处理:

vite内置了dotenv这个第三方库。dotenv会自动读取 `.env文件`, 并解析这个文件中的对应环境变量 并将其注入到process对象下(但是vite考虑到和其他配置的一些冲突问题, 他不会直接注入到process对象下)

vite给我们提供了一些措施:我们可以调用vite的loadEnv来手动确认env文件
```js
// vite.config.js
import { defineConfig, loadEnv } from 'vite';

export default ({ mode }) => {
  //参数mode为开发模式或生产模式
  //console.log(mode);  // development or product
  
  //npm run dev --mode development 会将mode设置为development传递进来
  
  // process.cwd() 获取当前vite的工作目录
  const env=loadEnv(mode, process.cwd());   // 获取.env文件里定义的环境变量
  //console.log(env);   //变量在命令行里打印出来
  
  return defineConfig({
    plugins: [vue()],
  })
}
```

当我们调用loadenv的时候, 他会做如下几件事:

1. 直接找到.env文件 并解析其中的环境变量 并放进一个对象里
2. 会将传进来的mode这个变量的值进行拼接: ```.env.development```,  并根据我们提供的目录去取对应的配置文件并进行解析, 并放进一个对象

- 实际上就是先读取全局的.env文件，放进一个对象；再根据执行命令 `npm run dev --mode develop` 的mode找到对应 `.env.develop` 文件，读取开发环境的变量放入同一个对象，重复会进行覆盖。因为在package.json执行命令mode可以自定义，并且对应.env文件名也可以自定义，从而实现区分不同环境。

  

**此外如果是客户端, vite会将对应的环境变量注入到 `import.meta.env` 里去,这样就可以在项目文件里访问环境变量。**

vite做了一个拦截, 他为了防止我们将隐私性的变量直接送进import.meta.env中, 所以他做了一层拦截, 如果你的环境变量不是以 **VITE_ 开头** 的, 他就不会帮你注入到客户端中去, 如果我们想要更改这个前缀, 可以去使用envPrefix配置



## vite对css的处理

vite项目天生就支持对css文件的直接处理

1. vite在读取到main.js中引用到了Index.css

2. 直接去使用fs模块去读取index.css中文件内容

3. 直接创建一个style标签, 将index.css中文件内容直接copy进style标签里

4. 将style标签插入到index.html的head中

5. 将该css文件中的内容直接替换为js脚本(方便热更新或者css模块化), 同时设置Content-Type为js 从而让浏览器以JS脚本的形式来执行该css后缀的文件

   

由于多人合作时类名可能会重复导致样式混乱，所以vite也是支持 **cssmodule** css模块化 （全部都是基于node）

1. `xxx.module.css` ，不再直接引入，而是import模块化引入，使用时相当于对象的形式，样式类名变成了引入css模块的属性
2. 他会将你的所有类名进行一定规则的替换（将footer 替换成 _footer_i22st_1）
3. 同时创建一个映射对象{ footer: "_footer_i22st_1" }
4. 将替换过后的内容塞进style标签里然后放入到head标签中 (能够读到index.html的文件内容)
5. 将 `componentA.module.css` 内容进行全部抹除, 替换成JS脚本
6. 将创建的映射对象在脚本中进行默认导出



## vite的css配置

在vite.config.js中我们通过css属性去控制整个vite对于css的处理行为,包括对**cssmodule**的处理配置，**css预处理器**全局执行参数的配置，以及postcss的配置。

```js
// vite.config.js

import { defineConfig } from 'vite'

// 引入预设环境包,里面包括css语法降级，编译插件等多种包
const postcssPresetEnv = require('postcss-preset-env')

export default defineConfig({
  optimizeDeps: {
    exclude: [], // 将指定数组中的依赖包不进行依赖预构建
  },
  envPrefix: "ENV_", // 修改环境变量中的默认开头关键词
  css: {
    modules: {
      localConvention: "camelCaseOnly", //修改生成的配置对象的key的展示形式(驼峰还是中划线形式)
      scopeBehaviour: "local", // 配置当前的模块化行为是模块化还是全局化 
      // (有hash就是开启了模块化的一个标志, 因为他可以保证产生不同的hash值来控制我们的样式类名不被覆盖)
      // generateScopedName: 生成的类名的规则(可以配置为函数, 也可以配置成字符串规则
      hashPrefix: "xxx", // 参与生成css的hash类名生成，加密保护安全性更高
      globalModulePaths: {"./componentA.module.css"} // 代表你不想参与到css模块化的路径
    },
    preprocessorOptions: {
      less: { // 整个配置对象最终都会传给less作为编译时的配置参数

         // 这是直接给less配置，事实上在webpack我们给less-loader配置即可
        math: "always", // 总是处理数学公式
        globalVars：{ // 全局变量
          myColor: "red",
        },
        devSourcemap: true // 开启编译后的css对源文件里面内容的索引
      }
    },
    // vite配置优先级更高，如果这里没有配置，vite会尝试读取工作目录下的postcss.config.js
    postcss: {
      plugins: [postcssPresetEnv(/* optionsObject */)]
    }
  }
})
```



## 扩展：postcss 介绍

**postcss 与 css 的关系相当于 babel 与 js 的关系**

postcss 的功能就是对我们书写的css代码进行转换，将一些未来的，实验性的css代码进行降级，转换为低版本浏览器也可以认识的css文件，保证css在各版本浏览器执行起来是万无一失的。

事实上在早期postcss有插件可以处理less和sass语法，可以一步到位直接从less/sass转css再降级转换，但随着less/sass的不断更新导致的同步语法困难性，这些插件最终也是停止维护了，目前的做法是书写的less和sass语法通过各自的预处理器转换为css后再交给postcss处理。postcss只用专心研究如何将css进行转换降级即可，所以业内就产生了一个新的说法: 说 **postcss是后处理器**



**使用postcss**

1. 安装依赖

```bash
# 安装postcss和相关脚手架和预设环境
npm install postcss postcss-cli postcss-preset-env -D
```

2. 书写描述文件

postcss配置文件： `postcss.config.js`

```js
// postcss.config.js

// 引入预设环境包,里面包括css语法降级，编译插件等多种包
const postcssPresetEnv = require('postcss-preset-env')

module.exports = {
  plugins: [postcssPresetEnv(/* optionsObject */)]
}
```



## vite静态资源及别名配置

在vite.config.js中可以通过 `resolve.alias` 配置路径别名，加载资源时直接使用别名引入路径而不要使用多层级相对路径形式。同时也可以配置一些静态资源打包规则。

```js
// vite.config.js

import { defineConfig } from 'vite'
// npm i @types/node -D  安装node的ts声明
import path from 'path'

export default defineConfig({
  resolve: {
    alias: { // 配置路径别名
      "@": path.resolve(__dirname, './src'),
      "@assets": path.resolve(__dirname, './src/assets')
    } 
  },
  build: {
    assetsInlineLimit: 4096, // 4kb 控制静态资源中低于设定大小的图片转换为base64格式
    outDir: 'dist', // 打包的输出文件夹名称
    assetsDir: 'static', // 打包的输出文件夹里存放静态资源的文件夹名称
    emptyDir：true // 打包时先清除dist目录下的所有文件
  }
})
```

如果想要路径获取智能提示，还需要在tsconfig.json中做对应配置

```json
"baseUrl": ".",
"paths": {
  "@/*": ["src/*"]
},
```





## vite插件

vite会在生命周期的不同阶段去调用不同的插件以达到不同的目的，所以插件就是在生命周期的一定阶段执行从而实现相关功能的库。

可能会遇到插件与npm版本不兼容问题
nvm: node的一个版本管理工具，需要和node一样从网上全局下载安装

vite常用插件： **vite-aliases** **vite-plugin-html** **vite-plugin-mock**

vite-aliases：此插件会读取src目录下的文件夹命名，帮助我们进行目录别名转换，例如将src替换为@，/src/assets替换为@assets

vite-plugin-html：此插件帮助我们给项目的网页名通过变量指定 此插件使用时需要修改项目index.html的title标签内容为 `<%= title %>`（ejs语法）,之后在vite.config.js指定变量具体值即可。

vite-plugin-mock: 此插件帮助我们mock接口数据，其依赖项就是mockjs，所以两个包都需要安装

```js
// vite.config.js

import { defineConfig, UserConfigExport, ConfigEnv } from 'vite'
import { ViteAliases } from 'vite-aliases'
import { createHtmlPlugin } from 'vite-plugin-html'
import { viteMockServe } from 'vite-plugin-mock'

export default ({ command }: ConfigEnv): UserConfigExport) => defineConfig({
  plugins: [
    ViteAliases(),
    createHtmlPlugin({
      /**
       * 需要注入 index.html ejs 模版的数据
       */
      inject: {
        data: {
          title: '我是项目标题',
          // injectScript: `<script src="./inject.js"></script>`,
        },
      },
    }),
    viteMockServe({ // 默认会去找/mock/ 与/src同级
        // default
        mockPath: 'mock',
        localEnabled: command === 'serve', // 匹配启动服务器的命令
      })
  ]
})
```

```ts
// /mock/index.ts
/*
{
  // 请求地址
  url: string;
  // 请求方式
  method?: MethodType;
  // 设置超时时间
  timeout?: number;
  // 状态吗
  statusCode?:number;
  // 响应数据（JSON）
  response?: ((opt: { [key: string]: string; body: Record<string,any>; query:  Record<string,any>, headers: Record<string, any>; }) => any) | any;
  // 响应（非JSON）
  rawResponse?: (req: IncomingMessage, res: ServerResponse) => void;
}
*/


import { MockMethod } from 'vite-plugin-mock'
export default [
  {
    url: '/api/get',
    method: 'get',
    response: ({ query }) => {
      return {
        code: 0,
        data: {
          name: 'vben',
        },
      }
    },
  },
  {
    url: '/api/post',
    method: 'post',
    timeout: 2000,
    response: {
      code: 0,
      data: {
        name: 'vben',
      },
    },
  },
  {
    url: '/api/text',
    method: 'post',
    rawResponse: async (req, res) => {
      let reqbody = ''
      await new Promise((resolve) => {
        req.on('data', (chunk) => {
          reqbody += chunk
        })
        req.on('end', () => resolve(undefined))
      })
      res.setHeader('Content-Type', 'text/plain')
      res.statusCode = 200
      res.end(`hello, ${reqbody}`)
    },
  },
] as MockMethod[]
```



## vite配置跨域

**同源策略:** 只有协议，域名，端口号相同的请求才会被浏览器解析，否则会被浏览器拦截（数据事实上已经返回了）。

配置跨域

当我们请求例如 127.0.0.1：5732/api/user 时，此配置会在本地开启一个代理服务器，代理服务器协议，域名，端口号都与此请求相同，所以浏览器请求代理服务器没有同源策略。而这个代理服务器接收到请求后查看配置发现路径开头出现了配置的 '/api',所以代理服务器将请求的协议，域名，端口号替换为target 配置的内容。即请求变成了 http://www.baidu.com/api/user， 并且再次查看 rewrite 配置，将修改后的请求再次进行路径修改，最终将修改后的请求路径由代理服务器发给目标服务器（**服务器间没有同源策略**），拿到数据后代理服务器再返回给浏览器。

```js
// vite.config.js

import { defineConfig } from 'vite'

export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: 'http://www.baidu.com',
        changeOrigin: true, // 是否在请求目标服务器时将代理服务器的协议域名修改成与其一致
        rewrite: (path) => path.replace(/^\/api/, ''),
      }
    }
  }
})

```