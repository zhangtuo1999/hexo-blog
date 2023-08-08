---
title: 【JavaScript红宝书】12.集合引用类型-Map、Set
tags:
  - 前端
  - JavaScript
keywords:
  - JavaScript
description: 《JavaScript高级程序设计（第4版）》第6章：集合引用类型。主要内容：Map、Set。
excerpt: 《JavaScript高级程序设计（第4版）》第6章：集合引用类型。主要内容：Map、Set。
abbrlink: 2d755eba
date: 2022-05-06 20:33:08
index_img: ../images/封面图/红宝书.png
---

# 1 Map

Map 是 ES6 新增的一种新的集合类型。Map 的大多属性都可以通过 Object 实现，但有一些细微差异。

## 1.1 初始化

使用 new 关键字和 Map 构造函数可以创建一个空**映射**。

```javascript
const m = new Map();
console.log(m);			// Map(0) {}
```

如果想在创建的同时初始化实例，可以给 Map 构造函数传入一个**可迭代对象**。

使用**嵌套数组**初始化实例：

```javascript
const m = new Map([
  ["a", 1],
  ["b", 2],
]);
console.log(m);			// Map(2) { 'a' => 1, 'b' => 2 }
```

使用自定义**可迭代对象**初始化实例：

```javascript
const m = new Map({
  [Symbol.iterator]: function* () {
    yield ["a", 1];
    yield ["b", 2];
  },
});

console.log(m);			// Map(2) { 'a' => 1, 'b' => 2 }
```

## 1.2 set() 添加

使用 set() 方法可以向 Map 中添加键值对。

```javascript
const m = new Map();
m.set("a", 1);
console.log(m);			// Map(1) { 'a' => 1 }
```

set() 方法返回 Map 实例，因此可以将多个操作连起来，包括初始化声明。

```javascript
const m = new Map().set("a", 1).set("b", 2);
console.log(m);			// Map(2) { 'a' => 1, 'b' => 2 }
```

## 1.3 查询

### 1.3.1 get() 根据键查询值

使用 get() 方法可以根据键查询值，若查不到返回 undefined。

```javascript
const m = new Map().set("a", 1).set("b", 2).set("c", 3);
console.log(m.get("a"));    // 1
console.log(m.get("d"));    // undefined
```

### 1.3.2 has() 查询是否存在某个键

使用 has() 方法可以查询是否存在某个键，存在返回 true，不存在返回 false。

```javascript
const m = new Map().set("a", 1).set("b", 2).set("c", 3);
console.log(m.has('a'));    // true
console.log(m.has('d'));    // false
```

### 1.3.3 size 属性

size 属性中存放键值对的数量。

```javascript
const m = new Map().set("a", 1).set("b", 2).set("c", 3);
console.log(m.size);		// 3
```

## 1.4 删除

### 1.4.1 delete() 根据单个键删除键值对

使用 delete() 可以删除单个键值对。删除成功返回 true，删除失败返回 false。

```javascript
const m = new Map().set("a", 1).set("b", 2).set("c", 3);
console.log(m);     // Map(3) { 'a' => 1, 'b' => 2, 'c' => 3 }
console.log(m.delete("a"));     // true
console.log(m);     // Map(2) { 'b' => 2, 'c' => 3 }
```

### 1.4.2 clear() 清空所有键值对

使用 clear() 可以清空映射中所有的键值对。

```javascript
const m = new Map().set("a", 1).set("b", 2).set("c", 3);
console.log(m); // Map(3) { 'a' => 1, 'b' => 2, 'c' => 3 }
m.clear();
console.log(m); // Map(0) {}
```

## 1.5 使用任意类型作为键和值

和 Object 只能使用数字、字符串和符号作为对象不同，Map 可以使用**任何数据类型**作为键。Map 内部使用类似严格相等的标准（SameValueZero）检查键的匹配性。和 Object 一样，映射的值没有类型限制。

```javascript
const func = function () {};
const obj = {};
const arr = [];
const m = new Map().set(func, 1).set(obj, 2).set(arr, 3);
```

当引用值作为键和值改变时：

