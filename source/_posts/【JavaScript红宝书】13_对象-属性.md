---
title: 【JavaScript红宝书】13.对象-属性
tags:
  - 前端
  - JavaScript
keywords:
  - JavaScript
description: 《JavaScript高级程序设计（第4版）》第8章：对象、类和面向对象编程。主要内容：对象。
excerpt: 《JavaScript高级程序设计（第4版）》第8章：对象、类和面向对象编程。主要内容：对象。
abbrlink: 2d79b123
date: 2022-05-07 16:33:12
index_img: ../images/封面图/红宝书.png
---

# 理解对象

ECMAScript 中对象是一组属性的无序集合。可以将对象看作一张散列表，其中的内容是一组**名/值对**，值可以是数据或者函数。

创建自定义对象的通常方法是创建 Object 的一个新实例，然后给它添加属性和方法。

```javascript
const obj = new Object();
obj.a = 1;
obj.fn = function () {
  console.log(this.a);
};
```

或者使用**对象字面量**的方式来创建新对象。

```javascript
const obj = {
  a: 1,
  fn: function () {
    console.log(this.a);
  },
};
```

## 1 Object.defineProperty() 定义属性

ECMAScript 中使用一些**内部特性**来描述属性的特征，开发者不能直接访问这些特性。为了表述某个属性是内部属性，应该用**两个中括号**括起来。

### 1.1 数据属性

**数据属性**包含一个保存数据值的位置。数据属性有4个特性来描述它们的行为。

1. `[[Configurable]]`：表示属性是否可以通过 delete 删除并重新定义、是否可以修改它的特性以及是否可以把它修改为访问器属性。默认情况下是 true。（一个属性如果被定为不可配置，就不可能变回可配置的了）
2. `[[Enumerable]]`：表示属性是否可以通过 for-in 循环返回。默认情况下是 true。
3. `[[Writable]]`：表示属性的值是否可以被修改。默认情况下是 true。
4. `[[Value]]`：包含属性的实际值。这是读取和写入属性值的位置。默认值是 undefined。

> **注意**：将属性像前面那样**显式添加到对象**后，`[[Configurable]]`、`[[Enumerable]]`、`[[Writable]]`都会被设置为 **true**，`[[Value]]`会被设置为指定的值。

要修改属性的默认属性，就必须使用 Object.defineProperty() 方法。这个方法接收三个参数：要给其添加属性的对象、属性的名称和一个**描述符对象**。描述符对象上的属性可以包含 configurable、enumerable、writable 和 value，和相关特性的名称一一对应。

> **注意**：使用 Object.defineProperty() 定义或者修改属性时，如果描述符对象没有指定 configurable、enumerable 和 writable 属性值，那么默认是**false**。 

```javascript
const obj = {};
Object.defineProperty(obj, "a", {
  writable: false,		// 添加a属性，并设置为不可修改
  value: 1,
});
console.log(obj.a); 	// 1
obj.a = 2;				// 尝试修改a属性，不报错但是静默失败
console.log(obj.a); 	// 1
```

### 1.2 访问器属性

访问器属性不包含属性值，相反包含一个获取函数（Getter）和设置函数（Setter），不过这个两个函数都不是必须的。在**读取**访问器属性时，会调用**获取函数**，这个函数的责任是返回一个有效值。在**写入**访问器属性时，会调用**设置函数**并传入新值，这个函数的责任是对数据进行修改。访问器属性有4个特性描述它们的行为。

1. `[[Configurable]]`：表示属性是否可以通过 delete 删除并重新定义、是否可以修改它的特性以及是否可以把它修改为数据属性。默认情况下是 true。
2. `[[Enumerable]]`：表示属性是否可以通过 for-in 循环返回。默认情况下是 true。
3. `[[Get]]`：获取函数。默认值为 undefined。
4. `[[Set]]`：设置函数。默认值为 undefined。

在**已有对象**的情况下，访问器属性**不能直接定义**，必须使用 Object.defineProperty()。

