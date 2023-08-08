---
title: 【JavaScript红宝书】11.集合引用类型-Object、Array
tags:
  - 前端
  - JavaScript
keywords:
  - JavaScript
description: 《JavaScript高级程序设计（第4版）》第6章：集合引用类型。主要内容：Object、Array。
excerpt: 《JavaScript高级程序设计（第4版）》第6章：集合引用类型。主要内容：Object、Array。
abbrlink: fdf032f9
date: 2022-05-05 09:29:26
index_img: ../images/封面图/红宝书.png
---

# 1 Object

## 1.1 创建 Object 类型实例

第一种方式，使用 new 操作符和 Object 构造函数：

```javascript
let obj = new Object();
obj.a = "123";
obj.b = "456";
console.log(obj);	// { a: '123', b: '456' }
```

另一种方式是使用**对象字面量**。对象字面量是对象定义的简写形式，简化了包含大量属性的对象的创建。

```javascript
let obj = {
  a: "123",
  b: "456",
};
console.log(obj);	// { a: '123', b: '456' }
```

在对象字面量表示法中，属性名可以是字符串或者数值：

```javascript
let obj = {
  a: "123",
  b: "456",
  3: "789",
};
console.log(obj);	// { '3': '789', a: '123', b: '456' }
```

注意：

1. 属性名是数值的会自动转换成字符串。
2. 使用对象字面量表示法定义变量时，实际上并**不会**调用 Object() 构造函数。

## 1.2 存取属性

属性可以通过**点语法**来进行存取，也可以使用**中括号**来存取。使用中括号时，要在括号内使用属性名的字符串形式。

```javascript
let obj = { a: 1, b: 2 };
console.log(obj.a);			// 1
console.log(obj["b"]);		// 2
```

以下情况只能通过中括号来存取属性：

1. 通过**变量**访问属性

   ```javascript
   let obj = { a: 1, b: 2 };
   let prop = "a";
   console.log(obj[prop]);		// 1
   ```

2. 属性名中包含可能导致错误的字符，或者包含关键字/保留字

   ```javascript
   let obj = { "prop name": 1 };
   console.log(obj["prop name"]);	// 1
   ```

通常情况下，**点语法**是首选的属性存取方式。

# 2 Array

ECMAScript 中的数组，每个槽位中可以存储任意类型的数据。而且，数组是动态大小的，会随着数据的添加自动增长。

## 2.1 创建数组

### 2.1.1 使用 Array() 构造函数

```javascript
let arr = new Array()
console.log(arr);   	// []
```

如果知道数组中元素的数量，可以传入一个数值。此时，length 属性就会自动设置为这个值。

```javascript
let arr = new Array(10);
console.log(arr); // [ <10 empty items> ]
```

也可以传给 Array 构造函数要保存的元素。

```javascript
let arr = new Array("a", "b", "c");
console.log(arr);   // [ 'a', 'b', 'c' ]
```

注意：如果传给 Array 构造函数的是**一个**值时，若这个值是**数值**，则会创建一个长度为指定数值的数组；若这个值是**其它类型**的，则会创建一个只包含这个值的数组。

使用 Array 构造函数时，也可以省略 new 操作符。结果是一样的。

```javascript
let arr = Array("a", "b", "c");
console.log(arr); // [ 'a', 'b', 'c' ]
```

### 2.1.2 使用数组字面量

**数组字面量**是在中括号中包含以逗号分割的元素列表。

```javascript
let arr = ["a", "b", "c"];
```

注意：使用数组字面量创建数组时，不会调用 Array 构造函数。

### 2.1.3 Array.from() 将类数组数据结构转为数组实例

Array.from() 是 ES6 新增的用于创建数组的静态方法，用于将**类数组**对象转为数组实例。

Array.from() 的第一个参数是一个类数组对象，即任何可迭代的结构，或者有一个 length 属性和可索引元素的数据结构。

1. 将字符串拆分为单字符数组

   ```javascript
   let arr = Array.from("abc");
   console.log(arr);	// [ 'a', 'b', 'c' ]
   ```