```javascript
const m = new Map();
const objKey = {};
const objVal = {};
const arrKey = [];
const arrVal = [];
m.set(objKey, objVal);
m.set(arrKey, arrVal);

console.log(objVal === m.get(objKey)); // true
console.log(m); // Map(2) { {} => {}, [] => [] }

objKey.a = 1;
objVal.b = 2;
arrKey.push("c");
arrVal.push("d");
console.log(m); // Map(2) { { a: 1 } => { b: 2 }, [ 'c' ] => [ 'd' ] }
```

## 1.6 顺序和迭代

与 Object 的一个主要区别是，Map 实例会维护键值对插入时的顺序，因此可以根据插入顺序进行迭代。

### 1.6.0 for-of 使用默认迭代器

Map 定义了默认迭代器 entires()，支持顺序迭代。

```javascript
const map = new Map([
  ["a", 1],
  ["b", 2],
]);

for (const val of map) {
  console.log(val, val[0], val[1]);
}		
// [ 'a', 1 ] a 1
// [ 'b', 2 ] b 2
```

```javascript
const map = new Map([
  ["a", 1],
  ["b", 2],
]);

for (const [k, v] of map) {
  console.log(k, v);
}
// a 1
// b 2
```

### 1.6.1 使用 entries() 

映射实例可以提供一个迭代器，能以插入顺序生成 [key,val] 数组。可以通过 entries() 方法或者 Symbol.iterator 属性（这个属性指向 entries() 方法）来取得这个迭代器。

```javascript
console.log(m.entries === m[Symbol.iterator]);	// true
```

```javascript
const m = new Map().set("a", 1).set("b", 2).set("c", 3);
for (const pair of m.entries()) {
  console.log(pair);
}
// [ 'a', 1 ]
// [ 'b', 2 ]
// [ 'c', 3 ]
```

```javascript
const m = new Map().set("a", 1).set("b", 2).set("c", 3);
for (const pair of m[Symbol.iterator]()) {
  console.log(pair);
}
// [ 'a', 1 ]
// [ 'b', 2 ]
// [ 'c', 3 ]
```

因为 entries() 是默认迭代器，所以可以直接对映射实例使用扩展操作，把映射转为数组：

```javascript
const m = new Map().set("a", 1).set("b", 2).set("c", 3);
console.log([...m]);	// [ [ 'a', 1 ], [ 'b', 2 ], [ 'c', 3 ] ]
```

### 1.6.2 使用 forEach() 

调用映射的 forEach() 方法并传入回调，依次迭代每个键值对。传入的回调接收可选的第二个参数，这个参数用来重写回调内部的 this 值。

```javascript
const m = new Map().set("a", 1).set("b", 2).set("c", 3);
m.forEach((key, val) => console.log(key, val));
// 1 a
// 2 b
// 3 c
```

### 1.6.3 使用 keys() 和 values() 

keys() 和 values() 分别返回以插入顺序生成键和值的迭代器。

```javascript
const m = new Map().set("a", 1).set("b", 2).set("c", 3);

for (const key of m.keys()) {
  console.log(key, m.get(key));
}
// 1 a
// 2 b
// 3 c

for (const val of m.values()) {
  console.log(val);		// 1 2 3
}
```

### 1.6.4 迭代时修改键和值

键和值在迭代器遍历时是可以修改的，但是Map对象内部的引用则**无法修改**。也就是说，迭代时将键修改成另一个值，那么映射内部不会改变。

```javascript
const m = new Map().set("a", 1);
for (let key of m.keys()) {
  key = "b";						// b undefined
  console.log(key, m.get("b")); 
}
console.log(m); 					// Map(1) { 'a' => 1 }
```

```javascript
const objKey = { id: 1 };
const m = new Map([[objKey, "val"]]);
console.log(m);							// Map(1) { { id: 1 } => 'val' }

for (let key of m.keys()) {
  key = { id: 2 };						// 这里修改了键的值，但是映射不会发生改变
  console.log(key, m.get(key));			// { id: 2 } undefined
}

console.log(m);							// Map(1) { { id: 1 } => 'val' }
```

如果键是对象，迭代时修改了这个对象的属性，对象在映射内部仍然引用相同的**值**（只修改了对象的属性，映射的键没有发生改变）。