```javascript
const book = {
  year_: 2022,	// 伪内部属性
  edition: 1,
};
Object.defineProperty(book, "year", {
  get() {
    return this.year_;
  },
  set(newVal) {
    this.year_ = newVal;
    this.edition += newVal - 2022;
  },
});

book.year = 2030;
console.log(book.edition);
```

year_ 中的**下划线**常用来表示该属性不希望在对象方法外部被访问。year 被定义为一个访问器属性，其获取函数会返回 year_ 的值，设置函数会修改 year_ 并计算 edition 的值。这是访问器属性的典型使用场景，即**设置一个属性值会导致一些其它的变化**。

获取函数和设置函数不用都定义。只定义获取函数意味着属性是只读的，只定义设置函数意味着属性不能读取。

## 2 Object.defineProperties() 定义多个属性

Object.defineProperties() 方法可以通过多个描述符在一个对象上一次定义多个属性。这个方法接收两个参数：要为之添加或者修改的对象和描述符对象。

```javascript
const book = {};
Object.defineProperties(book, {
  year_: {
    value: 2022,
  },
  edition: {
    value: 1,
  },
  year: {
    get() {
      return this.year_;
    },
    set(newVal) {
      this.year_ = newVal;
      this.edition += newVal - 2022;
    },
  },
});
```

这个例子定义的对象和前面的相同，只是这个对象的属性是一次同时定义的。

## 3 读取属性的特性

### 3.1 Object.getOwnPropertyDescriptor()

Object.getOwnPropertyDescriptor() 方法可以取得指定属性的**属性描述符**。这个方法接收两个参数：属性所在的对象和属性名。返回值是一个对象。

```javascript
const descriptor1 = Object.getOwnPropertyDescriptor(book, "year");
console.log(descriptor1);
// {
//  get: [Function: get],
//  set: [Function: set],
//  enumerable: false,
//  configurable: false
// }

const descriptor2 = Object.getOwnPropertyDescriptor(book, "year_");
console.log(descriptor2);
// {
//  value: 2022,
//  writable: false,
//  enumerable: false,
//  configurable: false
// }
```

###  3.2 Object.getOwnPropertyDescriptors()

Object.getOwnPropertyDescriptors() 会在每个属性上调用 Object.getOwnPropertyDescriptor()，并在一个新对象中返回它们。

```javascript
const descriptors = Object.getOwnPropertyDescriptors(book);
console.log(descriptors);
//{
//  year_: {
//    value: 2022,
//    writable: false,
//    enumerable: false,
//    configurable: false
//  },
//  edition: { value: 1, writable: false, enumerable: false, configurable: false },
//  year: {
//    get: [Function: get],
//    set: [Function: set],
//    enumerable: false,
//    configurable: false
//  }
//}
```

## 4 Object.assign() 合并对象

合并（merge）或者混入（mixin），指的是把源对象的属性一起复制到目标对象上。目标对象通过混入源对象的属性得到了增强。

ES6 中专门为合并对象提供了 Object.assign() 方法。这个方法接收一个目标对象和一个或者多个源对象作为参数，然后将每个源对象中**可枚举**的**自有**属性复制到目标对象。[`String`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String)类型和 [`Symbol`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol) 类型的属性都会被拷贝。

Object.assign() 修改目标对象，并且返回修改后的**新**目标对象。

简单复制：

```javascript
const dest = {};
const src = { a: 1 };
Object.assign(dest, src);
console.log(dest);      // { a: 1 }
```

多个源对象：

```javascript
const dest = {};
const src1 = { a: 1 };
const src2 = { b: 2 };
Object.assign(dest, src1, src2);
console.log(dest);		// { a: 1, b: 2 }
```

对于每个符合条件的属性，这个方法会使用源对象上的`[[Get]]`取得属性的值，然后使用目标对象上的`[[Set]]`设置属性的值。Getter 和 Setter：

```javascript
const dest = {
  set a(val) {
    console.log(`setter ${val}`);
  },
};

const src = {
  get a() {
    console.log(`getter`);
    return "foo";
  },
};

Object.assign(dest, src);
console.log(dest);

// getter
// setter foo
// { a: [Setter] }
```