2. 将映map和set转换为数组

   ```javascript
   const m = new Map().set(1, 2).set(3, 4);
   console.log(m);     // Map(2) { 1 => 2, 3 => 4 }
   const arr = Array.from(m);
   console.log(arr);   //[ [ 1, 2 ], [ 3, 4 ] ]
   ```

   ```javascript
   const s = new Set().add(1).add(2).add(3);
   console.log(s);     // Set(3) { 1, 2, 3 }
   const arr = Array.from(s);
   console.log(arr);   // [ 1, 2, 3 ]
   ```

3. 对现有数组进行**浅复制**

   ```javascript
   const a1 = ["a", "b", "c"];
   const a2 = Array.from(a1);
   console.log(a2);    		// [ 'a', 'b', 'c' ]
   console.log(a1 === a2);     // false
   ```

4. 将 arguments 对象转为数组

   ```javascript
   function fn() {
     return Array.from(arguments);
   }
   console.log(fn(1, 2, 3, 4));	// [ 1, 2, 3, 4 ]
   ```

5. 可以将任何可迭代对象转为数组

   ```javascript
   const iter = {
     *[Symbol.iterator]() {
       yield 1;
       yield 2;
       yield 3;
     },
   };
   
   const arr = Array.from(iter);
   console.log(arr);		// [ 1, 2, 3 ]
   ```

6. 可以将带有*必要属性*的自定义对象转为数组

   ```javascript
   const arrLikeObj = {
     0: "a",
     1: "b",
     2: "c",
     3: "d",
     length: 4,
   };
   
   console.log(Array.from(arrLikeObj));    // [ 'a', 'b', 'c', 'd' ]
   ```

Array.form() 还可以接收第二个可选的映射函数参数，这个函数可以增强新数组的值。

```javascript
const a1 = [1, 2, 3, 4];
const a2 = Array.from(a1, (x) => x * 2);
console.log(a2);    // [ 2, 4, 6, 8 ]
```

Array.from() 还可以接收第三个可选的参数，用于指定映射函数中 this 的值。但是这个重写的 this 值不适用于箭头函数。

```javascript
const a1 = [1, 2, 3, 4];
const a2 = Array.from(
  a1,
  function (x) {
    return x * this.num;
  },
  { num: 2 }
);
console.log(a2); 	// [ 2, 4, 6, 8 ]
```

### 2.1.4 Array.of() 将一组参数转为数组

```javascript
const arr = Array.of(1, 2, 3);
console.log(arr);	// [ 1, 2, 3 ]

function fn() {
  return Array.of(...arguments);
}
console.log(fn(1, 2, 3, 4));	// [ 1, 2, 3, 4 ]
```

## 2.2 数组空位

使用数组字面量初始化数组时，可以使用一串逗号来创建空位。

```javascript
const arr = [1, , , , 5];
console.log(arr);	// [ 1, <3 empty items>, 5 ]
```

ES6 新增的方法普遍将这些空位当成存在的元素，只不过值为 undefined。

ES6 之前的方法会忽略这个空位，具体的行为因方法而异。

```javascript
const arr = [1, , , , 5];

// map() 会跳过空位置
console.log(arr.map(() => 0));  // [ 0, <3 empty items>, 0 ]

// map() 将空位置视为空串
console.log(arr.join("-"));     // 1----5
```

注意：因为行为的不一致存在隐患，因此实践中应避免使用数组空位。或者使用 undefined 去代替数组空位。

## 2.3 数组索引

使用中括号并提供值的索引来存取数组值。

```javascript
const arr = ["a", "b", "c"];
console.log(arr[1]);    // b
```

如果设置值时，中括号中给出的索引值超出了数组目前的最大索引值，那么数组会自动扩展到该索引值+1的长度。

```javascript
let arr = ["a", "b", "c"];
console.log(arr.length);    // 3
arr[4] = "e";
console.log(arr);           // [ 'a', 'b', 'c', <1 empty item>, 'e' ]
console.log(arr.length);    // 5
```

数组中元素的数量保存在 length 属性中，这个属性始终返回大于等于0的值。length 属性不是只读的，可以通过修改 length 的值达到增删数组元素的目的。

## 2.4 检测数组

### 2.4.1 instanceof 操作符