```javascript
const objKey = { id: 1 };
const m = new Map([[objKey, "val"]]);
console.log(m);							// Map(1) { { id: 1 } => 'val' }

for (let key of m.keys()) {
  key.id = 2;							// 修改对象的值，键（该对象）的值没有变化
  console.log(key, m.get(key));			// { id: 2 } val
}
```

## 1.7 选择 Objcet 还是 Map

### 1.7.1 内存占用：Map 更少

给定固定大小的内存，Map 大约可以比 Object 多存储 50 % 的键值对。

### 1.7.2 插入性能：Map 更好

插入 Map 在浏览器中更快

### 1.7.3 查找速度：Object 更好

大型的 Map 和 Object 之间查找速度差异不大。小型的 Object 比小型的 Map 查找速度快。

### 1.7.4 删除性能：Map 更好

涉及大量的删除操作时，应该使用 Map。

# 2 Set

## 2.1 初始化

使用 new 关键字和 Set 构造函数可以创建一个空**集合**。

```javascript
const s = new Set();
console.log(s);			// Set(0) {}
```

创建的时候同时初始化对象，可以传给 Set 构造函数一个**可迭代对象**。

使用**数组**初始化对象：

```javascript
const s = new Set(["a", "b", "c"]);
console.log(s);			// Set(3) { 'a', 'b', 'c' }
```

使用自定义**可迭代对象**：

```javascript
const s = new Set({
  [Symbol.iterator]: function* () {
    yield 1;
    yield 2;
    yield 3;
  },
});
console.log(s);			// Set(3) { 1, 2, 3 }
```

## 2.2 add() 添加

初始化之后使用 add() 方法添加值。

```javascript
const s = new Set();
s.add("a");
console.log(s);			// Set(1) { 'a' }
```

add() 返回集合的实例，所以可以将多个操作连在一起，包括初始化。

```javascript
const s = new Set().add("a").add("b").add("c");
console.log(s);			// Set(3) { 'a', 'b', 'c' }
```

## 2.3 has() 方法、size 属性

使用 has() 查询集合中是否有某个值。

```javascript
const s = new Set().add("a").add("b").add("c");
console.log(s.has("a"));	// true
console.log(s.has("d"));	// false
```

size 属性中存放集合中值的数量。

```javascript
const s = new Set().add("a").add("b").add("c");
console.log(s.size);	// 3
```

## 2.4 删除

### 2.4.1 delete() 删除单个值

使用 delete() 可以删除单个元素。delete() 返回一个布尔值，表示是否删除成功。

```javascript
const s = new Set().add("a").add("b").add("c");
console.log(s.delete("a")); 	// true
console.log(s);             	// Set(2) { 'b', 'c' }
```

### 2.4.2 clear() 清空集合

使用 clear() 可以清空集合。

```javascript
const s = new Set().add("a").add("b").add("c");
s.clear();
console.log(s);					// Set(0) {}
```

## 2.5 使用任意类型作为值

Set 可以使用**任何数据类型**作为值。集合内部使用类似严格相等的标准（SameValueZero）检查值的匹配性。

## 2.6 顺序和迭代

Set 会维护插入时的顺序，因此支持顺序迭代。

### 2.6.0 for-of 使用默认迭代器

Set 定义了默认迭代器，支持顺序迭代。

```javascript
const set = new Set(["a", "b", "c"]);

for (const val of set) {
  console.log(val);			// a b c
}	
```

### 2.6.1 使用 values() 或 keys()

集合实例提供一个迭代器，能以插入顺序生成集合内容。可以通过 values() 方法及其别名 keys() 取得这个迭代器。也可以通过 Symbol.iterator 属性取得。

```javascript
const s = new Set().add("a").add("b").add("c");
console.log(s.values === s.keys);				// true
console.log(s.values === s[Symbol.iterator]);	// true
```

```javascript
const s = new Set().add("a").add("b").add("c");
for (const value of s.values()) {
  console.log(value);				// a b c
}
```

```javascript
const s = new Set().add("a").add("b").add("c");
for (const value of s[Symbol.iterator]()) {
  console.log(value);				// a b c
}
```

因为 values() 是默认迭代器，所以可以直接对集合实例使用扩展操作，把集合转为数组：

```javascript
const s = new Set().add("a").add("b").add("c");
console.log([...s]);    // [ 'a', 'b', 'c' ]
```

