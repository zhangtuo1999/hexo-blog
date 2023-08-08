---
title: 【JavaScript红宝书】18.迭代器和生成器
tags:
  - 前端
  - JavaScript
keywords: JavaScript
description: 《JavaScript高级程序设计（第4版）》第7章：迭代器和生成器。
excerpt: 《JavaScript高级程序设计（第4版）》第7章：迭代器和生成器。
abbrlink: 88df9c3f
date: 2022-05-14 14:20:36
index_img: ../images/封面图/红宝书.png
---

# 1 迭代器

**可迭代对象**实现了 Iterable 接口（可迭代协议），可以通过**迭代器**来操作。这种对象类似数组或者集合，包含的有限的元素，并且有无歧义的遍历顺序。不过，可迭代对象不一定都是集合对象，也可以是仅仅具有类似行为的其它数据结构。

任何实现 Iterable 接口（可迭代协议）的数据结构都可以被实现 Iterator 接口（迭代器协议）的结构“消费”。**迭代器**是按需创建的一次性对象。每个迭代器都**关联**一个可迭代对象，而迭代器会暴露其关联可迭代对象的 API。迭代器不需要知道与其关联的可迭代对象的结构，只需要知道如何取得连续的值。

## 1.1 可迭代协议

实现 Iterator 接口（可迭代协议）要求同时具备支持迭代的自我识别能力和和创建实现 Iterator 接口的对象的能力。在 ECMAScript 中，这意味着必须暴露一个 Symbol.iterator 属性作为“默认迭代器”。这个默认迭代器属性必须引用一个迭代器工厂函数，调用这个工厂函数必须返回一个新迭代器。

很多内置类型都实现了 Iterator 接口。包括字符串、数组、Map、Set、arguments 对象。

```javascript
// 没有实现迭代器工厂函数的类型
const num = 1;
const obj = {};
console.log(num[Symbol.iterator]);	// undefined
console.log(obj[Symbol.iterator]);	// undefined
```

```javascript
// 实现了迭代器工厂函数的类型
const arr = [1, 2, 3];
const set = new Set([1, 2, 3]);
console.log(arr[Symbol.iterator]);	// [Function: values]
console.log(set[Symbol.iterator]);	// [Function: values]
```

调用迭代器工厂函数会返回一个迭代器。

```javascript
console.log(arr[Symbol.iterator]());	// Object [Array Iterator] {}
console.log(set[Symbol.iterator]());	// [Set Iterator] { 1, 2, 3 }
```

实际写代码过程中，不需要**显式调用**这个工厂函数来生成迭代器。实现可迭代协议的所有类型都会自动兼容接收可迭代对象的任何语言特性。接收可迭代对象的原生语言特性包括：for-of 循环、数组解构、扩展操作符、Array.from()、创建集合、创建映射等等。

这些原生的语言结构会在后台调用提供的可迭代对象的这个工厂函数，从而创建一个迭代器。

```javascript
const arr = [1, 2, 3];

// for-of 循环
for (const item of arr) {
  console.log(item);		// 1 2 3
}

// 数组解构
const [a, b, c] = arr;
console.log(a, b, c);		// 1 2 3

// 扩展操作符
let arr2 = [...arr];

// 使用 Array.from() 赋值数组
let arr3 = Array.from(arr);

// 创建集合
let set = new Set(arr);

// 创建映射
let map = new Map(arr.map((x, i) => [i, x]))
console.log(map);			// Map(3) { 0 => 1, 1 => 2, 2 => 3 }
```

## 1.2 迭代器协议

迭代器是一种一次性使用的对象，用于迭代与其关联的可迭代对象。

迭代器 API 使用 next() 方法在可迭代对象中遍历数据。每次成功调用 next()，都会返回一个 IteratorResult 对象，其中包含迭代器返回的下一个值。若不调用 next()，则无法知道迭代器的当前位置。

next() 方法返回的迭代器对象IteratorResult 包含两个属性：done 和 value。done 是一个布尔值，表示是否还可以再次调用 next() 取得下一个值；value 包含可迭代对象的下一个值（done 为 false），或者 undefined（done 为 true）。

