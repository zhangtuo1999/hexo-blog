---
title: 【JavaScript红宝书】7.变量、作用域
tags:
  - 前端
  - JavaScript
keywords: 'JavaScript,'
description: 《JavaScript高级程序设计（第4版）》第4章：变量、作用域和内存。主要内容：原始值、引用值、instanceof、作用域链、变量声明
excerpt: 《JavaScript高级程序设计（第4版）》第4章：变量、作用域和内存。主要内容：原始值、引用值、instanceof、作用域链、变量声明
abbrlink: 271687d2
date: 2022-04-30 21:01:42
index_img: ../images/封面图/红宝书.png
---

# 变量、作用域

## 1 原始值和引用值

原始值：Undefined、Null、Boolean、Number、BigInt、String 和 Symbol 值。

引用值：Object 对象值。

保存原始值的变量是**按值访问**的，保存引用值的变量是**按引用访问**的。

### 1.1 动态属性

对于引用值，可以随时添加、修改和删除其属性和方法。原始值不能拥有属性，尽管给原始值添加属性不会报错。

### 1.2 复制值

复制原始值时，原始值会被复制到新变量的位置。

复制引用值时，储存在变量中的值也会被复制到新变量所在的位置，只不过这里的值是对象的引用。因此，在一个对象上面发生的变化也会在另一个对象上表现出来。

### 1.3 传递参数

#### 1.3.1 按值传参和按引用传参的区别

1. 按值传参：值会被复制到一个局部变量。C++ 例子：

   ```c++
   #include<iostream>
   using namespace std;
   void fn(int a){
   	a = 200; 
   }
   int main(){
   	int a = 1;
   	fn(a);	
   	cout<<a<<endl;		// 1
   } 
   ```

2. 按引用传参：值在内存中的位置会被复制到一个局部变量。这意味着，对本地变量的修改会反映到函数的外部。C++ 例子：

   ```c++
   #include<iostream>
   using namespace std;
   void fn(int &a){
   	a = 200; 
   }
   int main(){
   	int a = 1;
   	fn(a);	
   	cout<<a<<endl;		// 200
   } 
   ```

#### 1.3.2 JavaScript 中按值传参

**ECMAScript 中所有函数的参数都是按值传递的**。**变量有按值访问和按引用访问，但是传参只有按值访问**。

传递原始值时，看上去比较明显：

```javascript
let a = 1;
function fn(a) {
    a = 2;
}
fn(a);
console.log(a);   // 1
```

传递的是对象的话：

```javascript
let obj = { a: 1 };
function fn(o) {
    o.a = 2;
}
fn(obj);
console.log(obj.a);	// 2
```

我们创建了一个对象并保存在`obj`变量中。然后这个变量被传递给`fn()` 函数，并被复制到参数`o`中。在函数内部，`obj`和`o`都指向同一个对象。因此`o`对对象的改变也会反应到`obj`上。

并不是说，只要当局部作用域中修改对象而变化反应到全局时，就意味着参数是按引用传递的。为证明 ECMAScript 中，**对象是按值传递**的，看下面的例子：

```javascript
let obj = { a: 1 };
function fn(o) {
    o.a = 2;
    o = new Object();
    o.a = 3;
}
fn(obj);
console.log(obj.a);	// 2
```

如果是按引用传递， `obj.a`最后结果应该是 3 而不是 2。这表明函数参数的值改变后，原始的引用仍没有改变。当`o`在函数内部被重写时，它变成了指向本地对象的指针，随后在函数执行结束时销毁。

### 1.4 确定类型

#### 1.4.1 typeof 操作符

适合用来判断字符串、数值、布尔值、undefined。如果值是对象或者 null，那么都会返回`"object"`（如果是函数则返回`"function"`）。

#### 1.4.2 instanceof 操作符

如果变量是给定引用类型的实例则 instanceof 操作符返回 true。

```javascript
console.log({} instanceof Object);  // true
```

注意：

1. 所有引用值都是 Object 的实例，因此通过 instanceof 操作符检测引用值和 Object 构造函数都返回 true。
2. 如果使用 instanceof 检测原始值，则始终返回 false。

## 2 执行上下文和作用域

执行上下文简称**上下文**，变量和函数的上下文决定了它们可以访问哪些数据，以及它们的行为。

1. 全局上下文就是最外层的上下文。宿主不一样，全局上下文可能不一样。每个上下文都有一个**变量对象**，上下文中所有的变量都存在这个对象身上。
1. 在浏览器中，全局上下文是 window 对象。在全局作用域中，使用 var 定义的变量和函数都会成为 window 对象身上的属性，而 const 和 let 不会，但是在作用域链中的解析是一样的。
2. 上下文在代码执行完毕后会被销毁。包括里面定义的变量和函数。
3. 每个函数都有自己的上下文，函数参数被当作上下文中的变量。
4. 上下文代码执行时，会创建变量对象的**作用域链**。作用域链决定了各级上下文代码在访问变量和函数时的顺序。代码执行时的标识符解析是通过沿着作用域链**逐级搜索**标识符完成的。

### 2.1 作用域链增强

有其它方式来增强作用域链。某些语句会导致作用域链前端临时加上一个上下文：

1. try/catch 语句的 catch 块。catch 语句会创建一个新的**变量对象**，包含要抛出的错误对象的声明。
2. with 语句。向作用域前端添加指定的对象。

### 2.2 变量声明

#### 2.3.1 使用 var 的函数作用域声明

1. 使用 var 声明变量时，变量会自动加到最接近的上下文中（而不是全局上下文）。

2. 在函数中，最接近的上下文就是函数的局部上下文。在 with 语句中，最接近的上下文也是函数上下文。

   ```javascript
   function add(num1, num2) {
   	var sum = num1 + num2;
   	return sum;
   }
   add(10, 20);
   console.log(sum);		// 报错
   ```

3. 如果变量未经声明便被初始化，那么它会自动加到全局上下文中。

   ```javascript
   function add(num1, num2) {
       sum = num1 + num2;
       return sum;
   }
   add(10, 20);
   console.log(sum);		// 30
   ```

4. var 存在声明提升，可以多次声明不会报错。

#### 2.3.2 使用 let 的块级作用域声明

1. 块级作用域由最近的一对儿花括号`{}`界定。if 块、while 块、function 块和单独的块都是 let 声明的作用域。
2. let 不能在同一作用域内声明两次。
3. let 也会被提升，但是由于存在暂时性死区。实际上，不能在声明之前使用 let 变量。

#### 2.3.3 使用 const 的常量声明

1. 使用 const 声明的变量必须同时初始化。一经声明在声明周期内都不能再重新赋值。

2. 赋值为对象的 const 变量不能再被重新赋值为其它引用值，但对象的键不受限制。

   ```javascript
   const obj = {};
   obj = { a: 1 };		// 报错
   obj.a = 1;
   console.log(obj);	// {a:1}
   ```

3. 如果想让整个对象都不能修改，可以使用 `Object.freeze()`。这样再给属性赋值时，虽然不会报错但是会**静默失败**。

   ```javascript
   const obj = Object.freeze({});
   obj.a =1			// 不报错，静默失败
   console.log(obj);	// {}
   ```

#### 2.3.4 标识符查找

当在特定上下文中引用一个标识符时，必须沿着作用域链搜索确定这个标识符是什么。这个过程一直持续到搜索至全局上下文的变量对象。
