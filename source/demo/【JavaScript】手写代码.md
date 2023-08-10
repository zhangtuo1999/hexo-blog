---
title: 【JavaScript】手写代码
tags: JavaScript
keywords: JavaScript
description: JavaScript 面试常见手写代码题
excerpt: JavaScript 面试常见手写代码题
abbrlink: edd6d5dc
date: 2023-01-23 08:47:26
---

# ES6 API

## 1. Function.prototype.call / apply / bind / softBind

* call 和 apply 功能相同，都是以指定的 this 值来调用函数。call 和 apply 的一个参数都是指定的 this 值。但是 call 和 apply 之后传参的方式不同：call 是将从第二个开始的参数一个个传给原函数；apply 是将第二个参数作为参数数组，拆分后传给原函数。
* bind 的传参方式和 call 相同，但是不会执行函数，而是返回一个新的函数。返回的新函数中的 this 就是传入的 this 值。
* softBind 和 bind 的区别在于：多次链式调用 bind 时，会按第一次调用 bind 时传入的 this 值执行；多次链式调用 softBind 时，会按照最后一次调用 softBind 时传入的 this 值执行。

### call

注：

* 传给 call 函数的 this 如果是 undefined 或者 null，this 应该指向全局 this 值。
* 传给 call 函数的 this 如果是原始值，this 应该使用 Object() 进行包装。
* 如果给 Object() 构造函数传 null 或 undefined，会返回一个空对象，否则根据原始值类型返回一个包装对象。如果传的是已存在的对象，则返回原对象。

```javascript
Function.prototype.myCall = function (ctx = globalThis) {
  // 1. 如果传入的形参 ctx 是 undefined 或者 null，那就 ctx 需要指向全局 this
  ctx ??= globalThis; // 保证 	ctx 不是 null 或 undefined

  // 2. 如果传入的 ctx 是原始值，那么需要被 Object() 包装成对象
  ctx = Object(ctx); // 保证 ctx 是对象

  // 3. 拿到传入的参数
  const args = [...arguments].slice(1);

  // 4. 把调用 myCall() 的函数保存到 ctx
  const key = Symbol("key");
  ctx[key] = this;

  // 5. 执行函数
  const res = ctx[key](...args);

  // 6. 删除绑定的 symbol
  delete ctx[key];

  // 7. 返回结果
  return res;
};
```

### apply

```javascript
Function.prototype.myApply = function (ctx) {
    ctx ??= globalThis
    ctx = Object(ctx)

    const args = arguments[1]

    const key = Symbol('key')
    ctx[key] = this
    const res = ctx[key](...args)
    delete ctx[key]

    return res
}
```

### bind

注：

* 用 new 调用 bind 返回的函数时，应该忽略传入的 this 值。

```javascript
Function.prototype.myBind = function (ctx) {
    ctx ??= globalThis
    ctx = Object(ctx)
    
    const args = [...arguments].slice(1)
    
    const self = this
    function bound() {
        self.call(new.target ? this : ctx, ...args)
    }

    bound.prototype = self.prototype
    return bound
}
```

### softBind

注：

* 只有调用 bound 函数的环境为全局环境时，传入的 this 值才生效。

```javascript
Function.prototype.softBind = function (ctx) {
    ctx ??= globalThis
    ctx = Object(ctx)

    const self = this
    const args = [...arguments].slice(1)

    function bound() {
        fn.call(new.target || this !== globalThis ? this : ctx, ...args)
    }

    bound.prototype = self.prototype
    return bound
}
```

## 2. Array.isArray()

* 判断传入值是否是数组，理论上 Array.isArray() 就是最优解。
* 如果不用 Array.isArray()，则使用原始对象的 toString()

```javascript
const isArray = list => ({}).toString.call(list) === 'Object Array'
```

## 3. Array.prototype.flat()

* 数组的 flat() 方法可以创建一个新数组，按指定深度递归将所有子数组元素拼接到新数组中。
* 默认递归深度为1。

