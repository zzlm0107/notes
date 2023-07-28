---
title: webpack 笔记
icon: webpack
date: 2023-06-06
category:
  - 构建工具
tag:
  - webpack
---

## 什么是构建工具

> 浏览器他只认识html, css, js

企业级项目里都可能会具备哪些功能

1. typescript: 如果遇到ts文件我们需要使用tsc将typescript代码转换为js代码   
2. React/Vue: 安装react-compiler / vue-complier, 将我们写的jsx文件或者.vue文件转换为render函数
3. less/sass/postcss/component-style: 我们又需要安装less-loader, sass-loader等一系列编译工具
4. 语法降级: babel ---> 将es的新语法转换旧版浏览器可以接受的语法
5. 体积优化: uglifyjs ---> 将我们的代码进行压缩变成体积更小性能更高的文件
6. .....

稍微改一点点东西, 非常麻烦

将App.tsx ---> tsc ---> App.jsx ---> React-complier ---> js文件

希望有一个东西能够帮你把tsc, react-compiler, less, babel, uglifyjs全部集成到一起，这个东西就叫做**构建工具**



一个构建工具的功能:

1. 模块化开发支持: 支持直接从node_modules里引入代码 + 多种模块化支持
2. 处理代码兼容性: 比如babel语法降级, less,ts 语法转换(**不是构建工具做的, 构建工具将这些语法对应的处理工具集成进来自动化处理**)
3. 提高项目性能: 压缩文件, **代码分割**
4. 优化开发体验: 
    - 构建工具会帮你自动监听文件的变化, 当文件变化以后自动帮你调用对应的集成工具进行重新打包, 然后再浏览器重新运行（整个过程叫做热更新, hot replacement
    - 开发服务器: 跨域的问题, 用react-cli create-react-element vue-cli  解决跨域的问题, 



构建工具他让我们可以不用每次都关心我们的代码在浏览器如何运行, 我们只需要首次给构建工具提供一个配置文件(这个配置文件也不是必须的, 如果你不给他 他会有默认的帮你去处理), 有了这个集成的配置文件以后, 我们就可以在下次需要更新的时候调用一次对应的命令就好了, 如果我们再结合热更新, 我们就更加不需要管任何东西, 这就是构建工具去做的东西, **他让我们不用关心生产的代码也不用关心代码如何在浏览器运行, 只需要关心我们的开发怎么写的爽怎么写就好了**

```bash
# 构建工具按顺序依次编译执行命令
tsc xxx babel xxx less xxx
webpack
... 
```

市面上主流的构建工具有哪些:

- webpack
- vite
- parcel
- esbuild
- rollup
- grunt
- gulp

## webpack概述

**webpack官网** <https://www.webpackjs.com/>

来看webpack官网对自己的定义：

webpack 是一个用于现代 JavaScript 应用程序的 **静态模块打包工具**。当 webpack 处理应用程序时，它会在内部从一个或多个入口点构建一个 依赖图(dependency graph)，然后将你项目中所需的每一个模块组合成一个或多个 bundles，它们均为静态资源，用于展示你的内容。



**概念前提：**

- thunk：将...组合/结合,实际上就是指webpack的打包操作，一次chunk就是指一次完整的打包流程。
- bundle：包，块，实际上就是指webpack打包完成的产物，是chunk完成后所输出的东西。



## webpack简单使用

先理清 **webpack** 和 **webapck-cli** 的区别：

webpack 是核心编译包，具有webpack的所有功能并进行封装，包含了多个处理转换的函数并将其暴露了出来，但如果我们直接使用就需要分别一次次调用这些函数，整个项目操作下来十分繁琐。

webapck-cli 是 webpack 的脚手架，将 webpack 的多个功能函数的调用通过命令的形式组合起来，只需运行一次命令就可以打包完整个项目，大大减少使用者的心智负担。

使用时，先初始化项目,然后下载 webpack 和 webapck-cli：

```bash
yarn init -y

yarn add webpack webpack-cli -D
```

如果想要直接打包项目，运行 `npx webpack` 命令即可，开启一个node服务器（webpack运行在服务端，服务端才能读取文件），然后 webpack 会将我们的脚本 src/index.js 作为 入口起点，也会生成 dist/main.js 作为 输出。



## 入口 entry

**入口就是告诉webpack从哪个模块构建他的内部依赖图。** 默认是以 src/index.js 作为入口起点。

如果想要配置入口，可以在项目根目录下创建 `webpack.config.js` 配置：

```js
/**type (import('webpack",Configuration)} */

modu1e.exports = {
  mode: "development",
  entry: "/src/file/index.js"
}
```