```javascript
const arr = ["foo", "bar"];
const iter = arr[Symbol.iterator]();

console.log(iter.next());	// { value: 'foo', done: false }
console.log(iter.next());	// { value: 'bar', done: false }
console.log(iter.next());	// { value: undefined, done: true }
console.log(iter.next());	// { value: undefined, done: true }
```

迭代器并不知道怎么从可迭代对象中取得下一个值，也不知道可迭代对象有多大。只要迭代器到达`done: true` 状态，后续调用 next() 就一直返回 undefined。

每个迭代器都表示对可迭代对象的一次性有序遍历。不同迭代器的实例相互之间没有联系，只会独立地遍历可迭代对象：

```javascript
let arr = ['foo', 'bar'];
let iter1 = arr[Symbol.iterator]();
let iter2 = arr[Symbol.iterator]();
console.log(iter1.next()); // { done: false, value: 'foo' }
console.log(iter2.next()); // { done: false, value: 'foo' }
console.log(iter2.next()); // { done: false, value: 'bar' }
console.log(iter1.next()); // { done: false, value: 'bar' }
```

迭代器并不与可迭代对象某个时刻的快照绑定，而仅仅是使用游标来记录遍历可迭代对象的历程。如果可迭代对象在迭代期间被修改了，那么迭代器也会反映相应的变化：

```javascript
let arr = ['foo', 'baz'];
let iter = arr[Symbol.iterator]();
console.log(iter.next()); // { done: false, value: 'foo' }
// 在数组中间插入值
arr.splice(1, 0, 'bar');
console.log(iter.next()); // { done: false, value: 'bar' }
console.log(iter.next()); // { done: false, value: 'baz' }
console.log(iter.next()); // { done: true, value: undefined }
```

## 1.3 自定义迭代器

与 Iterator 接口类似，任何实现 Iterator 接口的对象都可以作为迭代器使用。

```javascript
class Counter {
  constructor(limit) {
    this.limit = limit;
  }
  [Symbol.iterator]() {
    let count = 1;
    let limit = this.limit;
    return {
      next() {				// 返回一个对象，这个实现 next() 方法，按规定返回内容
        if (count <= limit) {
          return { done: false, value: count++ };
        } else {
          return { done: true, value: undefined };
        }
      },
    };
  }
}

let counter = new Counter(3);
for (const item of counter) {
  console.log(item);			// 1 2 3
}
```

## 1.4 提前终止迭代器

可选的 return() 方法用于指定在迭代器提前关闭时执行的逻辑。执行迭代的结构在想让迭代器知道它不想遍历到可迭代对象耗尽时，就可以“关闭”迭代器。可能的情况包括：

* for-of 循环通过 break、continue、return 或 throw 提前退出
* 解构操作并未消费所有值

return() 方法必须返回一个有效的 IteratorResult 对象。简单情况下，可以只返回 `{ done: true }`。内置语言结构在发现还有更多值可以迭代，但不会消费这些值时，会自动调用 return() 方法。

```javascript
class Counter {
  constructor(limit) {
    this.limit = limit;
  }
  [Symbol.iterator]() {
    let count = 1;
    let limit = this.limit;
    return {
      next() {
        if (count <= limit) {
          return { done: false, value: count++ };
        } else {
          return { done: true, value: undefined };
        }
      },
      return() {				// 提前终止迭代器时访问 return()
        return { done: true };
      },
    };
  }
}

let counter = new Counter(3);
for (const item of counter) {
  if (item > 2) break;			// 终止迭代器
  console.log(item); // 1 2 
}
```

如果迭代器没有关闭，则还可以从上次的位置**继续迭代**。比如，数组的迭代器就是不能关闭的。

```javascript
const arr = [1, 2, 3, 4, 5];
const iter = arr[Symbol.iterator]();

for (const item of iter) {
  console.log(item);		// 1 2 3 
  if (item > 2) {
    break;
  }
}

for (const item of iter) {
  console.log(item);		// 4 5
}

```

