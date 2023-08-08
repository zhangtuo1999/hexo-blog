---
title: 【TypeScript】TypeScript笔记
tags: TypeScript
keywords: TypeScript
description: TypeScript笔记
excerpt: TypeScript笔记
abbrlink: 587aa8a1
date: 2022-11-21 21:55:05
---

JS、ES和TS的区别：ECMAScript 是 JavaScript 的标准，TypeScript 是 JavaScript 的超集。

安装node、typescript

```shell
npm install typescript -g
```

编译指令：tsc

观察ts文件变化：--watch

当ts代码有错误时，不将其编译成js代码：--noEmitOnError

生成编译文件：--init

基元类型：string、number、boolean

数组：type[] 或者Array\<type>

any类型

函数的参数列表中，形参后加上?表示可选参数。

union类型

类型别名 type

type 扩展类型：

```typescript
type Aniaml = {
    name: string
}

type Bear = Aniaml &{
    age: number
}

const bear: Bear = {
    name: 'zhangtuo',
    age: 18
}
```

类型创建以后不能更改

接口 interface

interface 扩展类型：

```typescript
interface Aniaml {
    name: string
}

interface Bear extends Aniaml{
    age: number
}

const bear: Bear = {
    name: 'zhangsan',
    age: 18
}
```

添加字段：

```
interface MyObj {
    x: number
}

interface MyObj {
    y: string
}

const myobj: MyObj = {
    x: 19,
    y: 'zhangtuo'
}
```

类型断言 as 或者\<>

文字类型

类型推断

null 和 undefined

!

枚举

bigint

symbol

类型缩小

类型保护

typeof 类型守卫

真值缩小

等值缩小

==null 可以识别null和undefined

in操作符类型缩小

分配缩小

控制流分析

类型谓词

union

never类型和穷尽性检查

函数类型表达式

类型别名

调用签名

构造签名

泛型函数
