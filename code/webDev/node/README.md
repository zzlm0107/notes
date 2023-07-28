---
title: Node 笔记
icon: nodeJS
date: 2023-03-08
category:
  - Node
tag:
  - 后端
---

Node.js 是一个基于Chrome V8引擎的JavaScript运行环境，使用了一个事件驱动、非阻塞式I/O模型, 让JavaScript 运行在服务端的开发平台。是前端学习和理解后端的一个重要知识点。

**node中文网** <https://nodejs.cn/>

**菜鸟教程** <https://www.runoob.com/nodejs/nodejs-tutorial.html>

**值得注意的是，在node中文网首页官方还放置了多个其他热门库的中文网链接，并且他们都经过了中文汉化，挂载在了node的域名下（之前我学习的时候都还没有这些）；有部分热门库的官方网站都没有汉化完全，但这里提供的已经完全中文化，我不知道这是不是node官方自己做的热门库汉化工作（很大可能是），但这无疑为前端中文社区提供了巨大的帮助支持，让无数初学者免去了硬啃英文文档的烦恼，在此由衷的表达对node中文网维护人员的敬意。**



## 初识node

在官网下载安装后，就可以使用。

创建一个js文件，在里面写一些JavaScript代码，之后打开终端，定位到文件夹位置，输入命令`node 文件名` 即可运行此文件。



## 文件系统

引入node中的 **fs模块** 进行文件的读取和写入。此外还有打开文件，创建文件等各种操作。

```js
const fs = require('fs')

// 参数：文件位置 编码方式（可选） 回调函数
fs.readFile('./01.hello.js', 'utf8',function (err,res) {
  console.log(err);   //读取成功为null
  console.log(res);
})

// 参数：文件位置 写入内容 回调函数
fs.writeFile('./01.hello.js','11111',function (err,res) {
    //读取成功为null
    if (err !== null) {
      console.log(err); 
    } else {
      console.log('写入成功'); 
    }
})
```



此外我们可以使用 `__dirname` 获取到当前模块的目录名，再拼接上文件名就可以避免文件操作失败。

```js
const fs = require('fs')

fs.readFile( __dirname + '/01.hello.js', 'utf8',function (err,res) {
  console.log(err);   //读取成功为null
  console.log(res);
})
```



## 工具模块

Node.js 模块库中有很多好用的模块。包括OS模块，path模块，net模块等等

下面简单介绍一下用于**处理文件路径的path模块的几个方法**。

- `path.join(path1, path2[, ...])` 		路径拼接,使用此方法拼接路径而不要用 `+` 拼接
- `path.basename(p)`     获取路径中的文件名
- `path.extname(p)`      获取路径中的文件后缀名



## Web模块

Node.js 提供了 http 模块，http 模块主要用于搭建 HTTP 服务端和客户端。

```js
// 一个最简单的服务端
const http = require('http')

const server = http.createServer()
server.on('request',(req,res)=>{
  // console.log(req);
  //设置响应头，解决中文乱码的问题
  res.setHeader('Content-type','text/html;charset=utf-8')
  res.write('我是返回内容') // json格式
  res.end(); // 结束返回
})

server.listen(8000,()=>{
  console.log('服务器已经启动，8000端口监听中...');
})
```



## 模块系统

Node.js 是依据**CommonJS规范**，提供了 `module.exports` 和 `require` 两个对象，其中 exports 是模块公开的接口，require 用于从外部获取一个模块的接口。

此外，现在也可以使用ES6模块化的导入导出了，但是需要先在package.json中添加一个字段 `"type": "module"` 才可。并且需要注意路径后缀也需要。

```js
//引入模块的时候会自动执行一次模块内部的代码
const hello = require('./01.hello')
//模块内部的变量和方法是有作用域的，只有暴露出去才能访问到
console.log(hello.first);
```



## Express框架

Express 是一个简洁而灵活的 node.js Web应用框架, 提供了一系列强大特性帮助你创建各种 Web 应用，和丰富的 HTTP 工具。

使用时需要安装：`npm install express --save`



### 基本使用

```js
const express = require('express')
const app = express()

//get请求,获取query参数与响应
app.get('/info?id=xxx',(req,res)=>{
  console.log(req.query);
  res.send({
    name:'za',
    age: 18
  })
}) 
//get请求,动态获取params参数与响应
app.get('/user/:id',(req,res)=>{
  console.log(req.params);
  res.send({
    name:'za',
    age: 18
  })
})
app.post('/user',(req,res)=>{
  res.send('post请求成功')
})


app.listen(8000,()=>{
  console.log('express服务器启动，8000端口监听中...');
})
```



