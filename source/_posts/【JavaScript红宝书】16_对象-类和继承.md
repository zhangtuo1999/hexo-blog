---
title: 【JavaScript红宝书】16.对象-类和继承
tags:
  - 前端
  - JavaScript
keywords:
  - JavsScript
description: 《JavaScript高级程序设计（第4版）》第8章：对象、类和面向对象编程。主要内容：类。
excerpt: 《JavaScript高级程序设计（第4版）》第8章：对象、类和面向对象编程。主要内容：类。
abbrlink: 2b46e60a
date: 2022-05-10 18:53:59
index_img: ../images/封面图/红宝书.png
---

# 类和继承

ECMAScript 6中引入的 `class` 关键字具有正式定义类的能力。表面上看起来可以支持正式的面向对象编程，但实际上背后仍是原型和构造函数的概念。

## 1 定义类

### 1.1 定义类的两种方式

定义类主要有两种方式：类声明和类表达式。

类声明：

```javascript
class Person {}
```

类表达式：

```javascript
const Person = class {};
```

### 1.2 声明提升

var 定义的类表达式有声明提升。let 和 const 定义的类表达式没有声明提升，类声明也没有声明提升。

```javascript
console.log(ClassExpression);		// undefined （声明提升）
var ClassExpression = class {};		// var 定义的类表达式
console.log(ClassExpression);		// [class ClassExpression]
```

```javascript
console.log(ClassExpression);		// 报错（没有声明提升）
const ClassExpression = class {};	// let 或者 const 定义的类表达式
console.log(ClassExpression);
```

```javascript
console.log(ClassDeclaration);		// 报错 （没有声明提升）
class ClassDeclaration {}			// 类声明
console.log(ClassDeclaration);
```

### 1.3 作用域限制

函数受函数作用域限制，而类受**块作用域**限制。

```javascript
{
  function functionDeclaration() {}
  class ClassDeclaration {}
}

console.log(functionDeclaration);   // [Function: functionDeclaration]
console.log(ClassDeclaration);      // 报错
```

### 1.4 类的构成

类可以包含**构造函数方法**、**实例方法**、**获取函数**、**设置函数**和**静态函数**，但是这都不是必须的。空的类定义依然有效。类定义中的代码都在**严格模式**下执行。*建议*类名的首字母大写。

```javascript
class Foo {
  constructor() {}	// 构造函数
  get bar() {}		// 获取函数
  static baz() {}	// 静态函数
}
```

### 1.5 可选的类表达式名称

类表达式的名称是可选的。在把类表达式赋值给变量后，可以通过 name 属性取得类表达式名称。但是不能在类表达式作用域之外的地方直接访问到类表达式名称。

```javascript
let Person = class Foo {		// Foo 是类表达式的名称，Person 是类表达式赋值给的变量
  bar() {
    console.log(Person.name, Foo.name);		// 类作用域中可以访问到 Foo 这个类名称	
  }
};
const p = new Person();
p.bar()						// Foo Foo
console.log(Person.name);	// Foo
console.log(Foo);			// 报错
```

## 2 类构造函数

方法名 constructor 会告诉解释器在使用 new 操作符创建类的新实例时，应该调用这个构造函数。构造函数的定义不是必须的，不定义构造函数相当于将构造函数定义为空函数。

### 2.1 实例化

使用 new 操作符实例化 Person 的操作等于使用 new 调用其构造函数。JavaScript 解释器知道使用 new 和类意味着应该使用 constructor 函数进行实例化。

使用 new 调用类的构造函数会执行下面的操作：

1. 在内存中创建一个新的对象。
2. 这个新对象的内部的`[[Prototype]]`指针被赋值为构造函数的`prototype`属性。
3. 构造函数内部的 this 被赋值为这个新的对象。即 this 指向新对象。
4. 执行构造函数内部的代码。即给新对象添加属性等操作。
5. 如果构造函数没有返回值，则返回该对象。

类实例化时传入的参数会作为构造函数的参数。如果不需要参数，则类名后面的括号可以*省略*。

```javascript
const Person = class {
  constructor(name) {
    this.name = name;
  }
};

const p = new Person("foo");
console.log(p);					// Person { name: 'foo' }
const p2 = new Person();		
console.log(p2);				// Person { name: undefined }
```