```javascript
const arr = [1, 2, 3];
if (arr instanceof Array) {
  console.log(true);    // true
}
```

在只有一个网页（因而只有一个全局作用域）的情况下足以。

### 2.4.2 Array.isArray() 检测对象是不是数组

```javascript
const arr = [1, 2, 3];
if (Array.isArray(arr)) {
  console.log(true); 	// true
}
```

如果网页中有多个框架（因而有多个不同的全局上下文），需要使用 Array.isArray()。

## 2.5 迭代器方法

### 2.5.1 keys() 返回数组索引的迭代器

keys() 返回**数组索引**的迭代器。因为返回的是迭代器，所以可以使用 Array.from() 方法将其转换成数组。

```javascript
const arr = ["a", "b", "c"];
const iter = arr.keys();
console.log(iter);  					// Object [Array Iterator] {}
const keys = Array.from(arr.keys());
console.log(keys);						// [ 0, 1, 2 ]
```

### 2.5.2 values() 返回数组元素的迭代器

values() 返回**数组元素**的迭代器。因为返回的是迭代器，所以可以使用 Array.from() 方法将其转换成数组。

```javascript
const arr = ["a", "b", "c"];
const iter = arr.values();
console.log(iter);						// Object [Array Iterator] {}
const values = Array.from(arr.values());
console.log(values);					// [ 'a', 'b', 'c' ]
```

### 2.5.3 entries() 返回数组键值对的迭代器

entries() 返回**键值对**的迭代器。因为返回的是迭代器，所以可以使用 Array.from() 方法将其转换成数组。

```javascript
const arr = ["a", "b", "c"];
const iter = arr.entries();				
console.log(iter);							// Object [Array Iterator] {}
const entries = Array.from(arr.entries());
console.log(entries);						// [ [ 0, 'a' ], [ 1, 'b' ], [ 2, 'c' ] ]
```

使用 ES6 的**解构**，可以非常容易地在循环中拆分键值对。

```javascript
const arr = ["a", "b", "c"];
for (const [k, v] of arr.entries()) {
  console.log(k, v);		
}
// 0 a
// 1 b
// 2 c
```

## 2.6 复制和填充方法

### 2.6.1 fill() 批量复制元素

使用 fill() 方法向一个已有数组中插入全部或者部分相同的值。这个方法会**改变原数组**。

如果只提供一个参数，那个这个参数会被视为填充的元素。fill() 数组全部元素将这个参数进行填充。

```javascript
const arr = [0, 0, 0, 0, 0];
arr.fill(6);
console.log(arr);	// [ 6, 6, 6, 6, 6 ]
```

如果提供两个参数，那么第二个参数会被视为开始索引。fill() 数组会从开始索引开始填充。

```javascript
const arr = [0, 0, 0, 0, 0];
arr.fill(6,1);
console.log(arr);	// [ 0, 6, 6, 6, 6 ]
```

如果提供三个参数，那么第三个参数会被视为结束索引。fill() 数组会从开始索引填充到结束索引（含头不含尾）。

```javascript
const arr = [0, 0, 0, 0, 0];
arr.fill(6, 1, 3);
console.log(arr);	// [ 0, 6, 6, 0, 0 ]
```

注意：

1. 负索引从数组末尾开始计算。

2. fill() 会**静默忽略**超出数组边界，零长度和方向相反的索引范围。

   ```javascript
   const arr = [0, 0, 0, 0, 0];
   
   // 索引过低，忽略
   arr.fill(6, -10, -6);	
   console.log(arr);   	// [ 0, 0, 0, 0, 0 ]
   
   // 索引过高，忽略
   arr.fill(6, 6, 10);
   console.log(arr);		// [ 0, 0, 0, 0, 0 ]
   
   // 索引反向，忽略
   arr.fill(6, 4, 2);
   console.log(arr);		// [ 0, 0, 0, 0, 0 ]
   	
   // 索引部分可用，填充可用部分
   arr.fill(6, 2, 6);
   console.log(arr);		// [ 0, 0, 6, 6, 6 ]
   
   ```

### 2.6.2 copyWithin() 填充数组

