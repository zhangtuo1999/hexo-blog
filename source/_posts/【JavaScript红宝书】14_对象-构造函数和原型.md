---
title: 【JavaScript红宝书】14.对象-构造函数和原型
tags:
  - 前端
  - JavaScript
keywords:
  - JavaScript
description: 《JavaScript高级程序设计（第4版）》第8章：对象、类和面向对象编程。主要内容：构造函数、原型。
excerpt: 《JavaScript高级程序设计（第4版）》第8章：对象、类和面向对象编程。主要内容：构造函数、原型。
abbrlink: a5403f42
date: 2022-05-09 09:07:12
index_img: ../images/封面图/红宝书.png
---

# 创建对象

ES6 开始正式支持**类**和**继承**。ES6 的类仅仅是封装了 ES5.1 **构造函数**加上**原型继承**的语法糖。

##  1 工厂模式

**工厂模式**是一种设计模式，用于抽象**创建特定对象**的过程。下面是一个工厂模式创建对象的例子：

```javascript
function createPerson(name, age) {
  return { name, age };
}
const p1 = createPerson("foo", 18);		// { name: 'foo', age: 18 }
const p2 = createPerson("bar", 20);		// { name: 'bar', age: 20 }
```

工厂模式解决了创建多个类似对象的问题，但是没有解决对象的**标识**问题。也就是说新创建的对象是什么类型还是不知道。

## 2 构造函数模式

构造函数用于创建**特定类型**的对象。像 Object 和 Array 这样的原生构造函数可以直接使用。也可以自定义构造函数，以函数的形式为自己的对象定义函数和方法。

```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
}
const p1 = new Person("foo", 18);	// Person { name: 'foo', age: 18 } 
const p2 = new Person("bar", 20);	// Person { name: 'bar', age: 20 }
```

使用 Person() 构造函数代替 createPerson() 工厂函数。实际上，Person() 内部的代码和 createPerson() 基本一样，区别如下：

1. 没有显式创建对象。
2. 属性和方法直接赋值给了 this。
3. 没有 return。

按照惯例，作为构造函数的函数名首字母大写 ，非构造函数以小写开头。这有助于区分构造函数和普通函数。

### 2.1 创建实例

要创建一个对象类型的实例，应使用 new 操作符，后面跟上构造函数。以这种形式调用构造函数会执行以下操作：

1. 在内存中创建一个新的对象。
2. **这个对象内部的`[[Prototype]]`属性被赋值为构造函数的 `prototype` 属性**。
3. **构造函数内部的 this 被赋值为这个新对象。即 this 指向新对象**。
4. 执行构造函数内部的代码。即给新对象添加属性。
5. 如果构造函数没有返回值，则返回该对象。

上个例子中，p1 和 p2 分别保存着 Person 的一个实例。这两个对象都有一个 constructor 属性指向 Person：

```javascript
console.log(p1.constructor === Person);
console.log(p2.constructor === Person);
```

constructor 本来是用来标识对象类型的。不过一般认为 instanceof 操作符是确定对象类型更为靠谱的方式。前面的两个对象都是 Object 的实例，同时也是 Person 的实例。

```javascript
console.log(p1 instanceof Person);	// true
console.log(p1 instanceof Object);	// true
```

构造函数不一定写成函数声明的形式。赋值给变量的函数表达式也可以表示构造函数。

```javascript
let Person = function (age, name) {
  this.age = age;
  this.name = name;
};
const p = new Person(18, "foo");	// Person { age: 18, name: 'foo' }
```

实例化时，如果不想传递参数，那么构造函数后面的括号可以省略。只要有 new 操作符，就可以调用相应的构造函数。

```javascript
let Person = function () {
  this.age = 18;
  this.name = 'foo';
};
const p = new Person;	// 不传参，可以省略构造函数后面的括号
```

### 2.2 构造函数也是函数

构造函数和普通函数唯一的区别就是调用的方式不同。任何函数只要使用 new 操作符去调用就是构造函数，不使用 new 操作符调用的函数就是普通函数。

### 2.3 构造函数的问题

构造函数解决了对象标识的问题，但是构造函数也不是没有问题。构造函数的问题在于，其定义的方法会在每一个对象实例上都创建一遍，这些方法**不是**指向**同一个** Function 实例。不同实例上的函数虽然同名但是不相等。

```javascript
let Person = function () {
  this.sayName = function () {
    console.log("foo");
  };
};
const p1 = new Person();
const p2 = new Person();
console.log(p1.sayName === p2.sayName);	// false
```

因为都是做同一件事，因此没必要定义两个不同的 Function 实例。可以将函数定义移到构造函数**外**来解决这个问题。