**默认情况下，类构造函数会在执行后返回 this 对象**。构造函数返回的对象会被用作实例化的对象，如果没有什么引用新创建的 this 对象，那么这个对象会被销毁。

如果返回的不是 this 对象，而是其它对象，那么这个对象不会通过 instanceof 操作符检测出来有关联，因为这个对象的**原型指针**并没有被修改。

```javascript
class Person{
    constructor(){
        return {}
    }
}

const p = new Person()
console.log(p instanceof Person);   // false
```

类构造函数与构造函数的主要区别是，调用类构造函数必须使用 new 操作符。普通构造函数如果不使用 new 调用，那么就会以**全局**的 this 作为内部对象。调用类构造函数时，如果忘了使用 new 则会抛出错误。

### 2.2 把类当成特殊函数

ECMAScript 中的类就是一种特殊的*函数*。通过 typeof 操作符检查类标识符，表明它是一个函数。

```javascript
class Person {}
console.log(typeof Person);	// function
```

**类标签**有 prototype 属性，而它的原型也有一个 constructor 属性指向自身。

```javascript
class Person {}
console.log(Person.prototype);	// {}
console.log(Person.prototype.constructor === Person);	// true
```

与普通的构造函数一样，可以使用 instanceof 操作符检查构造函数原型是否存在于实例的原型链中。

```javascript
class Person {}
const p = new Person();
console.log(p.__proto__ === Person.prototype);	// true
console.log(p instanceof Person);				// true
```

在类的上下文中，**类本身**在使用 new 调用时就会被当做构造函数。重点在于，类定义的 constructor 方法**不会**被当成构造函数，在对它使用 instanceof 返回 false。但是，如果在创建实例时直接将**类构造函数**当成普通构造函数来使用，那么 instanceof 操作符的返回值会反转。

```javascript
class Person {
  constructor() {
    this.name = "Foo";
  }
}

const p = new Person();
console.log(p instanceof Person);				// true
console.log(p instanceof Person.constructor);	// false

const p1 = new Person.constructor();
console.log(p1 instanceof Person);				// false
console.log(p1 instanceof Person.constructor);	// true
```

注意：

1. 可以将类作为**参数**来传递
2. 类可以立即实例化

## 3 实例、原型和类成员

类的语法可以非常方便地定义应该存在于**实例**上的成员，应该存在于**原型**上的成员，以及应该存在于**类本身**的成员。

### 3.1 实例成员

在**类构造函数**内部，可以为新创建的实例（this）添加自有属性。在类构造函数执行完毕后仍然可以给实例继续添加新成员。每个实例都对应一个**唯一**的成员对象，这意味着所有成员都**不会**在原型上共享。

```javascript
class Person {
  constructor() {
    this.names = ["foo", "bar"];
  }
}

const p1 = new Person();
const p2 = new Person();
console.log(p1.names === p2.names);		// false

p1.names.pop();
console.log(p1);	// Person { names: [ 'foo' ] }
console.log(p2);	// Person { names: [ 'foo', 'bar' ] }
```

### 3.2 原型方法与访问器

为了在实例间共享方法，类定义语法把在**类块**中定义的方法作为**原型方法**。在**类块**中定义的所有内容都最终会定义在类的**原型**上。

```javascript
class Person {
  constructor() {
    this.sayName = function () {
      console.log("instance");
    };
  }
  sayName() {
    console.log("prototype");
  }
}

const p = new Person();
p.sayName();			// instance
p.__proto__.sayName();	// prototype
```

可以把**方法**定义在类的构造函数或者类块中，但是不能在类块中给原型添加**原始值**或者**对象**作为成员数据。

类方法等同于对象属性，因此可以使用字符串、符号或计算的值作为键。

```javascript
class Person {
  stringKey() {}
  [Symbol("foo")]() {}
  ["computed" + "key"]() {}
}
```

类定义也支持获取和设置访问器。

```javascript
class Person {
  set name(newName) {
    this.name_ = newName;
  }
  get name() {
    return this.name_;
  }
}

const p = new Person();	
p.name = "foo";
console.log(p.name);	// foo
```