copyWithin() 会按照指定范围浅复制数组中的部分内容，然后将它们插入到指定索引开始的位置。这个方法会**改变原数组**。

如果只提供一个参数，那么这个参数被视为插入的开始索引。

```javascript
// copyWithin() 会复制索引0开始的内容，并将它们依次插入到索引4开始的位置。
let arr = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
arr.copyWithin(4)
console.log(arr);	// [0, 1, 2, 3, 0, 1, 2, 3, 4, 5]
```

如果提供两个参数，那么第二个参数被视为开始复制的索引。

```javascript
// copyWithin() 会复制索引2开始的内容，并将它们依次插入到索引2开始的位置。
let arr = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
arr.copyWithin(4,2)
console.log(arr);	// [0, 1, 2, 3, 2, 3, 4, 5, 6, 7]
```

如果提供三个参数，那么第三个参数被视为结束复制的索引。

```javascript
// copyWithin() 会复制索引2开始的内容，并将它们依次插入到索引2开始到索引5结束的位置。
let arr = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
arr.copyWithin(6, 2, 5);
console.log(arr);	// [0, 1, 2, 3, 4, 5, 2, 3, 4, 9]
```

注意：

1. 支持负索引，负索引相当于从末尾开始计算。
2.  copyWithin() 会**静默忽略**超出数组边界，零长度和方向相反的索引范围。

## 2.7 转换方法

### 2.7.1 valueOf() 、toString() 、toLocaleString() 

valueOf() 返回的还是数组本身。toString() 返回的是由数组中每个值的等效字符串用逗号拼接而成的字符串，也就是说对每个元素调用 toString() 得到的字符串。

```javascript
let arr = ["a", "b", "c"];

// [ 'a', 'b', 'c' ] object
console.log(arr.valueOf(), typeof arr.valueOf());

// a,b,c string
console.log(arr.toString(), typeof arr.toString());
```

数组的 toLocaleString() 方法，对每个元素调用 toLocaleString()。

注意：如果数组中的元素是undefined、null，则在 valueOf() 、toString() 、toLocaleString() 的返回结果中以**空串**表示。

### 2.7.2 join() 拼接成字符串

toString() 和 toLocaleString() 都返回数组值逗号分割的字符串，想用其它字符串分割可以使用 join() 方法。

```javascript
let arr = ["a", "b", "c"];
console.log(arr.join("-"));		// a-b-c
```

注意：如果不给 join() 传参数或者传 undefined，那么还是以逗号作为分隔符。

## 2.8 栈方法

使用 push() 和 pop() 可以在数组末尾增删元素，从而模拟栈。

### 2.8.1 push() 数组尾部添加元素

接收任意数量的参数，并将它们添加到末尾，返回数组最新的长度。

```javascript
let arr = ["a"];
console.log(arr.push("b", "c", "d"));	// 4
console.log(arr);						// [ 'a', 'b', 'c', 'd' ]
```

### 2.8.2 pop() 数组尾部删除元素

删除数组的最后一项，同时 length -1 ，返回被删除的项。

```javascript
let arr = ["a", "b", "c"];
console.log(arr.pop());		// c 
console.log(arr);			// [ 'a', 'b' ]
```

## 2.9 队列方法

shift() 可以在删除数组的开头的元素。使用 push() 模拟入队，使用 shift() 模拟出队。 

unshift() 可以在数组的开头添加值。使用 unshift() 和 pop() 可以在数组的反向上模拟队列。

### 2.9.1 shift() 数组头部删除元素

shift() 会删除数组的第一项，同时  length -1，返回被删除的项。

```javascript
let arr = ["a", "b", "c"];
console.log(arr.shift());	// a
console.log(arr);			// [ 'b', 'c' ]
```

### 2.9.2 unshift() 数组头部添加元素

unshift() 可以在数组的开头添加任意数量的值（并非依次插入，而是一次性按顺序全部插入），返回数组最新的长度。

```javascript
let arr = ["a", "b", "c"];
console.log(arr.unshift("1", "2", "3"));	// 6
console.log(arr);							// [ '1', '2', '3', 'a', 'b', 'c' ]
```

## 2.10 反向和排序方法

### 2.10.1 reverse() 数组反向

