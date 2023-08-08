---
title: 【JavaScript红宝书】10.基本引用类型-Global、Math
tags:
  - 前端
  - JavaScript
keywords: JavaScript
description: 《JavaScript高级程序设计（第4版）》第5章：基本引用类型。主要内容：单例内置对象：Global、Math
excerpt: 《JavaScript高级程序设计（第4版）》第5章：基本引用类型。主要内容：单例内置对象：Global、Math
abbrlink: fd41bc08
date: 2022-05-04 11:06:04
index_img: ../images/封面图/红宝书.png
---

## 单例内置对象

**内置对象**是任何由 ECMAScript 实现，与宿主环境无关，并在 ECMAScript 程序开始时就存在的对象。开发者不用显式地实例化内置对象，因为它们已经实例化好了。内置对象包括：Object、Array、String 等等。

## 1 Global

1. Global 对象是一种兜底对象，针对的是不属于任何对象的属性和方法。
2. 事实上，**不存在**全局变量或者全局函数这种东西。在全局作用域中定义的变量和函数都会成为 Global 对象的属性。
3. isNaN()、isFinite()、parseInt()、parseFloat()等等都是 Global 对象身上的方法。除这些方法外还有其它方法：

### 1.1 URL 编解码方法

1. [encodeURI()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/encodeURI)
2. [encodeURIComponent()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent)
3. [decodeURI()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/decodeURI)
4. [decodeURIComponent()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/decodeURIComponent)

### 1.2 eval() 方法

eval() 方法是一个完整的 ECMAScript 解释器，接收一个参数，即一个要执行的 ECMAScript 字符串。

```javascript
eval("console.log('hello')");	// 相当与下面的语句
console.log("hello");			// hello	
```

**解释器发现 eval() 调用时，将参数转换成实际的 ECMAScript 语句，然后将其插入到当前位置**。通过 eval() 执行的代码属于**该调用所在的上下文**。被执行的代码与该上下文拥有**相同的作用域链**。

```javascript
let message = "hello";
eval("console.log(message);");	// hello
```

### 1.3 Global 对象属性

像 undefined、NaN 和 Infinity 等特殊值都是 Global 对象的属性。此外，所有原生引用类型构造函数，比如 Object、Function，也都是 Global 对象的属性。

### 1.4 window 对象

虽然 ECMAScript 没有规定直接访问 Global 对象的方法，但是浏览器将 window 对象实现为 Global 对象的**代理**。因此，所有全局作用域中声明的变量和函数都成了 window 的属性。

```javascript
var s = "hello";
console.log(window.s);		// hello
```

另一种获取 Global 对象的方式是：

```javascript
let global = (function () {
  return this;
})();

console.log(global);		// Window
```

这段代码创建一个**立即调用的函数表达式**，返回 this 值。一个函数在没有明确指定 this 的值的情况下执行时，this 的值等于 Global 对象。

## 2 Math

 ECMAScript 提供了 Math 对象作为保存数学公式、信息和计算的地方。Math 对象上提供的计算要比 JavaScript 实现的快得多。因为 Math 对象上的计算使用了JavaScript 引擎中更高效的实现和处理器指令。

### 2.1 Math 对象属性

| 属性         | 说明                                               |
| ------------ | -------------------------------------------------- |
| Math.E       | 欧拉常数，也是自然对数的底数，约等于 `2.718`。     |
| Math.LN10    | `10` 的自然对数，约等于 `2.303`。                  |
| Math.LN2     | `10` 的自然对数，约等于 `2.303`。                  |
| Math.LOG2E   | 以 `2` 为底的 `E` 的对数，约等于 `1.443`。         |
| Math.LOG10E  | 以 `10` 为底的 `E` 的对数，约等于 `0.434`。        |
| Math.PI      | 圆周率，一个圆的周长和直径之比，约等于 `3.14159`。 |
| Math.SQRT1_2 | 1/2 的平方根                                       |
| Math.SQRT2   | `2` 的平方根，约等于 `1.414`。                     |

### 2.2 min() 和 max() 方法

min() 和 max() 用来确定一组数值中的最大值和最小值：

```javascript
let max = Math.max(12, 32, 4, 13);
console.log(max);       			// 32
```

要知道数组中的最大值和最小值，可以使用扩展操作符：

```javascript
let arr = [1, 13, 432, 12, 43];
console.log(Math.max(...arr));  	// 432
```

### 2.3 舍入方法

#### 2.3.1 Math.ceil()

> ceil：天花板，向上取整

始终**向上**舍入最接近的整数（总是取比它大的整数）：

```javascript
console.log(Math.ceil(12.3));		// 13
console.log(Math.ceil(12.6));		// 13
console.log(Math.ceil(-12.3));		// -12
console.log(Math.ceil(-12.6));		// -12
```

#### 2.3.2 Math.floor()

> floor：地板，向下取整

始终**向下**舍入最接近的整数（总是取比它小的整数）：

```javascript
console.log(Math.floor(12.3));		// 12
console.log(Math.floor(12.6));		// 12
console.log(Math.floor(-12.3));		// -13
console.log(Math.floor(-12.6));		// -13
```

#### 2.3.3 Math.round()

四舍五入到最接近的整数：

```javascript
console.log(Math.round(12.3));      // 12     
console.log(Math.round(12.6));		// 13
console.log(Math.round(-12.3));		// -12
console.log(Math.round(-12.6));		// -13
```

#### 2.3.4 Math.fround()

转换为离它最近的[单精度浮点数](https://en.wikipedia.org/wiki/Single-precision_floating-point_format)形式的数字：

```javascript
console.log(Math.fround(1.333));	// 1.3329999446868896
```

扩展：

JavaScript 内部使用64位的双浮点数字，支持很高的精度。但是，有时你需要用32位浮点数字，比如你从一个[`Float32Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Float32Array) 读取值时。这时会产生混乱：检查一个64位浮点数和一个32位浮点数是否相等会失败，即使二个数字几乎一模一样。

要解决这个问题，可以使用 `Math.fround()` 来将64位的浮点数转换为32位浮点数。在内部，JavaScript 继续把这个数字作为64位浮点数看待，仅仅是在尾数部分的第23位执行了“舍入到偶数”的操作，并将后续的尾数位设置为0。如果数字超出32位浮点数的范围，则返回 `Infinity`或 `-Infinity`。

因为`fround()` 是`Math` 的静态方法，你必须通过 `Math.fround()` 来使用，而不是调用你创建的`Math` 对象的一个实例方法（`Math`不是一个构造函数）。

数字 1.5 可以在二进制数字系统中精确表示，32 位和 64 位相同：

```javascript
Math.fround(1.5); 			// 1.5
Math.fround(1.5) === 1.5; 	// true
```

但是，数字1.337却无法在二进制数字系统中精确表示，所以32位和64位的值是不同的：

```javascript
Math.fround(1.337); 			// 1.3370000123977661
Math.fround(1.337) === 1.337; 	// false
```