### 3.3 静态类方法

可以在类上定义**静态方法**。这些方法通常用于执行**不特定于实例**的操作。静态类成员在类块中使用 static 关键字定义，this 引用类自身。

```javascript
class Person {
  constructor() {
    this.foo = function () {	// 添加到 this 的所有的内容都会存在于不同的实例上
      console.log("instance");
    };
  }
  foo() {						// 定义到类的原型上
    console.log("prototype");
  }
  static foo() {				// 定义到类本身上
    console.log("class");
  }
}

const p = new Person();
p.foo();				// instance
p.__proto__.foo();		// prototype
Person.foo();			// class
```

### 3.4 非函数原型和类成员

虽然类定义不支持在原型或类上添加**数据成员**，但是在类定义**外部**，可以手动添加。

```javascript
class Person {}
Person.prototype.name = "Foo";	// 在类的原型上定义属性
Person.age = 18;				// 在类上定义属性
```

> 在**共享目标**（原型和类）上添加可修改的数据成员是一种**反模式**。一般来说，对象实例应该独自拥有通过 this 引用的数据。

## 4 继承

ES6 原生支持了类继承机制。虽然类继承使用的是新语法，但背后依然使用的是原型链。

### 4.1 继承基础

ES6 中类支持单继承。使用 extends 关键字，就可以继承所有拥有`[[Construct]]`和原型的对象。这意味着不仅可以继承一个**类**，也可以继承普通的**构造函数**。

继承类：

```javascript
class Person{}
class Teacher extends Person{}
const t = new Teacher()
console.log(t instanceof Teacher);	// true
console.log(t instanceof Person);	// true
```

继承普通的构造函数：

```javascript
function Person() {}
class Teacher extends Person {}
const t = new Teacher()
console.log(t instanceof Teacher);	// true
console.log(t instanceof Person);	// true
```

**原型**和**类**上定义的方法都会带到派生类。this 的值会反映调用相应方法的**实例**或者**类**。

```javascript
class Person {
  foo() {
    console.log("foo", this);
  }
  static bar() {
    console.log("bar", this);
  }
}

class Teacher extends Person {}

const t = new Teacher();
const p = new Person();

t.foo();		// foo Teacher {}
p.foo();		// foo Person {}

Teacher.bar();	// bar [class Teacher extends Person]
Person.bar();	// bar [class Person]
```

### 4.2 super()

派生类的方法可以通过 super 关键字引用它们的**原型**。这个关键字只能在**派生类**中使用，而且仅限于类构造函数、实例方法和静态方法内部。

在**类构造函数**中使用 super() 可以调用**父类构造函数**。

```javascript
class Person {
  constructor() {
    this.name = "foo";
  }
}

class Teacher extends Person {
  constructor() {
    super();
    console.log(this);						// Teacher { name: 'foo' }
    console.log(this instanceof Person);	// true
  }
}
new Teacher()
```

在**实例方法**中使用 super 调用**父类实例方法**。

```javascript
class Person {
  constructor() {
    this.name = "foo";
  }
  sayHelloPerson() {
    console.log("hello person");
  }
}

class Teacher extends Person {
  sayHelloTeacher() {
    super.sayHelloPerson();
  }
}

const t = new Teacher();
t.sayHelloTeacher()			// hello person
```

在**静态方法**中可以通过 super 调用**父类静态方法**。

```javascript
class Person {
  static foo() {
    console.log("Person");
  }
}

class Teacher extends Person {
  static foo() {
    super.foo();
  }
}

Teacher.foo();		// Person
```

> ES6 给类构造函数和静态方法添加了内部特性`[[HomeObject]]`，这个特性是一个指针，指向**定义该方法的对象**。这个指针是自动赋值的，而且只能在 JavaScript 引擎内部方法访问。super 始终定义为 `HomeObject` 的**原型**。

#### 使用 super 时注意几个问题

1. super 只能在**派生类**构造函数、实例方法和静态方法中使用。

2. 不能单独使用 super 关键字，要么用它调用构造函数，要么用它引用实例方法或静态方法。

3. 调用 super() 会调用父类构造函数，并将返回的实例赋值给 this。

4. super() 的行为如同构造函数，如果需要可以给父类构造函数传参。