### 托管静态资源

使用 `express.static()` 方法可以托管静态资源，让请求直接访问获取相应资源

```js
const express = require('express')
const app = express()

//express.static方法托管静态资源（访问路径不需要带有./public）
app.use(express.static('./publicA'))

//挂载路径前缀，（访问路径需要带有/public）
app.use('/public',express.static('./publicB'))

app.listen(8000,()=>{
  console.log('express服务器已启动，8000端口监听中。。。');
})
```



### 模块化路由

事实上我们不应该将所有的路由都直接挂载到全局的app实例上。我们应该创建模块化路由再统一挂载。

```js
const express = require('express')

//创建路由对象
const router = express.Router()

//挂载具体的路由
router.get('/user/list',(req,res)=>{
  res.send('路由get请求')
})
router.post('/user/add',(req,res)=>{
  res.send('路由post请求')
})

//向外导出路由对象
module.exports = router
```

```js
const express = require('express')
const app = express()

//导入模块路由
const router = require('./09.express模块化路由')
//直接注册路由
// app.use(router)

//注册路由，添加路由前缀，访问路径需要带有/api
app.use('/api',router)

app.listen(8000,()=>{
  console.log('express服务器启动，8000端口监听中...');
})
```



### 中间件

中间件函数是可以访问请求对象 `（req）`，响应对象`（res）`以及应用程序的请求 - 响应周期中的下一个中间件函数的函数。中间件可以分为下面五种：

- **应用程序级中间件**
- **路由器级中间件**
- **错误处理中间件**
- **内置中间件**
- **第三方中间件**

```js
const express = require('express')
const app = express()
const router = express.Router()

//创建一个中间件函数
const mw =(req,res,next)=>{
  let time = Date.now()
  //上游中间件添加数据给下游使用
  req.startTime = time;
  console.log('中间件函数会有next函数进行放行');
  next();
}

//1应用级别的中间件（包括全局和局部中间件），中间件绑定到了app实例上
/* app.use((req,res,next)=>{
  console.log('应用级别的中间件');
  console.log('中间件函数会有next函数进行放行');
  next();
}) */

//2路由级别的中间件，中间件绑定到了router实例上
/* router.use((req,res,next)=>{
  console.log('路由级别的中间件');
  next();
}) */

//3错误级别的中间件,用于捕获项目错误
/* app.use((err,req,res,next)=>{
  console.log(err.message);
  res.send('错误级别的中间件,用于捕获项目错误')
}) */

//4内置的中间件
//express.static()中间件托管静态资源
// app.use(express.static('./public'))

//express.json()中间件解析post请求的json数据   application/json
// app.use(express.json())

//express.urlencoded()中间件解析post请求的键队数据   application/x-www-form-urlencoded
// app.use(express.urlencoded({extended: false}))


//5  第三方中间件
const parser = require('body-parser')
app.use(parser.urlencoded({extended: false}))


app.listen(8000,()=>{
  console.log('express服务器启动，8000端口监听中...');
})
```



### 连接数据库

express还可以用来连接数据库。

先安装一个mySql数据库： `npm install mysql`

```js
//导入mysql模块
const mysql = require('mysql')
//与数据库建立连接关系
const db = mysql.createPool({
  host: '127.0.0.1',      //数据库的ip地址
  user: 'root',
  password: 'admin123',
  database: 'my_db_01'    //指定要操作的数据库
})

//查询数据库数据,查询语句返回的是数组
db.query('select * from users',(err,res)=>{
  if (err !== null) {
    console.log(err.message);
  } else {
    console.log(res);
  }
})

//向数据库中插入数据
let user = {username: 'spiderman', password: '666666'}
//定义一条sql语句，?占位
let sqlStr = 'insert into users (username,password) values (?,?)'

db.query(sqlStr, [user.username,user.password], (err,res)=>{
  if (err !== null) {
    console.log(err.message);
  } 
  if (res.affectedRows === 1) {
    console.log('插入数据成功');
  }
})
//快捷插入数据（数据对象的每个属性与数据表一一对应）
let user = {username: 'spiderman-2', password: '888888'}
let sqlStr = 'insert into users set ?'
db.query(sqlStr, user, (err,res)=>{
  if (err !== null) {
    console.log(err.message);
  } 
  if (res.affectedRows === 1) {
    console.log('插入数据成功');
  }
})

//更新数据
let user = {id: 6, username: 'aaa', password: '789789'}
let sqlStr = 'update users set username=?, password=? where id=?'
db.query(sqlStr, [user.username, user.password, user.id], (err,res)=>{
  if (err !== null) {
    console.log(err.message);
  } 
  if (res.affectedRows === 1) {
    console.log('更新数据成功');
  }
})

//删除数据
let sqlStr = 'delete from users where id=?'
db.query(sqlStr, 6, (err,res)=>{
  if (err !== null) {
    console.log(err.message);
  } 
  if (res.affectedRows === 1) {
    console.log('删除数据成功');
  }
})
```