如果多个源对象有相同的属性，则使用**最后一个**复制的值。覆盖属性：

```javascript
dest = { a: 1 };
Object.assign(dest, { a: 2 }, { a: 3 });
console.log(dest);		// { a: 3 }
```

Object.assign() 实际上对每个源对象执行的是**浅复制**，意味着只会复制对象的引用。浅复制：

```javascript
const dest = {};
const obj = { a: {} };
Object.assign(dest, obj);		// 浅复制意味着只会复制对象的引用
console.log(dest.a === obj.a);	// true
```

如果复制期间出错，操作会**停止并退出**，同时抛出错误。此时，Object.assign() 不会回滚，只会完成部分复制。

```javascript
const dest = {};
const src = {
  a: 1,
  get b() {
    throw new Error();
  },
};
try {
  Object.assign(dest, src);
} catch (e) {}

console.log(dest);  // { a: 1 }
```

## 5 Object.is() 判定对象相等

### 5.1 使用 === 操作符

符合预期的情况：

```javascript
console.log(true === 1);	// false
console.log({} === {});		// false
console.log("2" === 2);		// false
```

被认为是相等的情况：

```javascript
console.log(+0 === -0);		// true
console.log(+0 === 0);		// true
console.log(-0 === 0);		// true
```

确定 NaN 的相等性需要使用 isNaN()：

```javascript
console.log(NaN === NaN);	// false
console.log(isNaN(NaN));	// true
```

### 5.2 使用 Object.is() 判定相等

```javascript
console.log(Object.is(true, 1));	// false 
console.log(Object.is({}, {}));		// false
console.log(Object.is("2", 2));		// false
```

正确的0、-0、+0 相等/不相等判定：

```javascript
console.log(Object.is(+0, -0));		// false
console.log(Object.is(+0, 0));		// true
console.log(Object.is(-0, 0));		// false
```

正确的 NaN 判定：

```javascript
console.log(Object.is(NaN, NaN));	// true
```

要检查超过两个值，递归利用相等性传递即可。

```javascript
function recursivelyCheckEqual(x, ...rest) {
  return (
    Object.is(x, rest[0]) && (rest.length < 2 || recursivelyCheckEqual(...rest))
  );
}
```

## 6 增强对象语法

ES6 新增的语法糖。

### 6.1 属性名简写

给对象添加属性时，属性名和变量名经常是一致的：

```javascript
const name = "foo";
const obj = {
  name: name,		// 属性名和变量名一致
};
console.log(obj); 	// { name: 'foo' }
```

属性名简写**只需要使用变量名**，就可以被解释为同名的属性键：

```javascript
const name = "foo";
const obj = {
  name,				// 属性名简写
};
console.log(obj);	
```

### 6.2 可计算属性

引入可计算属性之前，如果想使用变量的值作为属性，那么必须先声明变量，然后再使用中括号语法来添加属性。也就是不能在对象字面量中直接动态命名属性。

```javascript
const a = "a";
const b = "b";
const c = "c";
const obj = {};
obj[a] = 1;
obj[b] = 2;
obj[c] = 3;
console.log(obj);	// { a: 1, b: 2, c: 3 }
```

有了可计算属性后，可以直接在对象字面量中完成**动态属性赋值**。中括号包围的对象属性键告诉 JavaScript 将其作为 **JavaScript 表达式**而不是字符串求值。

```javascript
const a = "a";
const b = "b";
const c = "c";
const obj = {
  [a]: 1,
  [b]: 2,
  [c]: 3,
};
console.log(obj);	// { a: 1, b: 2, c: 3 }
```

注意：可计算属性表达式中抛出任何错误都会终止对象的创建。且不能回滚。

### 6.3 方法名简写

给对象定义方法时，通常需要一个方法名、冒号然后引用一个匿名函数表达式。

```javascript
let person = {
  sayName: function () {
    console.log("foo");
  },
};
person.sayName()		// foo
```

简写形式：

```javascript
let person = {
  sayName() {			// 简写形式
    console.log("foo");	
  },
};
person.sayName();		// foo
```

