## vue2项目启动

### vue/cli创建

（已经安装就不在需要此步骤，通过  **vue --version**  查看）

```
npm i -g @vue/cli
```

然后在cmd命令行窗口中合适的文件夹下新建项目。

```
vue create <项目名称（不能含有大写英文，中文）>
```

### 配置项

初次建立项目时，可以选择自己定义配置项，通过勾选  **Manually select features** 开启手动选择。个人建议配置如下：

```
(*) Babel
( ) TypeScript
( ) Progressive Web App (PWA) Support
(*) Router
(*) Vuex
(*) CSS Pre-processos   //开启css预处理器
(*) Linter/Formatter    //开启eslint代码校验
( ) Unit Testing
( ) E2E Testing
```

选择vue2还是vue3项目；

是否选择history路由模式，推荐 否，选择hash模式更简单；

选择css预处理器，按个人需求，个人喜欢less；

选择代码校验风格，推荐ESLint + Standard config  标准代码风格；

选择校验时机，建议两个都选；

选择各种工具的配置替代，选第一个（依赖插件或者工具的配置文件分文件保存）；

选择是否记录以上操作，选择 否。



## ESLint代码校验

**前言：**EsLint 可以帮助规范代码，团队协作时很有必要，十分建议开启，慢慢改善自己的编程陋习。

### 关闭校验

在项目的**vue.config.js**文件里的 **module.exports** 配置选项里可以配置关闭ESLint校验。

```
lintOnSave：false
```

### module模块报错

**解决办法：**在 .eslintrc.js 文件添加一行配置项取消对config文件的检查：

```javascript
  parserOptions: {
    parser: '@babel/eslint-parser',
    requireConfigFile: false              //取消必须config配置文件
  },
```

安装并配置完成 ESLint 后，我们继续回到 VSCode 进行扩展设置，依次点击 文件 > 首选项 > 设置 打开 VSCode 配置文件,添加如下配置

```json
"editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
}
```

这样每次保存的时候就可以根据根目录下.eslintrc.js你配置的eslint规则来检查和做一些简单的fix。



## Less文件全局导入

**前言：**在使用less时，我们有时候会抽提一些公共的变量或者样式到一个全局less文件中，需要使用时就可以使用**@import  ' 文件路径 '** 来单独导入，但当多次使用时每个.vue文件都引入就会很麻烦。

我们可以在vue项目的 **src/style/variables.less**  放置全局公共样式。

在当前项目下执行一下命令 **vue add style-resources-loader** ，选择less为预处理器，添加一个vuecli的插件。

安装完毕后会在vue.config.js中自动添加配置：

```javascript
const path = require('path')

module.exports = {
  pluginOptions: {
    'style-resources-loader': {
      preProcessor: 'less',
      patterns: [
        // 需要全局自动导入的文件的路径
        path.join(__dirname, './src/assets/styles/variables.less'),
        path.join(__dirname, './src/assets/styles/mixins.less')
      ]
    }
  }
}
```



## 路径别名

**前言：**这是为了当我们使用 **路径别名@** 的时候可以提示路径，比较方便。

我们只需在 **vue.config.js** 配置 alias 即可

```js
module.exports = {
    //修改文件路径
    //publicPath:process.env.NODE_ENV === 'production'?'./':'/',
     publicPath: './',
    //声明配置webpack
    configureWebpack: {
      resolve: {
        //配置路径别名
        alias: {
          '@': './src',
        }
      }
    }
  }
```



