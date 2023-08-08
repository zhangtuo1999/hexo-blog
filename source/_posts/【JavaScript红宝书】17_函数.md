---
title: 【JavaScript红宝书】17.函数
tags:
  - 前端
  - JavaScript
keywords:
  - JavaScript
description: 《JavaScript高级程序设计（第4版）》第10章：函数。主要内容：箭头函数、函数名、参数、函数表达式、尾调用优化。
excerpt: 《JavaScript高级程序设计（第4版）》第10章：函数。主要内容：箭头函数、函数名、参数、函数表达式、尾调用优化。
abbrlink: 4626a015
date: 2022-05-12 09:15:23
index_img: ../images/封面图/红宝书.png
---

# 函数

ECMAScript 中，函数实际上是对象。每个函数都是 Function 类型的实例，而 Function 也有属性和方法。

## 0 定义函数

**函数声明**的方式来定义：

```javascript
function sum(a, b) {
  return a + b;
}
```

**函数表达式**的方式来定义：

```javascript
let sum = function (a, b) {
  return a + b;
};
```

**箭头函数**的方式来定义：

```javascript
let sum = (a, b) => {
  return a + b;
};
```

**Function 构造函数**的方式来定义。这个构造函数接收任意多个字符串参数，最后一个参数会被当成函数体，而前面所有的参数都会被当成新函数的参数。

```javascript
let sum = new Function("a", "b", "return a + b");
```

> **注意**：这几种实例化对象的方式之间存在**微妙区别**。

## 1 箭头函数

ECMAScript 6 新增了使用 `=>`定义函数表达式的能力。任何可以使用函数表达式的地方，都可以使用箭头函数。

```javascript
// 函数表达式
let sum = function (a, b) {
  return a + b;
};

// 箭头函数
let sum2 = (a, b) => {
  return a + b;
};
```

简洁的语法非常适合于嵌入函数的场景：

```javascript
const arr = [1, 2, 3];
console.log(arr.map((x) => 2 * x));		// [ 2, 4, 6 ]
```

如果只有一个参数，那也可以**省略括号**。只有在没有参数或者多个参数的时候需要括号。

```javascript
// 这两种方式等价
let func = (x) => { return 2 * x; };
let func = x => { return 2 * x; };		// 省略括号
```

也可以**省略大括号**。使用大括号说明包含函数体，可以在一个函数体中写多条语句。如果不使用大括号，那么箭头后面就只能有**一行**代码，比如一个**赋值操作**或者一个**表达式**。**省略大括号会隐式返回这行代码的值**。

```javascript
// 这两种方式等价
let double = (x) => {
  return 2 * x;
};
let triple = (x) => 3 * x;		// 省略大括号，只有一个表达式
```

```javascript
// 这两种方式等价
let setName = (obj) => {
  obj.name = "Foo";
};
let setName = (obj) => obj.name = 'Foo'	// 省略大括号，只有一个赋值操作 
```

```javascript
// 无效写法
let multi = (a,b) => return a * b
```

> **注意**：箭头函数不能使用 arguments、super 和 new.target，也不能用作构造函数。此外，箭头函数没有 prototype 属性。

## 2 函数名

### 2.1 函数名是指向函数的指针

因为函数名就是指向函数的变量，所以和其它对象变量具有相同的行为。这意味着，一个函数可以有多个名称。

```javascript
function sum(a, b) {
  return a + b;
}
let sum2 = sum;		// sum 后没有括号
```

> **注意**：使用不带括号的函数名会访问函数指针，而不会执行函数。

### 2.2 name 属性

ES6 中所有的函数对象都会暴露一个 name 属性，值是一个字符串化的变量名。

如果函数没有名称，会显示空串；如果函数是使用 Function 构造函数创建的，则会显示 `'anonymous'`。

```javascript
function foo() {}
let bar = function () {};
let baz = () => {};

console.log(foo.name);			// foo
console.log(bar.name);			// bar
console.log(baz.name);			// baz
console.log((() => {}).name);	// 空串
console.log(new Function().name);	// anonymous
```

如果函数是一个获取函数、设置函数，或者使用 bind() 实例化，那么标识符会加上一个前缀。