入口有三种写法：
- 字符串写法：直接指明入口文件，即上述代码；
- 数组写法：将指明的多个文件合并到一个模块，相当于 import 引用，不常用
- 对象写法：可以配置入口的详情参数，较为重要

```js
/**type (import('webpack",Configuration)} */

modu1e.exports = {
  mode: "development",
  entry: {
    main: { // 入口的键 main 就是指代这次 chunk 流程
      filename: "target.js", // 输出的文件名
      import: "/src/file/index.js", // 入口文件
      
      // webpack 打包时在读取一个依赖后会将其放到缓存里，如果再次读取则直接使用缓存，减少开销
      // 不同的 chunk 流程 的缓存资源是分开的，但我们可以指明使用其他 chunk 流程 的缓存资源
      runtime: "mainRuntime", // 指明此次 chunk 运行环境，有就复用，没有就生成一个
      // dependOn: "" // 同样指明 chunk 运行环境，但环境必须存在，否则报错，与 runtime 配置只能存一
    }
  }
}
```



## 出口 output

output 属性告诉 webpack 在哪里输出它所创建的 bundle，以及如何命名这些文件。主要输出文件的默认值是 ./dist/main.js，其他生成文件默认放置在 ./dist 文件夹中。

当仅有一个入口文件配置时
```js
const path = require('path');

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js',
  },
}
```

当有多个入口文件配置时,即存在多个 chunk 流程则需要使用占位符来确保每个文件具有唯一的名称。
```js
module.exports = {
  entry: {
    app: './src/app.js',
    search: './src/search.js',
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/dist',
  },
};

// 写入到硬盘：./dist/app.js, ./dist/search.js
```



## 理解dependencies和devDependencies

简单理解：

- dependencies 里放置的都是生产依赖
- devDependencies 里放置的都是开发依赖



提问： 如果将package.json中的 dependencies 里的依赖包放到 devDependencies 里，还能正常打包构建吗？   
回答：是可以正常打包的，打完后的包也能正常运行



*这么看似乎一直以来上面的说法不对？*  
首先，我们要明白 package.json 是 npm 所需要的，是我们发包时必须具备的，我们发布的包如果有依赖，则必须放到 package.json 中的 dependencies 里。这样别人下载我们的包会一并下载依赖，确保我们的包是可用的。如果我们不发包，package.json 事实上是可有可无的。  
并且，打包构建是 webpack 的事情，它根本就不需要查看 package.json。

不过，以上的说法都是针对于**客户端环境**来说的，客户端开发一定是已经下载好了各种依赖包，打包工具再根据文件引入将依赖包一并打包。实际开发中还有**服务端渲染（ssr）**，服务端渲染会查看 package.json 中的 dependencies 下载生产依赖包，所以在这种服务端环境下必须正确放置，否则会报错。

所以说，实际上客户端开发是可以乱放的，但为了**规范化**，我们还是要正确放置对应的生产依赖和开发依赖。



## loader处理css文件

webpack 只能理解 JavaScript 和 JSON 文件，其他类型的文件是不认识的。**loader** 让 webpack 能够去处理其他类型的文件，并将它们转换为有效 模块，以供应用程序使用，以及被添加到依赖图中。

也就是说，loader 用于对模块的源代码进行转换。loader 可以使你在 `import` 或 "load(加载)" 模块时预处理文件。

```js
// webpack.config.js
module.exports = {
  module: {
    rules: [
      { test: /\.css$/, use: 'css-loader' }, // 直接指定对应loader
      {
        test: /\.css$/,
        use: [     // 指定多个loader时从后往前依次调用，处理结果交给上一个loader
          { loader: 'style-loader' }, // style-loader将css语法添加到创建的style标签中，并放到页面上
          {
            loader: 'css-loader', // css-loader将css语法处理为兼容性更好的css语法
            options: {
              modules: true
            }
          },
          { loader: 'sass-loader' } // sass-loader将sass语法处理为css语法
        ]
      }
    ],
  },
}
```

此外还有一些其他的loader

**mini-css-extract-plugin:** 将css-loader处理产生的结果输出为css文件，详细用法在 npm官网 搜索查看。

**file-loader：** 将 import/require 引入的文件（先经过其他loader处理后）在打包后转换成一个独立文件。



## css模块化

因为项目开发中多个页面我们会写出多个对应的css文件，webpack直接打包完成后会将这些css文件整合到一起，如果不同页面类名重复，则会导致样式混乱，所以在不同页面引入时需要开启css模块化。模块化开启后会给每个css文件添加一个hash，用于区分不同文件，相关用法参考 **vite笔记** 篇。

```js
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          { loader: 'css-loader', options: {modules: true} } // 添加配置项，开启模块化
        ]
      }
    ]
  },
}
```