在  **jsconfig.json**  文件中添加下面配置即可：

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
    }
  },
  "exclude": ["node_modules", "dist"]
}
```



## proxy跨域

**前言：**跨域问题是我们经常会碰到的一个问题，这主要是浏览器的同源政策限制的，同源政策要求必须**协议，域名，端口号**三者都相同才能正常访问，（服务器与服务器的访问不受限制）但往往前端和后端并不会如此，所以我们必须解决跨域问题。跨域问题可以通过后端配置解决，也可以在前端解决。

在 vue.config.js 中我们可以通过在本地开启一个代理服务器实现跨域：

```javascript
module.exports = {
  //开启代理服务器
  devServer: {
    proxy: {
      '/api': {
        //当匹配到端口号后面出现/api时，即走代理服务器请求target地址
        target: ' https://mock.presstime.cn/mock/62bd428e0a8ecd0022c95c5d/api ',
        pathRewrite: { '^/api': '' },    // 可以将匹配到的端口号后面的/api替换为空
        ws:true,    //用于支持websocket
        changeOrigin:true  //是否在请求时修改代理服务器的host地址与target地址一致
      }
    }
  }
}
```



## 封装Axios

**前言：**开始项目时常常需要封装一个Axios实例用来发送api请求。

```javascript
// 对于axios进行二次封装
import axios from 'axios'
// 引入进度条
import nprogress from 'nprogress'
// 引入进度条样式
import 'nprogress/nprogress.css'


const localhost = 'http://127.0.0.1:8080/api';

// 利用axios对象的方法create，创建一个axios实例
// 进行简单配置
const instance = axios.create({
    // 基础路径
    baseURL: localhost,
    // 设置请求超时时间
    timeout:5000,
})

// 请求拦截器:在发送请求之前，请求拦截器可以检测到并在请求发送出去之前做一些处理
instance.interceptors.request.use((config)=>{
    // 进度条开始启动
    nprogress.start();

    // ...如添加请求头携带token等一些逻辑
    
    return config;
})

// 响应拦截器
instance.interceptors.response.use((res)=>{
    // 成功的回调函数，服务器相应数据返回时，响应拦截器可以检测到并做一些处理
    
    // 进度条结束
    nprogress.done();
    return res.data;
},(error)=>{
    // 失败的回调函数
    console.log('axios响应失败'+error);
    return Promise.reject(new Error('请求未能成功'));
})

// 对外暴露instance实例
export default instance;
```



## 路由重复点击报错

**解决方法：**在引入使用VueRouter的 js文件中重写路由跳转方法。

```javascript
// 重写push和replace方法，解决路由重复产生的报错
const originalPush = VueRouter.prototype.push
const originalReplace = VueRouter.prototype.replace

VueRouter.prototype.push = function push(location) {
  return originalPush.call(this, location).catch(err => err)
}
VueRouter.prototype.replace = function replace(location) {
  return originalReplace.call(this, location).catch(err => err)
}

// 配置路由
const router = new VueRouter({
    routes,
    //滚动行为，控制详情页跳转时的滚轮所处位置
    scrollBehavior() {
      // 始终滚动到顶部，vue2
      // vue3的写法为 return { left:0, top:0 }
      return { y: 0 }
    },
})
export default router;
```



## 局域网访问

**前言：**在前端开发时有时候需要将本地项目的开发进度或页面分享给boss或是后端查看，所以就需要配置将本地项目可以在局域网下打开访问。

vue项目启动完成时在终端上会出现两个地址，第一个是本地访问地址，而第二个就是局域网访问地址。

```
- Local:   http://localhost:8080/    
- Network: http://192.168.2.218:8080/
```

但我们在同一局域网另一台电脑或手机直接访问第二个地址是无法访问成功的，探究原因发现是需要在本地电脑上取消防火墙，配置开放项目运行时端口号对应的入站和出站规则（两者配置方式基本一样）：

***控制面板 ---> Windows Defender防火墙 ---> 高级设置 ---> 入站/出站规则 ---> 新建规则 ---> 选择端口 ---> 选择TCP ---> 输入开放的端口号（即项目运行端口号） ---> 允许连接 ---> 选择开放场景（可以全选） ---> 输入规则名称和描述 ---> 完成***

配置完成后重启本地项目，之后在局域网下其他电脑手机发现也可以访问到了。

（网上的一些在vue.config.js里的dev配置项里配置host为0.0.0.0/本机IP地址或package.json中修改serve脚本加上 --host=本地IP地址本人测试无效，可能是有其他原因没有成功。）