```javascript
let person = {
  get name() {
    return this.name_;
  },
  set name(newVal) {
    this.name_ = newVal;
  },
};

const descriptor = Object.getOwnPropertyDescriptor(person, "name");
console.log(descriptor);
// {
//   get: [Function: get name],
//   set: [Function: set name],
//   enumerable: true,
//   configurable: true
// }
console.log(descriptor.set.name);	// set name
console.log(descriptor.get.name);	// get name
```

```javascript
function foo() {}
console.log(foo.bind(null).name); 	// bound foo
```

## 3 理解参数

ESMAScript 函数不关心传入的参数个数和数据类型。即使定义函数的时候要接收两个参数，并不意味着必须传两个参数，可以传一个、三个或者不传参数。

之所以这样，是因为 ECMAScript 函数的参数在内部表现为一个**类数组对象**。函数被调用的时候总是接收一个类数组对象，而且函数不关心这个这个对象中包含什么。

### 3.1 arguments 对象

在使用 function 关键字定义（非箭头）函数时，可以在函数内部访问 **arguments 对象**。

#### 3.1.1 使用 arguments 对象获取函数参数

arguments 对象是一个**类数组对象**（但**不是** Array 的实例），因此可以使用中括号语法来访问其中的元素。第一个参数是 arguments[0]，第二个参数是 arguments[1]。

如果要确定传递进来多少个参数，可以访问 arguments.length 属性。

```javascript
function sum() {
  console.log(arguments);			// [Arguments] { '0': 1, '1': 2, '2': 3 }
  console.log(arguments.length);	// 3
  console.log(arguments[1]);		// 2
}
sum(1, 2, 3);
```

#### 3.1.2 arguments 对象和**命名参数**

arguments 对象可以和**命名参数**一同使用。

```javascript
function sum(num1) {		// num1 命名参数
  console.log(num1 + arguments[1]);
}
sum(1, 2);		// 3
```

#### 3.1.3 修改 arguments 对象和命名参数

修改 arguments 对象的值，命名参数**会**跟着变。

```javascript
function sum(num1, num2) {
  console.log(num1, num2);					// 1 2
  console.log(arguments[0], arguments[1]);	// 1 2
  arguments[1] = 9;
  console.log(arguments[1], num2);			// 9 9
}
sum(1, 2);
```

修改命名参数，会不会影响到 arguments 对象呢？非严格模式下**会**，严格模式下**不会**。

> **注意**：如果只传了一个参数，然后把 arguments[1] 设置为一个值，那么这个值不会反映到第二个命名参数上。因为 arguments 对象的长度是根据**传入**的参数个数，而非**定义**函数时给出的命名参数个数确定的。

### 3.2 箭头函数中的参数

如果函数是使用箭头函数定义的，那么传给函数的参数就不能使用 arguments 来访问，只能通过命名参数来访问。

## 4 没有重载

ECMAScript 中的函数没有函数签名，因此无法重载。

## 5 默认参数值

### 5.1 定义默认参数

ECMAScript6 开始支持显式定义默认参数。只要在函数定义中参数后面使用` = `就可以为参数赋一个默认值。

```javascript
function sum(a = 1, b = 2) {	// 命名参数a的默认值是1
  return a + b;
}
console.log(sum(8));
```

### 5.2 调用函数返回的值作为默认参数

默认参数值并不限于原始值或者对象类型，可以使用任意表达式，当然也可以使用调用函数返回的值。

```javascript
function getNum() {
  return 2;
}
function sum(a = 1, b = getNum()) {
  return a + b;
}
```

注意：

1. 函数的默认参数只有在函数被**调用**时才会求值，不会在函数**定义**时求值。

2. 计算默认值的函数只有在调用函数但**未传相应参数**时才会被调用。

3. 箭头函数同样也可以使用默认参数，只不过在只有一个参数时，就必须使用括号而不能忽略。

   ```javascript
   let doubleNum = (x = 2) => 2 * x;
   ```

### 5.3 默认参数作用域与暂时性死区

给多个函数参数定义默认值实际上跟使用 let 关键字顺序声明变量一样。

因为参数是按顺序初始化的，所以后定义的默认值参数可以引用先定义的参数。

```javascript
function sum(num1 = 1, num2 = num1) {
  return num1 + num2;
}
```

