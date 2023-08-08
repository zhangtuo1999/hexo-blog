---
title: 【JavaScript】Promise-1-基础用法
tags:
  - JavaScript
  - 前端
  - Promise
excerpt: Promise；async；await
abbrlink: f28bcdc5
date: 2021-11-15 15:10:14
index_img: ../images/封面图/js.jpg
---

# 第一章 Promise 的理解和使用

## 1.1 Promise 是什么

### 1.1.1 理解

* Promise 是 JavaScript 中进行异步编程的新的解决方案。旧的方案是单纯使用回调函数。
* 从语法上讲，Promise 是一个构造函数。
* 从功能上讲，Promise 对象用来封装一个异步操作并获取其成功/失败的值。

### 1.1.2 异步编程

* fs 文件操作（node.js）
* 数据库操作
* AJAX
* 定时器

## 1.2 为什么使用 Promise

### 1.2.1 指定回调函数的方式更加灵活

* 旧的方案：必须在启动异步任务前指定。
* Promise：启动异步任务 => 返回 Promise 对象 => 给 Promise 对象绑定回调函数（甚至可以在异步任务结束后指定）

### 1.2.2 支持链式调用，解决回调地狱的问题

* 什么是回调地狱：回调函数嵌套回调函数
* 回调地狱的缺点：不便于阅读，不便于异常处理 

# 第二章 了解 Promise

## 2.1 Promise 的状态

1. pending 未决定的

2. resolved / fullfilled 成功

3. rejected 失败

* 实例对象中的一个属性：[[PromiseState]]

* 状态只能由 pending 转变成 resolved 或者由 pending 转变成 rejected，且每个 Promise 对象只能改变一次
* 成功的结果一般叫 value，失败的结果一般叫 reason

## 2.2 Promise 的值

* 实例对象中的另一个属性：[[PromiseResult]]
* 保存着异步任务对象成功或者失败的结果

## 2.3 Promise 的基本流程

1. 实例化一个 Promise 对象
2. 成功就调用 resolve() 函数

3. 失败就调用 reject() 函数

## 2.4 如何使用 Promise 对象

### 2.4.1 Promise() 构造函数

Promise 构造函数：`Promise(executor){}`
* executor：执行器函数 `(resolve, reject)=>{}`
* resolve() 函数：内部定义成功时调用的函数
* reject() 函数：内部定义失败时调用的函数

```javascript
const p = new Promise((resolve, reject) => {
  // 同步代码 ...
  if (true) {
    // 成功调用
    resolve("foo");
  } else {
    // 失败调用
    reject("bar");
  }
});
```

> **注意**：执行器函数是**同步调用**的，立即执行。

### 2.4.2 Promise.prototype.then() 方法

Promise.prototype.then() 方法接收两个函数参数：onResolved 和 onRejected。
* onResolved() 函数：成功的回调函数 `(value)=>{}`
* onRejected() 函数：失败的回调函数 `(reason)=>{}`
* 返回值是一个新的 Promise 对象

```javascript
p.then(
  (value) => {
    console.log(value);
  },
  (reason) => {
    console.log(reason);
  }
);
```

### 2.4.3 Promise.prototype.catch() 方法

Promise.prototype.catch() 方法只能接收失败的回调，不能接收成功的回调。只接受一个函数参数 onRejected。
* onRejected() 函数：失败的回调函数 `(reason)=>{}`

```javascript
p.catch((reason) => {
  console.log(reason);
});
```

### 2.4.4 Promise.resolve() 方法

Promise.resolve() 方法可以将任意值（参数 value）封装成一个的 Promise 对象（如果传入的 value 是一个 Promise 对象，那么便保留状态，因此创建出来的 Promise 对象不一定是成功状态）。

* value：成功的数据或一个 Promise 对象。
* 返回的 Promise 对象可以是成功状态也可以失败状态（传入失败状态的 Promise 对象或抛出错误时返回失败状态的对象，其余情况均返回成功状态的对象）。
* 用于快速创建一个 Promise 对象。

