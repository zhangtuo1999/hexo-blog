---
title: 【TypeScript编程】3.类和接口
tags: TypeScript
keywords: TypeScript
description: 《TypeScript编程》笔记。原书第5章：类和接口。主要内容：类；抽象类；接口等。
excerpt: 《TypeScript编程》笔记。原书第5章：类和接口。主要内容：类；抽象类；接口等。
abbrlink: 8e137a64
date: 2023-04-19 09:13:46
index_img: ../images/封面图/typescript.png
---

# 类和接口

## 1. 访问修饰符

TS 中类的属性和方法支持三种访问修饰符：

1. public。任何地方都可以访问。默认。
2. protected。可由当前类及其子类访问。
3. privite。只可由当前类的实例进行访问。

```typescript
class Foo {
    // 在构造方法的参数列表中声明
    constructor(
        private x: string,
        protected y: string,
        public z: string
    ) { }
    
    sayX(){
        console.log(this.x)
    }
}

let f = new Foo('x', 'y', 'z')

// error TS2341: Property 'x' is private and only accessible within class 'Foo'.
console.log(f.x) 

//  error TS2445: Property 'y' is protected and only accessible within class 'Foo' and its subclasses.
console.log(f.y) 

// 可访问
console.log(f.z) 
```

实例属性可以在构造方法的参数中声明，也可以通过属性初始化语句来声明：

```typescript
class Foo {
    // 在属性初始化语句中声明
    private x: number = 10
    protected y: string = 'y'
    z: boolean = true
}
```

## 2. 抽象类

如果想定义一个类，但是不想让用户直接实例化，而是在此基础上进行扩展。可以使用 abstract 关键字：

```typescript
abstract class Foo {}
```

抽象类中可以有抽象属性和抽象方法：

```typescript
abstract class Foo {
    abstract x: number
    y = 10
    abstract sayX(): void
}

// 实现抽象类时，必须实现抽象属性和抽象方法
class FooImpl extends Foo {
    x = 8
    sayX(): void {
        console.log(this.x)
    }
}

let f = new FooImpl()
console.log(f.x)	// 8
console.log(f.y)	// 10
```

## 3. 类中使用 this 作为返回类型

对于类来说，this 类型可以用作方法的返回类型。

```typescript
// 模拟 Set 类，其中 add 方法返回一个和该类相同的类型
class MySet {
    add(val: number): this {
    	// ...
    }
}
```

这样子类对其继承时，无需覆盖掉方法签名：

```typescript
// 继承自 MySet 类后，如果不覆写 add 方法，那么不会返回该类型而是返回 MySet。
// 所以在 SubMySet 类中，必须覆写 add 方法
class MySet {
    add(val: number): MySet {
        // ...
    }
}

class SubMySet extends MySet {
    add(val: number): SubMySet {
        // ...
    }
}
```

```typescript
// 使用 this 可以解决这个问题
// 在 MySet 中，this 指向 MySet 的实例；在 SubMySet 中，this 指向 SubMySet 的实例
class MySet {
    add(val: number): this {
    	// ...
    }
}

// 无需覆写 add 方法
class SubMySet {
	
}
```

## 4. 接口

与**类型别名**相似，接口也是一种命名类型的方式。接口和类型别名算是一种概念的两种句法，稍稍有些差别：

```typescript
type Foo = {
    x: number
    y: string
    z: boolean
}
```

```typescript
interface Foo{
    x: number
    y: string
    z: boolean
}
```

类型别名和接口的区别：

1. 类型别名更通用，右边可以是任何类型；在接口声明中，右边必须是结构：

   ```typescript
   // A 和 B 无法使用接口声明
   type A = number
   type B = A & string
   ```

2. 扩展接口时，TS 会检查扩展接口是否可以赋值给被扩展的接口，如果不可以会报错。

   使用交集类型时不会出现这个问题。 使用交集运算符 &，TS 会尽量将扩展和被扩展的类型组合到一起，结果是重载签名。

   ```typescript
   interface A {
       foo(x: number): string
       bar(y: number): string
   }
   
   interface B extends A {
       foo(x: string | number): string
       bar(y: string): string	// 报错
   }
   ```

   ```typescript
   type A = {
       foo(x: number): string
       bar(y: number): string
   }
   
   type B = A & {
       foo(x: number | string): string
       bar(y: string): string
   }
   ```