参数初始化顺序遵循**暂时性死区**原则，前面定义的参数不能引用后面定义的。

```javascript
function sum(num1 = num2, num2 = 2) {	// 报错
  return num1 + num2;
}
console.log(sum());
```

默认参数也存在于自己的作用域中，它们不能引用函数体的作用域。

```javascript
function sum(num1 = 1, num2 = n) {		// 报错
  let n = 2;
  return num1 + num2;
}
console.log(sum());
```

## 6 参数扩展与收集

ECMAScript6 中新增了**扩展操作符**，使用它可以非常简洁地操作和组合集合数据。扩展操作符既可以用于调用函数时传参，也可以用于定义函数参数。

### 6.1 扩展参数

对可迭代对象应用扩展操作符，并将其作为一个参数传入，就可以将可迭代对象**拆分**，并将迭代返回的每个值单独传入。

```javascript
function sum() {
  let result = 0;
  for (let i = 0; i < arguments.length; i++) {
    result += arguments[i];
  }
  return result;
}
const arr = [1, 2, 3];
console.log(sum(...arr));	// 使用扩展操作符来扩展参数
```

### 6.2 收集参数

可以使用扩展操作符把不同长度的独立数组组合为一个数组。这个有点类似 arguments 对象的构造机制，只不过收集参数的结果会得到一个 Array 实例。

```javascript
function sum(...values) {	// 使用扩展操作符来收集参数
  console.log(values);
}

sum(1, 2, 3, 4);	// [ 1, 2, 3, 4 ]
```

收集参数的前面如果还有命名参数，则只会收集其余的参数；如果没有收集到则会得到空数组。因为收集参数的结果可变，所以只能将其作为最后一个参数。

```javascript
function sum(num1,...values) {}
```

箭头函数虽然不支持 arguments 对象，但是支持收集参数的定义方式，因此可以实现与使用 arguments 一样的逻辑。

```javascript
let sum = (...values) => {
  console.log(values);
};
```

> **注意**：使用收集参数并不影响 arguments 对象，它仍然反映调用时传给函数的参数。

## 7 函数声明和函数表达式

函数声明和函数表达式存在一个细微区别。JavaScript 引擎在执行代码之前，会先读取函数声明，并在执行上下文中生成函数定义，这个过程叫做**函数声明提升**。而函数表达式必须等到代码执行到它那一行，才会在执行上下文中生成函数定义。

```javascript
// 函数声明提升
console.log(sum());		// 可以执行
function sum() {}
```

```javascript
// 使用函数表达式来定义函数，只有执行到定义函数的时候才会在执行上下文中生成函数定义
console.log(sum());		// 报错	
let sum = function () {};
```

## 8 函数作为值

因为函数名在 ECMAScript 中就是变量，所以函数可以用在任何可以使用变量的地方。这意味着不仅可以把函数作为参数传递给另一个参数，而且可以在一个函数中返回另一个函数。

函数作为**参数**：

```javascript
function foo(bar, baz) {
  bar(baz);
}
```

函数作为**返回值**：

```javascript
function foo(){
  return function(){}
}
```

## 9 函数内部

函数内部存在几个特殊对象：arguments、this、caller，ES6 新增了 new.target 属性。

### 9.1 arguments

arguments 是一个类数组对象，包含调用函数时传入的所有参数。这个对象只有以 function 关键字定义函数时才会有，箭头语法定义函数时没有。

#### arguments.callee

arguments 还有一个 callee 属性，是一个指向 arguments 对象**所在函数**的指针。

下面是一个递归的阶乘函数：

```javascript
function factorial(num) {
  return num <= 1 ? 1 : num * factorial(num - 1);
}
```

这个函数要想正确执行，必须保证函数名是 factorial，这就导致了紧密耦合。可以使用 arguments.callee 来让函数逻辑和函数名解耦。重写之后，无论函数名是什么都可以运行。

```javascript
function factorial(num) {
  return num <= 1 ? 1 : num * arguments.callee(num - 1);
}
```

### 9.2 this

this 对象在标准函数和箭头函数中有不同的行为。

#### 9.2.1 标准函数中的 this