```javascript
// 传入一个数据，得到封装该数据的成功状态的 Promise 对象
const p1 = Promise.resolve("foo");
setTimeout(console.log, 0, p1);			// Promise {<fulfilled>: 'foo'}
```

```javascript
// 传入一个 Promise 对象，返回的 Promise 对象会保留其状态
const p2 = Promise.resolve(p1);
setTimeout(console.log, 0, p2);			// Promise {<fulfilled>: 'foo'}
```

```javascript
const p3 = Promise.resolve(Promise.reject("bar"));
setTimeout(console.log, 0, p3);			// Promise {<rejected>: 'bar'}
// Uncaught (in promise) bar
```

### 2.4.5 Promise.reject() 方法

Promise.reject() 方法可以将任意值（参数 reason）封装成一个拒绝状态的 Promise 对象。

* reason：失败的数据或一个 Promise 对象。
* 返回的是失败状态的 Promise 对象。

```javascript
// 传入一个数据，返回一个失败的 Promise 对象
const p1 = Promise.reject("foo");
setTimeout(console.log, 0, p1);		// Promise {<rejected>: 'foo'}
// Uncaught (in promise) foo
```

```javascript
// 传入一个 成功状态的 Promise 对象
const p2 = Promise.reject(Promise.resolve("bar"));
setTimeout(console.log, 0, p2);		// Promise {<rejected>: Promise}
// Uncaught (in promise) Promise {<fulfilled>: 'bar'}
```

### 2.4.6 Promise.all() 方法

Promise.all() 方法 ：

* 接收包含 n 个 Promise 对象的数组。
* 返回的是一个 Promise 对象，只有参数中所有的 Promise 都成功才成功，只要由一个失败了就算失败。
* 成功的结果是每个 Promise 对象的结果的数组，失败的结果是第一个失败的那个 Promise 对象的失败结果。

### 2.4.7 Promise.race()  方法

Promise.race()  方法：

* 接收包含 n 个 Promise 对象的数组。

* 返回的是一个 Promise 对象，第一个完成的 Promise 的结果状态就是最终的结果状态。（赛跑，谁先改变状态，最后的状态就是谁）

## 2.5 几个关键问题

1. 如何改变 Promise 对象的状态
   * 调用 resolve() 函数，pending 转为 fullfilled 或 resolved
   * 调用 reject() 函数，pending 转为 rejected
   * throw 抛出错误，pending 转为 rejected
2. 一个 Promise 对象指定多个成功/失败回调函数，都会调用吗？
   * 都会
4. Promise.prototype.then() 返回的新 Promise 对象的结果状态由什么来决定
   * 由指定的回调函数的执行结果来决定的。
   * 如果抛出异常，新的 Promise 对象变为 rejected，reason 为抛出的异常。
   * 如果返回的是非 Promise 对象的值，新的 Promise 对象变成 resolved，value 为返回的值。
   * 如果返回的是另一个新的 Promise 对象。此 Promise 对象的结果就成为新的 Promise 对象的结果。
5. Promise 异常穿透
   * 当使用 Promise 的 then 链式调用时，可以再最后指定失败的回调
6. 中断 Promise 链
   * 有且只有一种方式：返回一个 pending 状态的 Promise 对象

# 第三章 async 和 await

## 3.1 async 函数

* 函数的返回值为 Promise 对象

* Promise 对象的结果由 async 函数执行的返回值决定

  1. 如果函数的返回值是一个非 Promise 类型的数据，返回的就是成功类型的对象，并且成功的结果就是返回值

  2. 如果函数返回的是一个 Promise 类型的数据，这个 Promise 对象的结果就是 async 返回的结果
  3. 如果抛出异常，返回的结果是失败状态的，并且原因是 throw 抛出来的值

## 3.2 await 表达式

* await 右侧的表达式一般为 Promise 对象，也可以是其他类型的值
* 如果表达式是 Promise 对象，await 返回的是 Promise 对象成功的值
* 如果表达式是其他值，直接讲此值作为 await 的返回值

## 3.3 注意

1. await 必须写在 async 函数中，但是 async 函数中可以没有 await
2. 如果 await 的 Promise 失败了，就会抛出异常，需要用 try...catch 捕获异常