### 2.6.2 使用 forEach()

可以调用集合的 forEach() 方法并传入回调，依次迭代每一个键值对。

```javascript
const s = new Set().add("a").add("b").add("c");
s.forEach((val, dupVal) => console.log(val, dupVal));
// a a
// b b
// c c
```

传入的回调可选的接收第二个值，用于重写回调内部的 this 值。

```javascript
const s = new Set().add("a").add("b").add("c");
s.forEach(
  function (val, dupVal) {
    console.log(val, dupVal, this.a);
  },
  { a: 1 }
);
// a a 1
// b b 1
// c c 1
```

### 2.6.3 迭代时修改集合中的值

修改集合中的值的属性不会影响到作为集合值的身份。

```javascript
const s = new Set(["a"]);

for (let value of s.values()) {
  value = "b";
  console.log(value, s.has(value));		// b false
}

console.log(s);		// Set(1) { 'a' }
```

```javascript
const obj = { a: 1 };
const s = new Set([obj]);
for (let val of s.values()) {
  val.a = 2;
}
console.log(s);				// Set(1) { { a: 2 } }
```

```javascript
const obj = { a: 1 };
const s = new Set([obj]);
for (let val of s.values()) {
  val = { a: 2 };
}
console.log(s);				// Set(1) { { a: 1 } }
```

# 3 迭代和扩展

ECMAScript 6 中新增的迭代器和**扩展操作符**对集合引用类型特别有用。这些新特性让集合类型之间相互操作、复制和修改变得简单。

Array、Map、Set 定义了默认迭代器。这意味着所有类型都支持顺序迭代，都可以使用 for-of 循环。

```javascript
const arr = [1, 2, 3];

for (const val of arr) {
  console.log(val);			// 1 2 3
}
```

```javascript
const map = new Map([
  ["a", 1],
  ["b", 2],
]);

for (const val of map) {
  console.log(val, val[0], val[1]);
}
// [ 'a', 1 ] a 1
// [ 'b', 2 ] b 2

for (const [k, v] of map) {
  console.log(k, v);
}
// a 1
// b 2
```

```javascript
const set = new Set(["a", "b", "c"]);

for (const val of set) {
  console.log(val);			// a b c
}
```

这也意味着这些类型都兼容**扩展操作符**。扩展操作符在对可迭代对象执行**浅复制**时特别有用。只需要简单的语法就可以复制整个对象。

```javascript
const arr1 = [1, 2, 3];
const arr2 = [...arr1];
console.log(arr1);			// [ 1, 2, 3 ]
console.log(arr2);			// [ 1, 2, 3 ]
console.log(arr1 === arr2);	// false
```

对于期待**可迭代对象**的构造函数，只需要传入一个可迭代对象就可以实现复制。

```javascript
const map1 = new Map().set("a", 1).set("b", 2);
const map2 = new Map(map1);
console.log(map1);			// Map(2) { 'a' => 1, 'b' => 2 }
console.log(map2);			// Map(2) { 'a' => 1, 'b' => 2 }	
console.log(map1 === map2);	// false
```

注意：浅复制意味着只复制对象**引用**。

```javascript
const arr1 = [{}];
const arr2 = [...arr1];

console.log(arr1);			// [ {} ]
console.log(arr2);			// [ {} ]
console.log(arr1 === arr2); // false

arr1[0].a = 1;

console.log(arr1);			// [ { a: 1 } ]
console.log(arr2);			// [ { a: 1 } ]
console.log(arr1 === arr2);	// false
```

Array.of() 和 Array.from() 静态方法，与扩展操作符一起使用，可以方法实现互操作。

把数组复制到映射：

```javascript
const arr = [1, 2, 3];
const map = new Map(arr.map((x) => [x, x * 2]));
console.log(map);   		// Map(3) { 1 => 2, 2 => 4, 3 => 6 }
```

把数组复制到集合：

```javascript
const arr = [1, 2, 3];
const set = new Set(arr);
console.log(set);			// Set(3) { 1, 2, 3 }
```

把数组复制回数组：

```javascript
const arr1 = [1, 2, 3];
const arr2 = [...arr1];
console.log(arr2);			// [ 1, 2, 3 ]
```