在**标准函数**中，this 引用的是**调用该函数的上下文对象**。也就是说，谁调用了函数，this 就指向谁。在全局上下文中调用函数时，this 指向 global 对象（在浏览器中表现为 window 对象）。

```javascript
color = "red";		// 省略 var 操作符，可以将 color 定义为全局变量
let obj = {
  color: "green",
};

function sayColor() {
  console.log(this.color);
}

sayColor();			// red		此时，this 指向 global 对象
obj.sayColor = sayColor;
obj.sayColor();		// green	此时，this 指向 obj 
```

#### 9.2.2 箭头函数中的 this

在**箭头函数**中，this 引用的是**定义该函数的上下文**。

```javascript
color = "red";
let obj = {
  color: "green",
};

let sayColor = () => console.log(this.color);

sayColor();			// red
obj.sayColor = sayColor;
obj.sayColor();		// red
```

在事件回调或者定时回调中调用某个函数中，this 值指向的并非想要的对象。此时将回调函数写成箭头函数就可以解决问题。这是因为箭头函数中 this 会保留定义该函数时的上下文。

### 9.3 caller

函数对象上也有一个 caller 属性，这个属性引用的是调用当前函数的**函数**。如果实在全局作用域中调用的则是 null。

```javascript
function outer() {
  inner();
}

function inner() {
  console.log(inner.caller);
}

outer();	// [Function: outer]
```

### 9.4 new.target

ECMAScript 中的函数始终可以作为构造函数实例化一个对象，也可以作为普通函数被调用。ES6 新增了检测函数是否可以使用 new 关键字调用的 new.target 属性。如果函数是正常调用的，则 new.target 的值是 undefined；如果是使用 new 关键字调用的，则 new.target 将引用被调用的**构造函数**。

```javascript
function fn() {
  if (!new.target) {
    console.log("不是使用 new 调用的");
    return;
  }
  console.log(new.target,"使用 new 调用的");
}

new fn();	// [Function: fn] 使用 new 调用的
fn();		// 不是使用 new 调用的
```

## 10 函数属性和方法

ECMAScript 中，函数是对象，因此有属性和方法。

### 10.1 length 属性

length 属性中保存的是**函数定义**时**命名参数**的个数。

```javascript
function foo(bar, baz) {}
console.log(foo.length);	// 2
```

### 10.2 prototype 属性

prototype 属性保存着函数的原型。

### 10.3 apply() 和 call()

函数身上有两个方法，apply() 和 call()。这两个方法都会以**指定的 this 值**来调用函数，即会设置调用函数时函数体内的 this 值。

#### apply()

apply() 函数接收两个参数：函数内的 **this** 值和一个**参数数组**。第二个参数可以是 Array 类型的实例，也可以是 **arguments 对象**。

第二个参数传入**参数数组**：

```javascript
function sum(num1, num2) {
  console.log(num1 + num2);
}

sum.apply(this, [1, 2]);		// 3
```

第二个参数传入 **arguments 对象**：

```javascript
function sum(num1, num2) {
  console.log(num1 + num2);
}

function callSum(num1, num2) {
  sum.apply(this, arguments);	// 3
}

callSum(1, 2)
```

#### call()

call() 方法和 apply() 方法作用一样，只是传参的形式不同。第一个参数和 apply() 一样，也是 this 值。剩下的参数是要给被调用函数传递的**参数**。也就是说，通过 call() 向函数传参时，必须将参数**一个一个**列举出来。

```javascript
function sum(num1, num2) {
  console.log(num1 + num2);
}

sum.call(this, 1, 2);			// 3
```

apply() 和 call() 可以控制函数调用上下文，即函数体内 this 指向。

```javascript
function foo() {
  console.log(this.bar);
}

bar = "global";

const obj = {
  bar: "obj",
};

foo.call(this);		// global
foo.call(obj);		// obj
```

### 10.4 bind()

bind() 方法创建一个**新的函数**，在 bind() 被调用时，这个新函数的 this 被指定为 bind() 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。

```javascript
const obj = {
  bar: "obj",
};
function foo() {
  console.log(this.bar);
}
const newObj = foo.bind(obj);
newObj();// obj
```

## 11 函数声明和函数表达式

定义函数主要有两种形式：函数声明和函数表达式。

### 11.1 函数声明

函数声明：