因为 return() 方法是可选的，所以并非所有迭代器都是可关闭的。要知道某个迭代器是否可关闭，可以测试这个迭代器实例的 return 属性是不是函数对象。不过，仅仅给一个不可关闭的迭代器增加这个方法**并不能**让它变成可关闭的。这是因为调用 return() **不会**强制迭代器进入关闭状态。即便如此，return() 方法还是会被调用。

```javascript
const arr = [1, 2, 3, 4, 5];
const iter = arr[Symbol.iterator]();

iter.return = function () {
  console.log("Exiting early");
  return { done: true };
};

for (const item of iter) {
  console.log(item);		// 1 2 3 Exiting early
  if (item > 2) {
    break;
  }
}

for (const item of iter) {
	console.log(item);		// 4 5	
}

```

# 2 生成器

**生成器**可以在代码块中暂停和恢复代码。使用生成器可以自定义迭代器。

## 2.1 生成器基础

在函数名称前面或者 function 关键字后面加一个星号`*`表示它是一个生成器。只要是可以定义函数的地方，都可以定义生成器。

```javascript
function* generatorFn() {}
let generatorFn = function* () {};
let obj = {
  *generatorFn() {},
};
```

> **注意**：箭头函数不能用来定义生成器函数。

调用**生成器函数**会产生一个**生成器对象**。生成器对象一开始位于**暂停执行（suspended）**的状态。生成器对象实现了 Iterator 接口，因此有 next() 方法。调用这个方法可以让生成器开始或者恢复执行。next() 方法的返回值类似于迭代器，有一个 done 属性和一个 value 属性。

```javascript
function* generatorFn() {}
const g = generatorFn();
console.log(g.next());		// { value: undefined, done: true }
```

value 属性的返回值可以通过生成器函数的返回值指定，默认返回值是 undefined。**只有在调用 next() 方法后，生成器函数才会开始执行**。	

```javascript
function* generatorFn() {
  return "foo";
}
const g = generatorFn();	// 此时不执行函数体，只有调用next() 后才执行函数体
console.log(g.next());		// { value: 'foo', done: true }
```

生成器对象实现了 Iterable 接口，默认迭代器即自身。

```javascript
function* generatorFn() {}
const g = generatorFn();
console.log(g[Symbol.iterator]() === g);
```

## 2.2 通过 yield 中断执行

yield 关键字可以让生成器停止和开始执行。生成器函数在遇到 yield 关键字之前会正常执行。遇到这个关键字后，执行会停止，函数作用域会被保留。停止执行的生成器函数只能通过在生成器对象上调用 next() 方法来恢复执行。

```javascript
function* generatorFn() {
  yield "foo";
  return "bar";
}
const g = generatorFn();
console.log(g.next()); // { value: 'foo', done: false }
console.log(g.next()); // { value: 'bar', done: true }
```

> **注意**：
>
> 1. 生成器函数会针对每个生成器对象区分作用域。在一个生成器对象上调用 next() 不影响其它生成器对象。
> 2. yield 关键字只能在生成器内部使用，用在其它地方会抛出错误。
> 3. yield 关键字必须直接定义在生成器函数内部定义中，出现在嵌套的非生成器函数中会抛出错误。

### 2.2.1 生成器对象作为可迭代对象

把生成器对象当成可迭代对象，使用起来会更加方便。

```javascript
function* generatorFn() {
  yield 1;
  yield 2;
  yield 3;
}

for (const x of generatorFn()) {
  console.log(x);		// 1 2 3
}
```

### 2.2.2 使用 yield 实现输入输出

除了可以作为函数的**中间返回语句**使用，yeild 关键字还可以作为**函数中间参数**使用。*上一次让生成器函数暂停的 yeild 关键字会接收到传给 next() 方法的第一个值*。第一次调用 next() 传入的值不会被使用，因为这一次调用是为了开始执行生成器函数。

```javascript
function* generatorFn() {
  console.log(yield);
  console.log(yield);
}

let g = generatorFn();
g.next("bar");		// 没有任何输出，因为这次是为了开始执行生成器函数
g.next("baz");		// baz
g.next("qux");		// qux
```

yield 关键字可以同时用于输入和输出。

