---
title: 【JavaScript】运行机制
tags:
  - JavaScript
  - Node
keywords:
  - JavaScript
  - Node
description: 单线程、事件循环、宏任务和微任务、Promise、async
excerpt: 单线程、事件循环、宏任务和微任务、Promise、async
abbrlink: ca3aff80
date: 2023-01-21 18:43:16
index_img: ../images/封面图/js.jpg
---

## 1 同步和异步

同步：按顺序执行代码，上一代码行完成后再执行下一行。

异步：运行时间长的操作在执行时，不影响其他同步代码。操作完成时通知运行结果。

JavaScript 中的异步操作：

* 定时器

* 发送请求
* 读写文件
* Promise 实例的 then() 方法
* ...

**执行顺序**：

1. 同步代码
2. 异步代码

举例：

```javascript
// 1 5 2 3 4
console.log(1)
setTimeout(() => { console.log(2) }, 0)
setTimeout(() => { console.log(3) }, 0)
setTimeout(() => { console.log(4) }, 0)
console.log(5)
```

## 2 单线程

JavaScript 是单线程的，只能等一个任务执行完毕后执行下一个任务。

举例：

```javascript
// 1000个1 5 234
for (let i = 0; i < 1000; i++) {
    console.log(1)
}
setTimeout(() => { console.log(2) }, 0)
setTimeout(() => { console.log(3) }, 0)
setTimeout(() => { console.log(4) }, 0)
console.log(5)
```

定时器只负责在指定的时间将任务插入任务队列中，不关心任务的具体执行时间。无论定时器设置的时间是多少，都会让同步的代码先执行完。所以有时会有定时器没有按指定的时间运行的感觉，因为需要等同步代码和任务队列中前面的代码执行完毕。

## 3 process.nextTick() 和 setImmediate()

注：process.nextTick() 只能在 Node 中运行。

process.nextTick() 在同步代码完成之后，异步代码完成之前执行。setImmediate() 在异步代码完成之后执行。

**执行顺序**：

1. 同步代码
2. process.nextTick()
3. 异步代码
4. setImmediate()

举例：

```javascript
// 1 3 2 4
console.log(1)

setTimeout(() => { console.log(2) }, 0)

process.nextTick(()=>{
    console.log(3)
})

setImmediate(()=>{
    console.log(4)
})
```

## 4 事件循环

同步代码存在运行栈中，异步代码存在任务队列中。事件循环不停地检测任务队列中的是否有任务，如果有就按顺序执行。

**执行顺序**：

* 同步代码
* process.nextTick()
* 异步代码
* 当前时间循环结束后执行 setImmediate()

举例：

```javascript
// 1 5 2 4 6 3
console.log(1)

setTimeout(() => { console.log(2) }, 0)
setTimeout(() => { console.log(3) }, 1000)
setTimeout(() => { console.log(4) }, 0)

process.nextTick(()=>{
    console.log(5)
})

setImmediate(()=>{
    console.log(6)
})
```

## 5 宏任务和微任务

宏任务：计时器、发送请求、读取文件

微任务：Promise 实例的 then() 方法

先执行微任务再执行宏任务。

**执行顺序**：

1. 同步代码
2. process.nextTick()
3. 微任务
4. 宏任务
5. 当前时间循环结束后执行 setImmediate()

举例：

```javascript
// 1 7 5 8 2 4 6 3
console.log(1)

setTimeout(() => { console.log(2) }, 0)
setTimeout(() => { console.log(3) }, 1000)
setTimeout(() => { console.log(4) }, 0)

process.nextTick(()=>{
    console.log(5)
})

setImmediate(()=>{
    console.log(6)
})

new Promise((resolve)=>{
    console.log(7)
    resolve()
}).then(()=>{
    console.log(8)
})
```

## 6 Promise 和 async/await

构造 Promise 实例时传入的执行器函数是同步代码。Promise 实例的 then() 方法是微任务。

async 能把函数的返回值用 Promise.resolve() 包装成一个 Promise 实例：

```javascript
async function fn() {
    return 'foo'
}

fn().then(res => { console.log(res) })  // foo
```

await 关键字会等待表达式返回 Promise 对象，并把 Promise 对象进行解包。

```javascript
async function fn() {
    return 'foo'  
}

async function test() {
    let res = await fn()
    console.log(res)
}

test()	// foo
```

await 后面的代码可以看作是 Pormise 实例 then() 方法中的代码。

举例：

```javascript
// 1 3 5 7 2 6 4
console.log(1)

async function fn1() {
    await fn2()
    console.log(2)
}

async function fn2() {
    console.log(3)
}

fn1()

setTimeout(() => { console.log(4) }, 0)

new Promise(resolve => {
    console.log(5)
    resolve()
}).then(() => {
    console.log(6)
})

console.log(7)
```