```javascript
function foo() {}
```

函数声明的的关键特点是**函数声明提升**，即函数声明会在代码执行之前获得定义。这意味着函数声明可以出现在调用它的代码之后。

```javascript
foo()		// 正确执行
function foo() {}
```

### 11.2 函数表达式

函数表达式的一种形式：

```javascript
const foo = function(){}
```

函数表达式看起来像一个普通的变量定义和赋值，即创建一个**函数**再把它赋值给一个变量。这样创建的函数叫**匿名函数**（anonymous function），因为 function 关键字后面没有标识符。未赋值给其它变量的匿名函数的 name 属性是空串。

函数表达式和其它表达式一样，需要先赋值再使用。

```javascript
foo()		// 报错
const foo = function(){}
```

## 12 递归

递归函数的通常形式是一个函数通过名称调用自己。

```javascript
// 求阶乘
function factorial(num) {
  return num <= 1 ? 1 : num * factorial(num - 1);
}

console.log(factorial(4));		// 24
```

使用 arguments.callee 来代替函数内部的函数名。

```javascript
function factorial(num) {
  return num <= 1 ? 1 : num * arguments.callee(num - 1);
}

console.log(factorial(4));		// 24
```

## 13 尾调用优化

函数调用会在内存形成一个"调用记录"，又称**"调用帧"**（call frame），保存调用位置和内部变量等信息。ES6 规范新增了一项内存管理优化机制，可以让 JavaScript 引擎在满足条件时可以重用调用帧。

### 13.1 尾调用

尾调用，即外部函数的返回值是一个内部函数的返回值。

```javascript
function foo() {
  return bar();
}
```

### 13.2 尾调用优化

在优化之前，执行上述的例子会在内存中发生如下操作：

1. 执行到 foo() 函数，第一个调用帧被推到栈上。
2. 执行 foo() 函数体，到 return 语句。计算返回值必须计算 bar()。
3. 执行到 bar() 函数，将第二个调用帧推到栈上。

4. 执行 bar() 函数体，计算返回值。
5. 将返回值传给 foo()，然后 foo() 再返回值。

在 ES6 优化后，这个例子会在内存中执行下面的操作：

1. 执行到 foo() 函数体，第一个调用帧被推到栈上。
2. 执行 foo() 函数体，到 return 语句。计算返回值必须计算 bar()。
3. 引擎发现把第一个调用帧弹出栈外也没有问题。
4. 弹出 foo() 调用帧。
5. 执行到 bar() 函数，将调用帧推到栈上。
6. 执行 bar() 函数体，计算返回值。
7. 将 bar() 调用帧弹出。

优化前，每多调用一次嵌套函数，就会多增加一个调用帧。优化后，无论调用多少次嵌套函数，都只有**一个**栈帧。尾调用优化的**关键**：如果函数的逻辑允许基于尾调用将其销毁，则引擎就会那么做。

### 13.3 尾调用优化的条件

尾调用优化的条件就是确定外部调用帧真的没有必要存在了。涉及的条件如下：

* 代码在**严格模式**下执行
* 外部函数的返回值是对尾调用函数的调用
* 尾调用函数返回后不需要执行额外的逻辑
* 尾调用函数不是引用外部函数作用域中自有变量的闭包

之所以要求**严格模式**，主要是因为在非严格模式下函数调用中允许使用 f.arguments 和 f.caller，而它们都会引用外部函数的调用帧。这意味着不能应用优化了。因此尾调用优化要求必须在严格模式下有效，以防引用这些属性。

## 14 闭包

参考 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures)。

## 15 立即调用的函数表达式

立即调用的匿名函数，又称立即调用的函数表达式（IIFE）。类似于函数声明，但是由于被包含在括号里，所以会被解释为函数表达式。紧跟在扩号后面的第二组括号会立即调用前面的函数表达式。

```javascript
(function () {
	// 块作用域
})();
```

使用 IIFE 可以模拟**块级作用域**。即在一个函数表达式内部声明变量，然后立即调用这个函数，这样位于函数体作用域的变量就像是在块级作用域中一样。

ES5 尚未支持块级作用域，使用 IIFE 模拟块级作用域非常普遍。在 ES6 之后，IIFE 就没那么必要了。
