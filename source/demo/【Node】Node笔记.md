---
title: 【Node】Node笔记
tags: Node
keywords: Node
description: Node笔记
excerpt: Node笔记
abbrlink: a08e589f
date: 2022-11-24 15:10:59
---

CommonJS规范：

单独的js文件作为一个模块，默认情况下这个模块中的属性和方法外部是无法访问的。如果想要让外部可以访问模块中的属性和方法，就必须在模块中通过module.exports暴露属性和方法。

NPM：

初始化：

```
npm init
```

安装包：

```
npm install <包名>
```

```
npm install <包名> --save
npm install <包名> -S
```

包名@版本号：指定版本号安装，升级：update， 卸载：uninstall

加不加 --save / -S 效果一样。

```
npm install <包名> --save-dev
npm install <包名> -D
```

加上 --save-dev 之后，安装的包只用于开发环境，不会用于生产环境。

`^2.3.0` 表示大版本号锁定，后面最新的。

`~2.3.0` 表示前两个版本号锁定，后面最近的。

`*` 表示最新的版本。

全局安装：
```
npm install -g
```

Yarn：

初始化：

```
yarn init
```

安装包：

```
yarn add <包名>
```

包名@版本号：指定版本号安装，升级：upgrade，卸载：remove

ESM：

在package.json中添加一个字段："type":"module"

ESM 和 commonjs 不能混着写

内置模块：

http 模块

```javascript
const http = require('http')

// 创建服务器
http.createServer((req, res)=>{
    // req 请求参数
    // res 响应内容
    const html = `<html>
    <b>hello world</b>
</html>`
    res.writeHead(200, {"Content-Type": "text/html"})
    res.write(html)
    res.end()
}).listen(3000, ()=>{
    console.log('server start')
})
```

url模块

1. parse （旧版）将url解析成信息
2. format（旧版） 将信息格式化成url

3. resolve（旧版） 字符串拼接替换


新版url模块

queryString模块

parse

stringfiy

escape/unescape 转义

服务器自动重启

npm install -g nodemon

nodemon <文件名>

jsonp/cdn