reverse() 可以将数组进行反向排列。该方法会**改变原数组**，并返回**新**数组。

```javascript
let arr = [1, 2, 3, 4];
console.log(arr.reverse());	// [ 4, 3, 2, 1 ]
console.log(arr);		// [ 4, 3, 2, 1 ]
```

### 2.10.2 sort() 数组排序

默认情况下，sort() 会按照升序重新排序数组。为此，sort() 会在每一项上调用 String() 转型函数，然后比较字符串来决定顺序。即使数组中的每一项都是数字，还是会将每一项转成字符串来进行比较、排序。该方法会**改变原数组**，并返回**新**数组。

```javascript
let arr = [1, 20, 3, 4];
console.log(arr.sort());	// [ 1, 20, 3, 4 ]
console.log(arr); // [ 1, 20, 3, 4 ]
```

字符串"20"排在字符串"3"前面，所以结果看起来是不合适的。为此，sort() 方法可以接收一个**比较函数**，用于判断哪个值应在排在前面。

**比较函数**接收两个参数，如果第一个参数应该排在第二个参数的**前面**，就返回**负值**；如果第一个参数应该排在第二个参数的后面，就返回正值；如果两个参数相等，就返回0。

```javascript
// 升序时，传入的比较函数  a-b 返回-1
function compare(a, b) {
  if (a < b) {
    return -1;
  } else if (a > b) {
    return 1;
  } else {
    return 0;
  }
}

let arr = [2, 1, 4, 3];
arr.sort(compare);		// 升序排序
console.log(arr);   	// [ 1, 2, 3, 4 ]
```

```javascript
// 降序时，传入的比较函数	a-b 返回1
function compare(a, b) {
  if (a < b) {
    return 1;
  } else if (a > b) {
    return -1;
  } else {
    return 0;
  }
}

let arr = [2, 1, 4, 3, 3];
arr.sort(compare);		// 降序排序
console.log(arr); 		// [ 4, 3, 3, 2, 1 ]
```

此外，比较函数还可以简写成一个**箭头函数**。

```javascript
let arr = [2, 1, 4, 3, 3];
arr.sort((a, b) => (a < b ? -1 : a > b ? 1 : 0));
console.log(arr);
```

如果数组的元素是**数值**，或者其 valueOf() 方法返回数值的对象（如 Date 对象）。这个比较函数可以直接用两个参数相减。

```javascript
let arr = [2, 1, 4, 3, 3];

arr.sort((a, b) => a - b);	// 升序
console.log(arr);  			// [ 1, 2, 3, 3, 4 ]

arr.sort((a, b) => b - a);	// 降序
console.log(arr);   		// [ 4, 3, 3, 2, 1 ]
```

## 2.11 拼接和截取

### 2.11.1 concat() 数组拼接

concat() 会创建当前数组的一个副本，然后把它的参数添加到副本末尾，最后返回一个**新数组**。如果传入的是一个或多个**数组**，则 concat() 会把数组的每一项都添加到数组中（如果参数是数组，默认打平数组）。

```javascript
let a1 = [1, 2, 3];
let a = a1.concat(4, 5);
console.log(a);			// [ 1, 2, 3, 4, 5 ]
```

```javascript
let a1 = [1, 2, 3];
let a2 = [4, 5, 6];
let a = a1.concat(a2);
console.log(a);			// [ 1, 2, 3, 4, 5, 6 ]
```

打平数组参数的行为可以重写，方法是在参数数组上指定一个特殊的符号：`Symbol.isConcatSpreadable`。这个符号能**阻止** concat() 打平参数数组。相反，这个值设置为 true 可以强制打平**类数组对象**。

```javascript
let a1 = [1, 2, 3];

let a2 = [4, 5, 6];
a2[Symbol.isConcatSpreadable] = false;	// 强制不打平数组

let a3 = {				// 类数组对象
  [Symbol.isConcatSpreadable]: true,	// 强制打平数组
  length: 2,
  0: 7,
  1: 8,
};

let arr1 = a1.concat(a2);
let arr2 = a1.concat(a3);

console.log(arr1);	// [ 1, 2, 3, [ 4, 5, 6 ] ]
console.log(arr2);	// [1, 2, 3, 7, 8]
```