```javascript
Array.prototype.myFlat = function (depth = 1) {
    if (depth === 0) return this
    return this.reduce((a, b) => a.concat(Array.isArray(b) ? b.myFlat(depth - 1) : b), [])
}
```

## 4. Array.prototype.reduce()

```javascript
Array.prototype.myReduce = function (fn, init) {
  // 空数组直接返回init（这里init可能是undefined，没关系）
  if (this.length === 0) return init;
  // 判断传没传init参数。如果没传，就拿数组第一项当init。
  let next = init ?? this[0];
  // 没传init的情况下，少循环一次
  for (let i = next === init ? 0 : 1; i < this.length; i++) {
    next = fn(next, this[i]);
  }
  return next;
};
```



## 5. Promise.all()

* 传入一个数组，数组中 Promise 对象全部 resolve 了，Promse.all() 才 resolve。数组中只要有一个 Pormise 对象 reject 了，那么 Promise.all() 就 reject。
* Promise.all() resolve 的值是传入数组中对应位置 Promise 对象的值。
* Promise.all() reject 的值是传入数组中 Promise 对象第一个 reject 的值。

```javascript
function all(promises) {
  return new Promise((resolve, reject) => {
    const res = [];
    let count = 0;
    for (let i = 0; i < promises.length; i++) {
      // 数组的每个元素都需要转成Promise对象
      Promise.resolve(promises[i])
        .then((value) => {
          res[i] = value;

          // 所有Promise对象都解决了才解决
          if (++count === promises.length) {
            resolve(res);
          }
        })
        .catch(reject);
    }
  });
}
```



# lodash API

## 1. sleep / delay

* sleep(t)：等待指定时间
* delay(func, t, ...args)：等待指定时间后执行函数

### sleep

```javascript
const sleep = (time) => new Promise(resolve => setTimeout(resolve, time))
```

### delay

```javascript
function delay(func, time, ...args) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            Promise.resolve(func(...args)).then(resolve).catch(reject)
        }, time)
    })
}
```

## 2. debounce / throttle

* 防抖（debounce）：在一定的时间间隔内，多次触发的事件合并为一次执行。（回城）

  实现思路：每次事件被触发时，设置一个计时器，在指定的时间间隔内，如果该事件被再次触发，则清除计时器并重新开始计时，直到指定的时间间隔内没有事件触发为止，然后调用函数。

  ```javascript
  (function debounce(fn, wait) {
    let timer;
    return (...args) => {
      clearTimeout(timer);
      timer = setTimeout(() => {
        fn(...args);
      }, wait);
    };
  }
  ```

* 节流（throttle）：节流是指在一定的时间间隔内，函数只被调用一次。如果在这个时间间隔内触发了多次事件，只有第一次会调用函数，其余的会被忽略。（技能CD）

  实现思路：每次事件被触发时，如果函数没有在指定的时间间隔内被调用过，则调用函数并设置一个计时器，在指定的时间间隔内不再触发事件。如果在指定的时间间隔内再次触发了事件，则不调用函数，直到指定的时间间隔过去，重新开始调用函数。

  ```javascript
  function throttle (fn, wait) {
    let timer
    return (...args) => {
      if (timer) { return }
      timer = setTimeout(() => {
        fn(...args)
        timer = null
      }, wait)
    }
  }
  ```


## 3. cloneDeep

```javascript
function cloneDeep(target, map = new WeakMap()) {
    if (typeof target !== 'object') return target
    if (target === null) return target

    if (target instanceof Date) return target
    if (target instanceof RegExp) return target

    if (map.has(target)) return map.get(target)

    const cloneTarget = new target.constructor()

    Reflect.ownKeys(target).forEach(key => {
        cloneTarget[key] = cloneDeep(target[key], map)
    })

    map.set(target, cloneTarget)

    return cloneTarget
}
```





 