```javascript
let sayName = function () {		// 在构造函数外定义方法
  console.log("foo");			
};
let Person = function () {		
  this.sayName = sayName;		// 构造函数内部引用这个方法
};
```

这种方式虽然解决了相同逻辑的函数重复定义的问题，但是构造函数内部使用的方法泄露到了构造函数外面。这个问题可以通过**原型模式**来解决。

## 3 原型模式

每个**函数**都会创建一个 prototype 属性，这个属性值是一个对象，包含应该由特定引用类型的实例**共享**的属性和方法。这个对象就是通过调用构造函数创建的对象的**原型**。

使用原型对象的好处是，在它上面定义的属性和方法可以被对象实例**共享**。原来在构造函数中直接赋值给对象实例的值，可以直接赋值给它们的原型。

```javascript
function Person() {}
Person.prototype.name = "foo";
Person.prototype.sayName = function () {
  console.log(this.name);
};
const person1 = new Person();
const person2 = new Person();

console.log(person1.name);  // foo
person1.sayName();  // foo

console.log(person2.name);  // foo
person2.sayName();  // foo

console.log(person1.name === person2.name);			// true
console.log(person1.sayName === person2.sayName);	// true
```

### 3.1 理解原型

#### 3.1.1 原型对象身上的 constructor 属性

只要创建一个函数，就会为这个函数创建一个 prototype 属性，这个属性指向该函数的原型对象。所有的原型对象自动获得一个名为 constructor 的属性，指回与之关联的构造函数。

```javascript
console.log(Person.prototype.constructor === Person); // Person
```

#### 3.1.2 原型链

每次调用构造函数创建一个实例，这个实例内部的`[[Prototype]]` 属性都会被赋值为构造函数的**原型对象**。

>ECMAScript 中没有访问这个`[[Prototype]]`特性的标准方法，但是浏览器会在每个对象上暴露`__proto__`这个属性。通过这个属性可以访问到对象的原型。

也就是说，**实例的`__proto__`上存放着构造函数的`prototype`**：

```javascript
function Person() {}
let person = new Person();

console.log(person.__proto__ === Person.prototype);		// true
```

而构造函数的 `prototype` 属性值同样也是一个对象实例。**构造函数的 `prototype` 的 `__proto__`属性指向Object 构造函数的 `prototype`属性**。

```javascript
console.log(Person.prototype.__proto__ === Object.prototype);	// true
```

同样的，Object.prototype 也是一个对象实例。Object.prototype 也有`__proto__`属性，但是它的属性值是 `null`。这就是原型链的终点。也就是说，**Object 原型的原型是 `null`**。

```javascript
console.log(Object.prototype.__proto__ === null);
```

#### 3.1.3 同一构造函数的不同实例共享同一原型对象

同一个构造函数创建的两个实例，共享同一原型对象。

```javascript
function Person() {}
let p1 = new Person();
let p2 = new Person();
console.log(p1.__proto__ === p2.__proto__);		// true
```

#### 3.1.4 使用 instanceof 检查实例的原型链中是否包含指定构造函数的原型

 使用 instanceof 可以检查**实例的原型链中是否包含指定构造函数的原型**。

```javascript
// p1 的原型链中包含 Person 构造函数的原型
// 因此 instanceof 操作符返回 true
console.log(p1.__proto__ === Person.prototype);
console.log(p1 instanceof Person);	// true

// p1 的原型链中包含 Object 构造函数的原型
// 因此 instanceof 操作符返回 true
console.log(p1.__proto__.__proto__ === Object.prototype);
console.log(p1 instanceof Object);	// true
```

#### 3.1.5 注意事项

1. 原型对象包含 constructor 属性和其它后添加进来的属性。（原型对象的 constructor 属性指回构造函数）
2. 如果在对象身上没有找某个属性，那么就会去沿着原型链去它的原型中去查找。

#### 3.1.6 isPrototypeOf() 验证实例的原型对象

因为不是所有的实现都对外暴露了`[[Prototype]]`，因此 ECMAScript 提供了 isPrototypeOf() 方法。

isPrototypeOf() 方法用来验证一个对象是不是实例的原型对象。

```javascript
// 因为 p1.__proto__ 指向了 Person.prototype，
// 也就是说 Person.prototype 是 p1 的原型对象，
// 因此返回 true。
console.log(Person.prototype.isPrototypeOf(p1));	// true
```

#### 3.1.7 Object.getPrototypeOf() 返回 [[Prototype]] 的值

因为不是所有的实现都对外暴露了`[[Prototype]]`，因此 ECMAScript 提供了 Object.getPrototypeOf() 方法。

Object.getPrototypeOf() 方法可以返回参数的内部属性` [[Prototype]] `的值，也就是浏览器实现的`__proto__`属性值。

```javascript
console.log(Object.getPrototypeOf(p1) === p1.__proto__); 	//true
```

