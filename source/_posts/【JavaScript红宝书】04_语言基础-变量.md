---
title: 【JavaScript红宝书】4.语言基础 - 变量
tags:
  - 前端
  - JavaScript
excerpt: 《JavaScript高级程序设计（第4版）》笔记。原书第3章：语言基础。主要内容：var、let、const。
abbrlink: 78d9e65d
date: 2022-04-25 21:52:17
index_img: ../images/封面图/红宝书.png
---

# 变量

ECMAScript 6 之前只支持 var 关键字，ECMAScript 6 开始支持 let 和 const。

## 1 var 关键字

### 1.1 var 声明作用域

使用 var 操作符定义的变量会成为**包含它的函数**的局部变量。使用 var 在一个函数中定义的变量，意味着变量在退出函数时销毁：

```javascript
function test(){
    var message = 'hello';	// 使用 var 在函数中定义局部变量
}
test();
console.log(message);		// 出错
```

省略 var 操作符可以将变量定义为**全局变量**：

```javascript
function test() {
    message = "hello";		// 全局变量
}
test();						// 调用
console.log(message);		// hello 
```

省略掉 var 操作符后，message 成了全局变量。但是，必须在 test() 函数被**调用**一次后才能访问到全局变量 message。

### 1.2 var 声明提升

**声明提升**，就是把所有的变量声明都拉到函数作用域的顶部。比如，下面这种形式不会报错：

```javascript
function test() {
    console.log(message);	// undefined
    var message = "hello";
}
test();
```

因为，ECMAScript 在运行时将上面的代码看成下面的代码：

```javascript
function test() {
	var messsge;
    console.log(message);	// undefined
    message = "hello";
}
test();
```

此外，**多次声明同一变量**也没有问题：

```javascript
var a = 1;
var a = 2;
var a;
console.log(a);	// 2
```

## 2 let 关键字

let 和 var 的作用差不多，最明显的区别是，let 声明的范围是**块作用域**，而 var 声明的是**函数作用域**。块作用域是函数作用域的**子集**。

```javascript
if (true) {
    var a = 1;		// var 声明的变量属于函数作用域，范围大于块作用域
}
console.log(a);		// 1

if (true) {
    let b = 2;		// let 声明的变量属于块作用域
}
console.log(b);		// 出错
```

let **不允许**同一个块作用域下出现冗余声明。并且，对于冗余声明的报错不因**混用** let 和 var 受影响。

```javascript
var a;
var a;

let b;
let b;	// 报错

let c;
var c;	// 报错

var d;
let d;	// 报错
```

JavaScript 引擎会记住变量声明标识符及其所在的块作用域。因此嵌套使用标识符不会出错，因为在**同一个块**中没有嵌套声明：

```javascript
let a = 1;
if (true) {
    let a = 2;
    console.log(a); // 2
}
console.log(a);   	// 1
```

### 2.1 暂时性死区

let 和 var 的另一个重要区别是，let **不会**声明提升。在 let 声明前使用变量的执行瞬间叫做**暂时性死区**。

```javascript
console.log(a);		// undefined （声明提升，不会报错）
var a = 1;

console.log(b);		// 报错（此时称为暂时性死区）
let b = 2;
console.log(b);		
```

虽然 JavaScript 引擎会注意到后面的变量声明，但是不会像 var 那样进行声明提升。

### 2.2 全局声明

与 var 关键字不同，使用 let 声明的变量不会成为 window 对象的属性。

```javascript
var a = 1;
console.log(window.a);	// 1

let b = 2;
console.log(window.b);	// undefined
```

### 2.3 条件声明

let 无法使用条件声明。

```javascript
// let b;
if (typeof b === "undefined") {
    let b;			// b被限制在if的块作用域内。这里声明的b，外面是感知不到的。
}
b = 2;				// 因为没有感知到上面定义的b，所以这里相当于对全局变量b赋值。
```

注意：条件声明是一种反模式，**尽量不要用条件声明**。

### 2.4 for 循环中的 let 声明

使用 var 定义循环变量会渗透到循环体外，造成污染：

```javascript
for (var i = 0; i < 5; i++) {}
console.log(i);		// 5
```

使用 let 不会出现这个问题：

```javascript
for (var i = 0; i < 5; i++) {}
console.log(i);		// 报错，说明循环变量没有污染循环体外
```

一个例子：

```javascript
for (var i = 0; i < 5; i++) {
    setTimeout(() => {
        console.log(i);			// 5 5 5 5 5
    }, 100);
}
```

这个例子不会输出 0 1 2 3 4，而是会输出 5 5 5 5 5。这是因为退出循环时，迭代变量保存的是退出循环时的值 5。

而使用 let 不会出现这个问题，因为使用 let，引擎会为每个循环声明一个新的迭代变量：

```javascript
for (let i = 0; i < 5; i++) {
    setTimeout(() => {
        console.log(i);			// 0 1 2 3 4 
    }, 100);
}
```

## 3 const 声明

const 的行为和 let 基本相同，唯一的重要区别是，const 声明的变量需要同时赋予初始值，并且修改 const 声明的变量会报错。

const 也不允许在同一个块中重复声明。

如果 const 变量引用的是一个对象，那么修改对象内部属性不会报错：

```javascript
const a = {};
a.foo = "bar";	// 没有违反const限制
```

## 4 声明风格与最佳实践

1. 不使用 var
2. 优先使用 const，在预知会修改变量时才去使用 let