3. 同一作用域中的多个同名接口将自动合并；同一作用域中的多个同名类型别名会导致编译错误：

   ```typescript
   // 同名接口自动合并
   interface A {
       foo(x: number): string
   }
   
   interface A {
       bar(x: string): string
   }
   
   let a: A = {
       foo(x) {return ''},
       bar(x) {return ''}
   }
   ```

   ```typescript
   // 同名类型别名，报错
   type A = {
       foo(x: number): string
   }
   
   //  error TS2300: Duplicate identifier 'A'.
   type A = {
       bar(x: string): string
   }
   ```

### 4.1 声明合并

TS 会自动把多个同名声明组合到一起。

声明两个同名接口：

```typescript
interface A {
    foo(x: number): string
}

interface A {
    bar(x: string): string
}

let a: A = {
    foo(x) {return ''},
    bar(x) {return ''}
}
```

但是，两个同名的接口不能有冲突：

```typescript
interface A {
    foo: number
}

interface A {
    foo: string	// 报错
}
```

如果接口中声明了泛型，那么两个接口中要使用**完全相同**的方式来声明泛型，这样才能合并接口：

```typescript
interface Uer<Age extends number> {
    age: Age
}

// 泛型的名字也必须一样
// error TS2428: All declarations of 'Uer' must have identical type parameters.
interface Uer<MyAge extends number> {
    age: MyAge
}
```

### 4.2 实现

声明类时，可以使用 implements 关键字来指明该类实现某个接口。类必须实现接口中声明的**所有**属性和方法。在此基础上可以实现其他的属性和方法。

```typescript
interface Foo {
    num: number
    foo(x: number): void
    bar(x: number): void
}

class FooImpl implements Foo {
    num = 10
    foo(x: number): void { }
    bar(x: number): void { }
}
```

接口可以声明实例属性，但是不能带有可见性修饰符（private，protected，public），也不能使用 static 关键字。但是可以使用 readonly 将实例属性标记为已读。

一个类可以实现多个接口：

```typescript
interface A {
    foo(x: number): void
}

interface B {
    bar(x: string): void
}

class C implements A, B {
    foo(x: number): void { }
    bar(x: string): void { }
}
```

## 5. 类是结构化类型

TS 会根据**结构**比较类，与类的**名称**无关。两个类根据结构来决定是否兼容；对象如果定义了相同的属性或者方法，也会和类兼容。

```typescript
class A {
    foo() { }
}

class B {
    foo() { }
}

function bar(x: A) {
    x.foo()
}

let a = new A()
let b = new B()

bar(a)
bar(b)
```

但是，当类中有 private 和 protected 修饰的字段时，情况不同。检查一个结构是否可以赋值给一个类时，如果类中有 private 和 protected 修饰的字段，而且结构不是类或者子类的实例，那么结构就不能赋值给类：

```typescript
class A {
    private x = 1	// 属性 x 被 private 修饰
}

class B extends A { }

function f(a: A) { }

f(new A)
f(new B)

f({ x: 1 })	// 报错
```

## 6. 类和接口的泛型

类和接口也支持泛型，泛型的作用域可以放在整个类或者接口中，也可以放在特定的方法中：

```typescript
class MyMap<K, V>{
    constructor(initKey: K, initVal: V) {
    	// 构造函数... 
    }
    get(key: K): V {
    	// 实例方法...
    }
    set(key: K, val: V) {
    	// ... 
    }
    merge<K1, V1>(map: MyMap<K1, V1>): MyMap<K | K1, V | V1> { 
    	// 实例方法...
    }
    static of<K, V>(k:K, v:V): MyMap<K, V>{
        // 静态方法...
    }
}
```

当泛型的作用域是整个类时，泛型可以在每个实例方法和属性中使用。

构造函数中不能声明泛型，只能在类声明中声明泛型。

实例方法可以访问类一级的泛型，也可以自己声明泛型。

静态方法不能访问类的泛型，但是可以自己声明泛型。

接口绑定泛型：

```typescript
interface MyMap<K, V> {
    get(key: K): V
    set(key: K, val: V): void
}
```