使用 Object.getPrototypeOf() 可以方便地取得一个对象的原型。

#### 3.1.8 Object.setPrototypeOf() 重写实例的原型对象

Object.setPrototypeOf() 方法可以向实例的私有属性`[[Prototype]]`写入一个新值。这样就可以重写一个对象的原型继承关系。

```javascript
function Person() {}
const p = new Person();

console.log(Person.prototype.isPrototypeOf(p)); // true

const obj = {};
Object.setPrototypeOf(p, obj);					// 重写实例的原型对象
console.log(Person.prototype.isPrototypeOf(p)); // false
console.log(obj.isPrototypeOf(p));              // true
```

> **注意**：Object.setPrototypeOf() 严重影响代码性能。

#### 3.1.9 Object.create() 创建新对象并指定原型

为了避免 Object.setPrototypeOf() 造成的性能下降，可以通过 Object.create() 来创建一个新对象，同时指定原型。

```javascript
const obj = {
  name: "foo",
};

let person = Object.create(obj);
console.log(person.name);				// foo
console.log(person.__proto__ === obj);	// true
```

### 3.2 原型层级

在通过对象访问属性时，会从对象实例本身开始搜索。如果在这个实例上发现了给定的属性名，则返回该属性值。如果没有找到这个属性，则搜索会沿着指针进入**原型对象**，然后在原型对象上找到属性后再返回对应的值。找到属性后，搜索**停止**，不会再沿着原型链继续搜索。

```javascript
function Person() {
  this.name = "foo";
}
Person.prototype.age = 18;
const person = new Person();
console.log(person.name);		// 这个属性位于对象实例身上
console.log(person.age);		// 这个属性位于对象原型上
```

#### 3.2.1 hasOwnProperty() 返回是否为自有属性

hasOwnProperty() 方法用于确定某个属性是在实例上还是在原型对象上。在属性存在于调用它的对象实例上返回 true。

```javascript
function Person() {
  this.name = "foo";
}
const person = new Person();
console.log(person.hasOwnProperty("name"));	// true
```

#### 3.2.2 Object.getOwnPropertyDescriptor() 获取属性描述符

```javascript
function Person() {
  this.name = "foo";
}
const person = new Person();
console.log(Object.getOwnPropertyDescriptor(person, "name"));
// { value: 'foo', writable: true, enumerable: true, configurable: true }
```

Object.getOwnPropertyDescriptor() 方法只对**实例身上**的属性（自有属性）有效。要取得原型属性的描述符，就必须在原型对象上调用Object.getOwnPropertyDescriptor()。

### 3.3 原型和 in 操作符

有两种方法使用 in 操作符：单独使用和在 for-in 循环中使用。

#### 3.3.1 单独使用 in 操作符

单独使用时，in 操作符会在可以通过对象访问指定属性时返回 true，无论该属性是在**实例**上还是在**原型**上。

```javascript
function Person() {
  this.name = "foo";
}
Person.prototype.age = 18;
const person = new Person();
console.log("name" in person);		// 属性在实例上
console.log("age" in person);		// 属性在原型上
```

可以使用 in 操作符和 hasOwnProperty() 可以确定一个属性是否在原型上。如果一个属性可以通过 in 操作符返回 true，并且 hasOwnProperty() 返回 false，那么这个属性就在原型上。

```javascript
function Person() {}
Person.prototype.age = 18;
const person = new Person();

// 属性在原型上
console.log("age" in person && !person.hasOwnProperty("age"));	// true
```

#### 3.3.2 在 for-in 循环中使用 in 操作

##### in 返回实例身上和原型身上的可枚举属性

在 for-in 循环中使用 in 操作符时，可以通过对象访问且可以被枚举的属性都会被返回，包括**实例属性**和**原型属性**。

```javascript
function Person() {
  this.name = "foo";
}
Person.prototype.age = 18;
const p = new Person();
for (const key in p) {
  console.log(key, p[key]);		// [ 'name' ]
}
```

##### Object.keys() 只返回实例身上的可枚举属性

要想获得实例身上所有的有可枚举属性，而不带上原型身上的可枚举属性，可以使用 Object.keys() 方法。这个方法接收一个对象作为参数，返回对象身上所有的可枚举属性名组成的数组。

```javascript
function Person() {
  this.name = "foo";
}
Person.prototype.age = 18;
const p = new Person();
p.addr = "bar";
console.log(Object.keys(p));	// [ 'name', 'addr' ]
```

##### Object.getOwnPropertyNames() 返回实例身上所有属性（不包括符号），无论是否可枚举

如果想列出实例身上所有的属性（不包括符号），无论是否可枚举，可以使用 Object.getOwnPropertyNames()。