### 2.11.2 slice() 数组截取

slice() 用于创建一个含有原有数组中一个或多个元素的**新数组**。

slice() 方法可以接收一个或者两个参数：返回元素的开始索引和结束索引。如果只有一个参数，则 slice() 会返回该索引到数组末尾的所有元素。如果有两个参数，则 slice() 返回从开始索引到结束索引对应的元素（含头不含尾）。

```javascript
let arr = [1, 2, 3, 4, 5, 6];
console.log(arr.slice(2, 4));	 // [ 3, 4 ]
```

注意：如果 slice() 的参数有负值，那就数组长度加上这个负值来确定位置。

### 2.11.3 splice() 数组删除、插入、替换

splice() 主要目的是在数组中插入元素，但有三种不用的方法使用。splice() 始终**返回**这样的数组：它包含数组中被删除的元素（如果没有删除元素，则返回空数组）。splice() 会**改变原数组**。

**删除**

给 splice() 传 2 个参数：要删除的第一个元素位置、要删除的元素数量。

```javascript
let arr = [1, 2, 3, 4];
console.log(arr.splice(1, 2));  // [ 2, 3 ]
console.log(arr);               // [ 1, 4 ]
```

 **插入**

给 splice() 传 3 个参数：开始位置、0（即要删除的元素数量）、要插入的元素。也可传递第4、5、6 ··· 个参数，表示要插入多个元素。

```javascript
let arr = [1, 2, 3, 4];
console.log(arr.splice(1, 0, "a", "b"));    // []
console.log(arr);           // [ 1, 'a', 'b', 2, 3, 4 ]
```

**替换**

splice() 在删除元素的同时在指定位置插入新的元素。给 splice() 传 3 个参数：开始位置、要删除的元素数量、要插入的元素。也可传递第4、5、6 ··· 个参数，表示要插入多个元素。

```javascript
let arr = [1, 2, 3, 4];
console.log(arr.splice(1, 1, "a", "b")); // [ 2 ]
console.log(arr);       // [ 1, 'a', 'b', 3, 4 ]
```

## 2.12 搜索和位置方法

### 2.12.1 indexOf()、lastIndexOf() 严格搜索

indexOf()、lastIndexOf() 接收两个参数，要查找的元素和一个可选的起始搜索位置。返回第一个找到元素的下标。

indexOf() 从数组开头开始向后搜索，lastIndexOf() 从数组的末尾向前搜索。

```javascript
let arr = [1, 2, 2, 2, 3];
console.log(arr.indexOf(2)); // 1
console.log(arr.lastIndexOf(2)); // 3
console.log(arr.lastIndexOf(4)); // -1
```

找不到则返回-1。

### 2.12.2 includes() 严格搜索

includes() 返回布尔值，表示是否至少找到一个和指定元素匹配的项。返回布尔值。

```javascript
let arr = [1, 2, 2, 2, 3];
console.log(arr.includes(3));   // true
console.log(arr.includes(4));   // false
```

### 2.12.3 find() 、findLast()和 findIndex() 、findLastIndex() 断言搜索

ECMAScript 也允许按照定义的**断言函数**搜索数组。每个索引都会调用这个函数，断言函数的返回值决定了相应索引的元素是否被认为匹配。

> **断言函数**接收 3 个参数：元素、索引和数组本身。其中元素是数组中当前搜索的元素，索引是当前元素的索引，数组即正在搜索的数组。断言函数返回真值，表示是否匹配。

find() 和 findIndex() 使用了断言函数，都从数组的最小索引开始。find() 返回第一个匹配的**元素**，findIndex() 返回第一个匹配元素的**索引**。这两个方法也都接收第二个可选的参数，用于指定断言函数内部的 this 值。

findLast() 和 findLastIndex() 和前面两个方法相似，但是返回最后一个匹配的元素和索引。

```javascript
const arr = [
  { a: 3, b: 2 },
  { a: 15, b: 12 },
  { a: 23, b: 22 },
];

console.log(arr.find((element, index, array) => element.a % 3 === 0));	
// { a: 3, b: 2 }
```

注意：