5. 如果没有定义派生类的构造函数，在实例化派生类时会调用 super() ，并传入所有传给派生类的参数。

6. 在派生类的构造函数中，不能在调用 super() 之前引用 this。

   ```javascript
   class Person {
     constructor() {
       this.name = "foo";
     }
   }
   
   class Teacher extends Person {
     constructor() {
       this.age = 18;		// 报错 不能在调用 super() 之前引用 this。
       super();
     }
   }
   
   new Teacher();
   ```

7. 如果在派生类中显式定义了构造函数，要么在其中调用 super()，要么在其中返回一个对象（可能破坏继承关系）。

   ```javascript
   class Person {}
   class Teacher extends Person {
     constructor() {
       return {};
     }
   }
   const t = new Teacher();
   console.log(t instanceof Person);	// false
   ```

### 4.3 抽象基类

抽象基类是这样一个类，它可供其他类继承，但是本身不会被实例化。虽然 ECMAScript 没有专门支持抽象基类，但是可以通过 new.target 实现。new.target 保存通过 new 关键字调用的类或函数。通过在实例化时检测 new.target 是不是抽象基类，可以阻止对抽象基类的实例化。

```javascript
class Person {
  constructor() {
    if (new.target === Person) {
      throw new Error("无法实例化 Person ");
    }
  }
}

const p = new Person();	// 报错 无法实例化 Person
```

### 4.4 继承内置类型

```javascript
class SuperArray extends Array {
  foo() {
    console.log(this);
  }
}

const sa = new SuperArray(1, 2, 3, 4);
console.log(sa);	// SuperArray(4) [ 1, 2, 3, 4 ]
sa.foo();			// SuperArray(4) [ 1, 2, 3, 4 ]
```

### 4.5 类混入(Mixin)

经常需要把不同类的行为集中到一个类。ES6没有显式支持多继承，但是可以通过现有的特性来模拟这种行为。

> 如果只需要混入多个**对象**的属性，那么使用 Object.assign() 即可。

下面例子中，extends 后面是一个 JavaScript 表达式。任何可以解析为一个类或者一个构造函数的**表达式**都是有效的。

```javascript
class Person {}
function getSuperClass() {		// 可求值的类定义表达式
  return Person;
}
class Teacher extends getSuperClass {}
```

**混入模式**可通过在一个表达式中连缀多个混入元素来实现，这个表达式最终会解析为一个可以被继承的类。如果 Person 类需要组合 A、B、C，则需要实现 B 继承 A，C 继承 B，最后 Person 继承 C。

实现混入模式的一种策略是，定义一组可嵌套的函数，每个函数分别接收一个超类作为参数，并返回一个子类。连缀调用这些函数，便组合成了超类的表达式。

```javascript
class Person {}

let FooMixin = (SuperClass) =>
  class extends SuperClass {
    foo() {
      console.log("foo");
    }
  };

let BarMixin = (SuperClass) =>
  class extends SuperClass {
    bar() {
      console.log("bar");
    }
  };

let BazMixin = (SuperClass) =>
  class extends SuperClass {
    baz() {
      console.log("ba");
    }
  };

class Teacher extends FooMixin(BarMixin(BazMixin(Person))) {}	// 嵌套调用
const t = new Teacher();
t.foo();	// foo
t.bar();	// bar
t.baz();	// baz
```

通过写一个辅助函数将嵌套调用展开。

```javascript
class Person {}
let FooMixin = (SuperClass) =>
  class extends SuperClass {
    foo() {
      console.log("foo");
    }
  };

let BarMixin = (SuperClass) =>
  class extends SuperClass {
    bar() {
      console.log("bar");
    }
  };

let BazMixin = (SuperClass) =>
  class extends SuperClass {
    baz() {
      console.log("baz");
    }
  };

function mix(BaseClass, ...Mixins) {				// 辅助函数 解决嵌套调用
  return Mixins.reduce(
    (accumulator, current) => current(accumulator),
    BaseClass
  );
}
class Teacher extends mix(Person, FooMixin, BarMixin, BazMixin) {}

const t = new Teacher();
t.foo();	// foo
t.bar();	// bar
t.baz();	// baz
```