```javascript
function Person() {
  this.name = "foo";
}
Person.prototype.age = 18;
const p = new Person();
console.log(Object.getOwnPropertyNames(p.__proto__));
// [ 'constructor', 'age' ]
// p.__proto__ 身上的不可枚举属性 constructor 也被列举出来了
```

##### Object.getOwnPropertySymbols() 返回实例身上的符号属性

```javascript
const obj = {
  [Symbol("foo")]: "foo",
  [Symbol("bar")]: "bar",
};
console.log(Object.getOwnPropertySymbols(obj));
// [ Symbol(foo), Symbol(bar) ]
```

### 3.4 枚举属性顺序

#### 枚举顺序不确定的方法：

for-in 循环、Object.keys() 枚举的顺序不确定。

#### 枚举顺序确定的方法：

Object.getOwnPropertyNames()、Object.getOwnPropertySymbols()、Object.assign() 的顺序是确定的。先以升序枚举数值键，然后以插入顺序枚举字符串和符号键。**对象字面量**定义的键以它们逗号分隔的顺序插入。

### 3.4 对象迭代

ECMAScript 2017 中新增了两个静态方法，用于将对象内容转化为序列化的、可迭代的格式。Object.values() 和 Object.entires() 接收一个对象，返回一个数组。Object.values() 返回对象值的数组，Object.entires() 返回键值对的数组。这两个方法都只会返回对象**实例身上**的属性，不会返回原型对象身上的属性。

```javascript
function Person() {
  this.name = "foo";
}
Person.prototype.age = 18;
const p = new Person();
p.addr = "bar";
console.log(Object.values(p));		// [ 'foo', 'bar' ]
console.log(Object.entries(p));		// [ [ 'name', 'foo' ], [ 'addr', 'bar' ] ]
```

### 3.5 对象字面量重写原型

#### 3.5.1 使用对象字面量重写原型

为了减少冗余和从视觉上更好地封装原型功能，可以使用对象字面量来重写原型。

```javascript
function Person() {}
Person.prototype = {
  name: "foo",
  addr: "bar",
  age: 18,
};
```

#### 3.5.2 重写原型带来的 constructor 问题

这样重写之后 Person.prototype 之后，Person.prototype 的 constructor 属性不再指向 Person 构造函数。而是沿着原型链找到 `Person.prototype.__proto__.constructor` 也就是 Object 构造函数。

```javascript
console.log(Person.prototype.constructor === Person.prototype.__proto__.constructor);
```

虽然此时 instanceof 操作符还能可靠地返回值，但是已经不能依靠 constructor 属性来识别类型了。

```javascript
function Person() {}
Person.prototype = {
  name: "foo",
  addr: "bar",
  age: 18,
};
const p = new Person();
console.log(p.__proto__ === Person.prototype); 	// true
console.log(p instanceof Person); 				// true
console.log(p.__proto__.constructor);			// [Function: Object]
```

#### 3.5.3 重写 constructor 

如果 constructor 的值非常重要，可以在重写原型时同时指定 constructor。

```javascript
function Person() {}

Person.prototype = {
  constructor: Person,	// 重写原型时指定 constructor
  name: "foo",
  addr: "bar",
  age: 18,
};
```

此时的 constructor 属性是可枚举的，如果想让 constructor 恢复成原生的**不可枚举**的属性，可以使用Object.defineProperty()。

```javascript
function Person() {}

Person.prototype = {
  name: "foo",
  addr: "bar",
  age: 18,
};

Object.defineProperty(Person.prototype, "constructor", {
  enumerable: false,	// 默认也是不可枚举
  value: Person,
});
```

### 3.6 原型的动态性

虽然，随时能给原型添加属性和方法，并能够立即反映在所有对象实例上，但这和重写这个原型对象是两码事。实例的`[[Prototype]]`指针是在调用构造函数时自动赋值的，这个指针即使把原型修改为不同的对象也不会变。

**重写整个原型会切断最初原型和构造函数之间的关系，但实例引用的还是最初的原型**。

```javascript
function Person() {}
const p = new Person();
Person.prototype = {
  name: "foo",
};
console.log(p.name);		// undefined 
```

重写构造函数上的原型之后创建的实才会引用新的原型。而在此之前创建的实例仍然会引用最初的原型。

### 3.7 原生对象原型

原型模式之索引重要，不仅体现在自定义类型上，而且因为它也是实现所有原生引用类型的模式。所有原生引用类型的构造函数（比如：Object、Array、String 等）都在原型上定义了实例方法。

```javascript
console.log(Array.prototype.sort);			// [Function: sort]
console.log(String.prototype.substring);	// [Function: substring]
```

通过原生对象的原型可以取得所有默认方法的引用，也可以给原生实例定义新的方法（但不建议这样做）。