1. 找不到时，find() 返回 undefined，findIndex() 返回-1。
2. 找到匹配项后，这两个方法都不再继续搜索。

## 2.13 迭代方法

ECMAScript 有5个数组迭代方法，每个方法接收两个参数：以每一项为参数运行的函数、以及可选的作为函数运行上下文的作用域对象（影响函数中 this 的值）。传给每个方法的函数接收3个参数：数组元素、数组索引和数组本身。

### 2.13.1 forEach()

对数组每一项都运行传入的函数，没有返回值。

```javascript
const arr = [1, 3, 2, 4, 5];
arr.forEach((element, index, array) => console.log(element));
// 1 3 2 4 5
```

### 2.13.2 filter()

对数组每一项都运行传入的函数，函数返回 true 的**元素**会组成数组之后返回。

```javascript
const arr = [1, 3, 2, 4, 5];
const res = arr.filter((element, index, array) => element < 4);
console.log(res); // [ 1, 3, 2 ]
```

### 2.13.3 map()

对数组每一项都运行传入的函数，返回由每次函数调用的**结果**构成的数组。

```javascript
const arr = [1, 3, 2, 4, 5];
const res = arr.map((element, index, array) => element < 4);
console.log(res);	// [ true, true, true, false, false ]
```

注：还有一个 flatMap() 方法，类似于 flat(1) + map()，效率更高。 

### 2.13.4 every()

对数组每一项都运行传入的函数，如果**每一项**都返回 true，则这个方法返回 true。

```javascript
const arr = [1, 3, 2, 4, 5];
const res = arr.every((element, index, array) => element < 4);
console.log(res);   // false
```

### 2.13.5 some()

对数组的每一项都运行传入的函数，如果**有一项**函数返回 true，则这个方法返回 true。

```javascript
const arr = [1, 3, 2, 4, 5];
const res = arr.some((element, index, array) => element < 4);
console.log(res); // true
```

## 2.14 归并方法

### 2.14.1 reduce()

reduce() 方法从数组第一项开始遍历到最后一项。reduce() 函数接收两个参数：对每一项都会运行的归并函数，以及可选的以之为归并起点的初始值。

传给 reduce() 的函数接收 4 个参数：上一个归并值、当前项、当前项的索引值和数组本身。这个函数返回的任何值都会作为下次调用同一个函数的第一个参数。如果没有给方法传入第二个参数（作为归并的起点值），则第一次迭代从数组的第二项开始，因此传给归并函数的第一个参数是数组的第一项，第二个参数是数组的第二项。

```javascript
const arr = [1, 2, 3, 4];
let sum = arr.reduce((prev, cur, index, array) => prev + cur);
console.log(sum);   // 10
```

可以提供一个初始值作为 reduce() 函数的初始值：

```javascript
const arr = [1, 2, 3, 4];
let sum = arr.reduce((prev, cur, index, array) => {
  return prev + cur;
}, 20);
console.log(sum);	// 30
```

### 2.14.2 reduceRight()

与 reduce() 之间的唯一区别是，reduceRight() 从最后一项开始遍历到第一项。

## 2.15 扁平化方法

### 2.15.1 flat() 扁平化

深度递归遍历嵌套数组，并且把元素合并为**新**数组返回。接受一个参数，表示提取嵌套数组的深度，默认是1：

```javascript
const arr = [1, [2, 3, [4, 5, 6], 7], 8, 9]

console.log(arr.flat()) 			// [ 1, 2, 3, [ 4, 5, 6 ], 7, 8, 9 ]
console.log(arr.flat(Infinity))		// [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

也可以用来移除数组中的空项：

```javascript
const arr = [1, 2, 3, , , 4]

console.log(arr.flat())	// [ 1, 2, 3, 4 ]
```

### 2.15.2 flatMap() 扁平化迭代

类似于 map() 和 flat(1) 相结合，但是效率更高：

```javascript
const arr = [1, [2, 3, [4, 5, 6], 7], 8, 9]

const res = arr.flatMap((element, index, array)=>{return element})
console.log(res) // [ 1, 2, 3, [ 4, 5, 6 ], 7, 8, 9 ]
```











 
