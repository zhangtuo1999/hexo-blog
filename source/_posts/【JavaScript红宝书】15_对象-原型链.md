---
title: 【JavaScript红宝书】15.对象-原型链
tags:
  - 前端
  - JavaScript
keywords:
  - JavaScript
description: 《JavaScript高级程序设计（第4版）》第8章：对象、类和面向对象编程。主要内容：原型链。
excerpt: 《JavaScript高级程序设计（第4版）》第8章：对象、类和面向对象编程。主要内容：原型链。
abbrlink: c92c42c1
date: 2022-05-10 14:36:00
index_img: ../images/封面图/红宝书.png
---

## 原型链

**原型链**是 ECMAScript 主要的**继承**方式。其主要思想就是通过**原型**继承多个引用类型的属性和方法。构造函数、原型和实例的关系：**每个构造函数都有一个原型对象，该原型对象有一个属性指回构造函数，而实例有一个内部指针指向原型**。如果*原型是另一个类型的实例*，实例和原型之间就构造了一条原型链。

```javascript
function SuperType() {}
SuperType.prototype.foo = "foo";

function SubType() {}
SubType.prototype = new SuperType();	// 继承
SubType.prototype.bar = "bar";

const instance = new SubType();
console.log(instance.foo);			// foo
console.log(instance.bar);			// bar
```

上述例子中，SubType 通过创建 SuperType 的实例并将其赋值给自己的原型 SubType.pototype，实现了对 SuperType 的继承。这意味着 SuperType 实例可以访问的所有属性和方法也会存在于 SubType.prototype 中。

调用 instance.foo 经历了三步，首先搜索 instance，然后搜索 SubType.prototype，再然后搜索 SuperType.prototype。此时已经找到，故返回。

重写 SubType.prototype 后，SubType.prototype 已经没有了 constructor 属性。访问 SubType.prototype.constructor 时会进入 SubType.prototype 的原型中查找 SubType.prototype 的原型身上的 constructor 属性。此时， SubType.prototype 的原型，也就是 SuperType.prototype 身上的 constructor 属性指向的是 SuperType 构造函数。所以 instance.constructor 指向 SuperType 而不是 SubType。

```javascript
console.log(instance.__proto__.hasOwnProperty('constructor'));	// false
console.log(instance.constructor === SuperType);				// true
```

### 1 默认原型

默认情况下，所有引用类型都继承自 Object。任何函数的原型都是一个 Object 实例，这意味着这个实例的原型指向 Object.prototype。Object.prototype 身上定义了 toString()、valueOf() 等方法。这也是自定义类型能继承toString()、valueOf() 等方法的原因。

### 2 原型和实例的关系

原型和实例的关系可以通过两种方式来确定。

#### 2.1 使用 instanceof 操作符

如果一个实例的原型链中出现过相应构造函数的原型，那么 instanceof 操作符返回 true。

```javascript
console.log(instance.__proto__ === SubType.prototype);						// true
console.log(instance.__proto__.__proto__ === SuperType.prototype);			// true
console.log(instance.__proto__.__proto__.__proto__ === Object.prototype);	// true
```

```javascript
console.log(instance instanceof SubType);		// true
console.log(instance instanceof SuperType);		// true
console.log(instance instanceof Object);		// true
```

#### 2.2 使用 isPrototypeOf() 方法

如果一个实例的原型链中出现过相应构造函数的原型，那么 isPrototypeOf() 返回 true。

```javascript
console.log(Object.prototype.isPrototypeOf(instance));		// true
console.log(SubType.prototype.isPrototypeOf(instance));		// true
console.log(SuperType.prototype.isPrototypeOf(instance));	// true
```

### 3 原型链的问题

#### 3.1 共享引用值

原型中包含引用值时，包含的引用值会在所有的实例之间共享。使用原型链实现继承时，原型实际上变成了另一个类型的实例。这意味着原先的实例属性变成了原型属性。

```javascript
function SuperType() {
  this.names = ["foo", "bar"];
}
function SubType() {}
SubType.prototype = new SuperType();
const s1 = new SubType();
const s2 = new SubType();
console.log(s1.names, s2.names);	// [ 'foo', 'bar' ] [ 'foo', 'bar' ]
s2.names.push("foobar");
console.log(s1.names);				// [ 'foo', 'bar', 'foobar' ]
```

上述例子中实例 s1 对 names 属性的修改同样会体现在实例 s2 中。

#### 3.2 子类型实例化时不能给父类型传参

子类型在实例化时不能给父类型的构造函数传参。再加上之前提到的原型中包含应用值的问题，导致原型链基本上不会被单独使用。



