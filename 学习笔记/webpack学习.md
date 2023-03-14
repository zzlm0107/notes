# webpack 笔记



## webpack概述

在没有使用打包工具前，我们可能会直接使用 *script标签* 引用各种包，但这可能会导致一些问题：

- 无法直接体现，脚本的执行依赖于外部库。
- 如果依赖不存在，或者引入顺序错误，应用程序将无法正常运行。
- 如果依赖被引入但是并没有使用，浏览器将被迫下载无用代码。



那么，我们就需要了解什么是打包/构建工具，因为浏览器只认识html，css ，js文件，而我们实际开发中所写的文件格式类型多种多样，所以就需要一个工具来将我们写的多种格式的代码进行转换，当然可能帮助我们管理引入的各种依赖包。而这就是打包工具的其中几个功能，具体可查看我的 **vite笔记** 篇。



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