```javascript
function* generatorFn() {
  return yield "foo";
}

const g = generatorFn();
console.log(g.next());		// foo
console.log(g.next("bar"));	// bar
```

###  2.2.3 使用 yield* 产生可迭代对象

使用星号`*`可以增强 yield 的行为，让它能够迭代一个**可迭代对象**，从而一次产出一个值。

```javascript
function* generatorFn() {
  yield* [1, 2, 3];
}

const g = generatorFn();
for (const item of g) {
  console.log(item);		// 1 2 3
}
```

这里的 yield* 实际上只是将一个可迭代对象序列化为一连串可以单独产出的值。和把 yield 放在一个循环中没有区别。

## 2.3 生成器作为默认迭代器

因为生成器对象实现了 Iterable 接口，而且生成器函数和默认迭代器工厂函数被调用之后都产生迭代器。因此，生成器适合作为默认的迭代器工厂函数。

```javascript
class Foo {
  constructor() {
    this.values = [1, 2, 3];
  }
  *[Symbol.iterator]() {
    yield* this.values;
  }
}

const f = new Foo();
for (const x of f) {
  console.log(x);		// 1 2 3
}
```

## 2.4 提前终止生成器

与迭代器类似，生成器也可以提前终止。生成器对象实现了 Iterator 接口，有 next() 和可选的 return() 方法。除了这两个方法，生成器对象还有第三个方法：throw()。

```javascript
function* generatorFn() {}
const g = generatorFn();
console.log(g.next);		// [Function: next]
console.log(g.return);		// [Function: return]
console.log(g.throw);		// [Function: throw]
```

return() 和 throw() 都可以强制生成器进入**关闭（closed）**状态。

### 2.4.1 return()

return() 方法会强制生成器进入**关闭（closed）**状态。提供给 return() 方法的值，就是终止迭代器对象的值。

```javascript
function* generatorFn() {
  for (const x of [1, 2, 3]) {
    yield x;
  }
}

const g = generatorFn();

console.log(g);				// generatorFn {<suspended>}
console.log(g.return(4));	// { value: 4, done: true }
console.log(g);				// generatorFn {<closed>}
```

与迭代器对象不同，所有生成器对象都有 return() 方法，只要通过它进入关闭状态，就无法恢复。后续调用 next() 会显示 `done : true`状态，而提供的任何返回值都不会被储存或传播。

```javascript
function* generatorFn() {
  for (const x of [1, 2, 3]) {
    yield x;
  }
}

const g = generatorFn();

console.log(g.next());		// { value: 1, done: false }
console.log(g.return(4));	// { value: 4, done: true }
console.log(g.next());		// { value: undefined, done: true }
console.log(g.next());		// { value: undefined, done: true }
console.log(g.next());		// { value: undefined, done: true }
```

for-of 循环等内置语言结构会忽略状态为 `done : true` 的 IteratorObject 内部返回的值。

```javascript
function* generatorFn() {
  for (const x of [1, 2, 3]) {
    yield x;
  }
}

const g = generatorFn();

for (const item of g) {
  if (item > 1) {
    g.return(4);
  }
  console.log(item);		// 1 2 
}
```

### 2.4.2 throw()

throw() 方法会在**暂停**的时候将一个提供的错误注入到生成器对象中。如果错误没有被处理，生成器就会**关闭**。

```javascript
function* generatorFn() {
  for (const x of [1, 2, 3]) {
    yield x;
  }
}

const g = generatorFn();

console.log(g);			// generatorFn {<suspended>}
try {
  g.throw("foo");
} catch (e) {
  console.log(e);		// foo
}
console.log(g);			// generatorFn {<closed>}
```

不过，假如生成器内部处理了这个错误，那么生成器就**不会**被关闭，而且可以恢复执行。错误处理会跳过对应的 yield。

```javascript
function* generatorFn() {
  for (const item of [1, 2, 3]) {
    try {
      yield item;
    } catch (e) {}
  }
}

const g = generatorFn();
console.log(g.next());		// { value: undefined, done: true }
g.throw("foo");
console.log(g.next());		// { value: 3, done: false }
```

