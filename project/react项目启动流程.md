## react项目启动

```bash
yarn create vite my-react-app --template react-ts
```

之后再进入项目文件夹安装依赖即可。**下面的各种流程依赖包按需安装。**





## 配置环境变量

争对不同的开发环境，我们需要准备不同的环境变量，这样当我们运行不同环境启动或打包指令时，就能自动替换环境变量。具体可以查看 **vite笔记中的环境变量相关内容**

在项目根目录下创建下面文件：

- **.env** 文件（可选），全局的环境变量文件，所有环境都会使用到
- **.env.production** 文件，生产环境文件，在打包时会使用此文件里的环境变量
- **.env.development** 文件，开发环境文件，在本地开发时会使用此文件里的环境变量

注意，环境变量文件里的变量必须以 `VITE_` 开头，在项目中读取时通过 `import.meta.env.xxx` 获取。

```
## 开发环境

VITE_APP_TITLE = '后台管理系统（开发）'
VITE_APP_PORT = 3000
VITE_APP_BASE_API = '/dev-api'
```

如果我们想增加一些其他环境文件如 `.env.test` ,那么我们就需要在 `package.json` 中添加新的运行指令,通过 `--mode xxx` 指定运行环境，这样我们运行指令 `yarn dev:test` 就会读取 `.env.test` 环境文件了。

```json
"scripts": {
    "dev": "vite", // 默认读取.env.development
    "dev:test": "vite --mode test",
    "build": "vue-tsc && vite build", // 默认读取.env.production
    "preview": "vite preview"
  },
```





## 配置路径别名

我们可以在 `vite.config.ts` 中实现将 src路径 映射为 `@` ,方便后续文件引入。

如果找不到path模块，则需要安装开发依赖 `@types/node` 包

```ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import path from 'path'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  // 设置路径别名
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src')
    }
  }
})
```

此时就已经可以将src替换为@了，但如果我们还想要路径智能提示，则还需要再 `tsconfig.json` 中添加如下配置：

```json
 "compilerOptions": {
   ...
   "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    },
    ...
 }
```







## css处理

vite生来就支持 less 和 sass 作为css预处理器，所以只需要安装自己习惯的处理器即可

```bash
yarn add sass -D
```

如果想向全局注入一些公共样式文件，则需要在 `vite.config.ts` 中进行配置

```ts
css: {
  // CSS 预处理器
  preprocessorOptions: {
    //define global scss variable
    scss: {
      additionalData: '@import "src/styles/globalVar.scss";@import "src/styles/globalMixin.scss";'
    }
  }
},
```

此外由于各种浏览器的默认样式，我们还需要进行清除：

```ts
yarn add normalize.css

// main.ts
import 'normalize.css'
```



由于react并没有提供类似vue的`scoped`方式避免样式的全局污染，所以当我们在react项目中导入外部样式时，一种常见的做法是利用**vite的css模块化功能**。

`xxx.module.css` ，不再直接引入，而是import模块化引入，使用时相当于对象的形式，样式类名变成了引入css模块的属性.





## react-router-dom

在react项目中使用 `react-router-dom` 进行路由管理。

```bash
yarn add react-router-dom
```