方法名的简写同样适用于获取函数和设置函数。

```javascript
const person = {
  name_: "foo",
  get name() {			// 获取函数的简写形式
    return this.name_;
  },
  set name(newVal) {	// 设置函数的简写形式
    this.name_ = newVal;
  },
  sayName() {
    console.log(this.name_);
  },
};

person.name = "bar";
person.sayName();		// bar
console.log(person);	// { name_: 'bar', name: [Getter/Setter], sayName: [Function: sayName] }
```

## 7 对象解构

ES6 新增了对象解构语法，可以在一条语句中使用嵌套数据实现一个或者多个赋值操作。简单的说，对象解构就是使用与对象匹配的结构来实现对象属性的赋值。

### 7.1 解构赋值

不使用解构操作：

```javascript
let person = {
  name: "foo",
  age: 18,
};
let personName = person.name;
let personAge = person.age;
console.log(personName);		// foo
console.log(personAge);			// 18
```

使用对象解构：

```javascript
let person = {
  name: "foo",
  age: 18,
};
let { name: personName, age: personAge } = person;	// 变量的解构赋值
console.log(personName);		// foo
console.log(personAge);			// 18 
```

如果想让变量直接使用属性的名称，还可以使用**简写语法**。

```javascript
let person = {
  name: "foo",
  age: 18,
};
let { name, age } = person;		// 变量解构赋值+简写语法
console.log(name);				// foo	
console.log(age);				// 18 
```

解构赋值不一定与原对象的属性匹配。赋值的时候可以**忽略某些属性**，如果引用的属性不存在，则该变量的值是undefined。

```javascript
let person = {
  age: 18,						// 对象中没有name这个属性
};
let { name, age } = person;		// 变量解构赋值
console.log(name);				// 此时的name是undefined
```

也可以在解构赋值的同时定义默认值，适用于上述引用的属性中不存在于原对象中的情况。

```javascript
let person = {
  age: 18,
};
let { name = "foo", age } = person;		// 变量解构赋值
console.log(name);
console.log(age);
```

null 和 undefined 不能被解构，否则抛出错误。

```javascript
const { foo } = null;			// 报错
const { bar } = undefined;		// 报错
```

解构不要求必须在解构表达式中声明。不过，如果是事先声明的变量赋值，则赋值表达式必须包含在一对括号中。

```javascript
let age, name;
const person = {
  age: 18,
  name: "foo",
};
({ age, name } = person);		// 18
console.log(age, name);			// foo
```

### 7.2 嵌套解构

解构赋值可以使用嵌套解构，以匹配嵌套的属性。

```javascript
let person = {
  name: "foo",
  age: 18,
  job: {
    title: "bar",
  },
};

let {
  job: { title },
} = person;

console.log(title); 	// bar
console.log(job);   	// 报错
```

### 7.3 部分解构

如果一个解构表达式涉及多个赋值，开始的赋值成功而后面的赋值出错，那么整个解构赋值只会完成一部分。

### 7.4 参数列表中使用解构赋值

在函数的参数列表中使用解构赋值。对参数的解构赋值不会影响 arguments 对象，但是可以在函数签名中声明在函数体内使用的局部变量。

```javascript
let person = {
  name: "Tourry",
  age: 18,
};
function fn(foo, { name, age }, bar) {
  console.log(arguments);
  console.log(name, age);
}
function fn2(foo, { name: personName, age: personAge }, bar) {
  console.log(arguments);
  console.log(personName, personAge);
}

fn("a", person, "b");
// [Arguments] { '0': 'a', '1': { name: 'Tourry', age: 18 }, '2': 'b' }
// Tourry 18

fn2("a", person, "b");
// [Arguments] { '0': 'a', '1': { name: 'Tourry', age: 18 }, '2': 'b' }
// Tourry 18
```

## 8 Object.fromEntries() 键值对列表转为对象

Object.fromEntries() 可以将键值对列表转为对象：

```javascript
const obj = Object.fromEntries([['a', 1], ['b', 2]])
console.log(obj)	// { a: 1, b: 2 }
```