### 身份认证

目前常用的身份认证方式包括 **session认证** 和 **token认证** 两种。

Session是存放在服务器端的，可以保存在：内存、数据库、NoSQL中。若Session没有持久化落地存储，一旦服务器重启，Session数据会丢失。

```js
const express = require('express')
const app = express()

//配置session中间件,需要下载安装
const session = require('express-session')
app.use(
  session({
    secret: 'hello',
    resave: false,
    saveUninitialized: true
  })
)

app.use(express.urlencoded({extended:false}))

//登录，使用session中间件存储用户信息
app.post('/user/login',(req,res)=>{
  if (req.body.username !== 'admin' || req.body.password !== '111111') {
    return res.send({status: 1, msg: '登录失败'})
  }

  req.session.user = req.body;
  req.session.islogin = true;

  res.send({status: 0, msg: '登录成功'})
})

//获取用户名的接口
app.get('/user/username',(req,res)=>{
  if (!req.session.islogin) {
    return res.send({status: 1, msg: '没有登录'})
  }
  res.send({status: 0, msg: 'success', username: req.session.user.username})
})

//退出登录
app.post('/user/logout',(req,res)=>{
  //清空session信息
  req.session.destroy()
  res.send({status: 0, msg: '退出成功'})
})

app.listen(8000,()=>{
  console.log('express服务器启动，8000端口监听中...');
})
```



Token是放在客户端存储的，在分布式环境中应用广泛。

```js
const express = require('express')
const app = express()

const jwt = require('jsonwebtoken')   //将信息加密为token
const expressJWT = require('express-jwt')       //将token还原为json数据,这是一个中间件

const cors = require('cors')
app.use(cors())
app.use(express.urlencoded({extended:false}))

//定义一个secret密钥
const secretKey = 'hello'
//使用expressJWT中间件解析token  配置成功就会有req.user
app.use(expressJWT({secret: secretKey, algorithms:['HS256']}).unless({path: [ /^\/api\// ]}))

//登录，使用存储用户信息,不需要权限
app.post('/api/login',(req,res)=>{
  let userinfo = req.body;
  if (userinfo.username !== 'admin' || userinfo.password !== '111111') {
    return res.send({status: 400, msg: '登录失败'})
  }
  //生成token  三个参数  加密的信息对象，加密的密钥，配置对象
  let tokenStr = jwt.sign({username:userinfo.username},secretKey,{expiresIn: '60s'})
  res.send({code: 200, msg: '登录成功',token:tokenStr})
})

//使用全局错误处理中间件，捕获token过期错误
app.use((err,req,res,next)=>{
  if (err.name === 'UnauthorizedError') {
    res.send({
      code:401,
      msg: '无效的token'
    })
  } else {
    res.send({
      code:500,
      msg: '未知错误'
    })
  }
})

//获取用户名。需要权限,请求头里的token已经被expressJWT中间件解析
app.get('/user/username',(req,res)=>{
  res.send({status: 200, msg: 'success', username: req.user})
})

app.listen(8000,()=>{
  console.log('express服务器启动，8000端口监听中...');
})
```





## 常用spl语句

```sql
-- 检索全部的数据，用*通配符
select * from users

-- 检索对应的列 
select username, password from users

# 插入数据
insert into users (username,password) values ('老六','333444')

# 更新某一行某一列的数据
update users set password='888888' where id=2

# 更新某一行若干列的数据
update users set password='abcdef',status=1 where id=2

# 删除表中的某一行数据
delete from users where id=2

/* 使用where子句过滤数据 */
select * from users where username<>'老六'

/* 使用and和or连接条件 */
select * from users where username<>'老六' and id>=3

# 排序
select * from users order by password                          -- 默认升序排序
select * from users order by password desc                     -- desc 表示降序排序
select * from users order by password desc, username asc         -- 多重排序 

# 使用count(*)来统计数据
select count(*) from users where status=0

# 使用as 来给列起别名
select count(*) as total from users where status=0
```

