---
title: 【JavaScript】ES6笔记
tags:
  - 前端
  - JavaScript
excerpt: （TODO）ES6 新特性
abbrlink: b2b6ba06
date: 2021-12-03 21:53:32
---

## 1. let 关键字

* let声明的变量不能重复声明（var可以）

  ```js
  let a
  let a	//报错
  
  var b
  var b	//不报错
  ```

* 块级作用域

  ```js
  {
  	let a = 'aaa'
  }
  console.log(a)	//报错
  
  {
      var b = 'bbb'
  }
  console.log(b)	//不报错
  ```

  * 形式：

    * {}

    * if else while for 循环语句

    * let声明的变量也是只在块作用域内有效

  * 不存在变量提升，不允许在变量声明之前使用变量（var可以）

  * 不影响作用域链的效果

## 2. const关键字

* const声明变量必须要赋初值
* 一般来说，常量使用大写
* const声明的常量值不可以修改
* const声明的常量也在块级作用域中
* 对数组和对象的元素修改不算对常量进行修改，不会报错（地址没有发生改变）

## 3. 变量解构赋值

```js
const arr = [1, 2, 3, 4];
let [a, b, c, d] = arr;
console.log(a, b, c, d);	//1 2 3 4

const obj = {
	x: 100,
	y: 200,
	f: function () {
		console.log(this.x);
    },
};

let { x, y, f } = obj;
console.log(x, y, f);	//100 200 fun...
```

## 4. 模板字符串

* 使用反引号声明模板字符串

* 内容中可以直接出现换行符

* 使用`${}`可以进行字符串插值

  ```js
  let a = 'zhangsan'
  let b = `${a} is 18 years old`
  console.log(b)  //zhangsan is 18 years old
  ```

## 5. 简化对象属性值写法

* 定义对象时，如果属性名和变量名相同，属性值就是对应变量的值。那么，在定义变量时，可以在大括号中直接写入变量和函数的名称，作为对象的属性和方法。书写更加简洁。

```js
a = 100
b = 200
f = function (){
    console.log('func')
}
const obj = {
	a,
	b
}
obj.f()		//func
console.log(obj.a,obj.b)	//100 200
```

## 6. 箭头函数

* this是静态的，this始终指向函数声明时所在作用域下的this的值

  ```js
  function getName() {
      console.log(this.name);
  }
  
  let getName2 = () => {
      console.log(this.name);
  };
  
  window.name = "zhangsan";
  
  const obj = {
      name: "tourry",
  };
  
  console.log("--直接调用--");
  
  getName();      //zhangsan
  getName2();     //zhangsan
  
  console.log('--call调用--');
  
  getName.call(obj)   //tourry
  getName2.call(obj)  //zhangsan
  ```

* 不能作为构造函数实例化对象

  ```js
  let Person = (name, age) => {
      this.name = name;
      this.age = age;
  };
  
  //Uncaught TypeError: Person is not a constructor
  let p = new Person("zhangsan", 18);
  ```

* 不能使用arguments变量

  ```js
  let fn = () => {
      console.log(arguments);
  };
  //Uncaught ReferenceError: arguments is not defined
  fn(1,2,3)
  ```

  

* 箭头函数简写

  * 省略小括号，当形参有且只有一个时

    ```js
    let add = n => {
    	return n + 1;
    };
    
    consle.log(add(9));		//10
    ```

  * 省略大括号，当代码块中只有一条语句时。此时的return必须省略，而且语句的执行结果就是函数的返回值

    ```js
    let add = n => n + 1;
    
    console.log(add(9));	//10
    ```

* 箭头函数适合与this无关的回调，比如定时器、数组方法

* 箭头函数不适合与this有关的回调，比如事件的回调，对象方法

## 7. 形参赋初值

* es6允许给函数形参赋初值，具有初始值的形参，一般位置要靠后

* 可以与解构赋值结合使用

  ```js
  function func({ a = 800, b, c, d }) {
      console.log(a, b, c, d);
  }
  obj = { b:200,c:300,d:400 }
  func(obj)	//800 200 300 400
  ```

## 8. rest 参数

* arguments是类数组对象，rest是数组

* rest参数必须放到参数列表中的最后

  ```js
  function func2(...args) {
      console.log(args);
  }
  func2("q", "w", "e", "r");	//['q', 'w', 'e', 'r']
  ```

## 9. 扩展运符

```js
const a = ["q", "w", "e", "r"];
function func(){
    console.log(arguments)
}
func(a)     //只传入一个参数，是一个数组
func(...a)  //传入4个参数
```

```js
//1. 使用扩展运算符来合并数组

const arr1 = ["q", "w"];
const arr2 = ["e", "r"];

const res1 = arr1.concat(arr2);
console.log(res1);	//["q", "w", "e", "r"]
const res2 = [...arr1, ...arr2];
console.log(res2);	//["q", "w", "e", "r"]
```

```js
//2. 拷贝数组，（浅拷贝）

const arr = ['q','w','e']
const copy  = [...arr]
console.log(copy)	//['q','w','e']
```

```js
//3. 将类数组对象转化成真正的数组
function func() {
    console.log(arguments);			//类数组对象
    console.log([...arguments]);	//真数组
}
func("q", "w", "e", "r");
```

## 10. Symbol类型

### 10.1 创建Symbol

* 使用Symbol()创建

  ```js
  let s1 = Symbol()
  let s2 = Symbol('s')
  let s3 = Symbol('s')
  console.log( s2 === s3 )	//false
  ```

* 使用Symbol.for()创建

  ```js
  let s1 = Symbol.for('s')
  let s2 = Symbol.for('s')
  console.log( s1 === s2 )	//true
  ```

### 10.2 特点

* 不能与自己或者其他数据进行运算、比较

### 10.3 用处

* 给对象添加属性，用Symbol类型数据做属性名，可以做到属性名独一无二

# 11. 迭代器 Symbol.iterator

* 使用for-of遍历

* 数组内部有一个属性：属性名是Symbol类型的Symbol.iterator。属性值一个函数，实现了数组的迭代方法。

* 迭代器可以自定义遍历数据

# 12. 生成器

* 生成器是一个特殊的函数
* 一种对异步编程的解决方案

```js
function * generator(){
    console.log('Hello Generator')
}

let iterator = generator()
console.log(iterator)   //generator {<suspended>}

iterator.next()         //Hello Generator
```

```js
function* generator() {
    console.log("q");
    yield "1";
    console.log("w");
    yield "2";
    console.log("e");
    yield "3";
    console.log("r");
}

let iterator = generator();

iterator.next();	//q
iterator.next();	//q w
iterator.next();	//q w e
iterator.next();	//q w e r
iterator.next();	//q w e r

//q 1 w 2 e 3 r
//执行每一段函数并把yield的值返回
for (let item of generator()) {
    console.log(item);
}

```

* 可以给生成器函数传递参数

