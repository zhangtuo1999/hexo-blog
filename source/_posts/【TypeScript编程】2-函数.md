---
title: 【TypeScript编程】2.函数
tags: TypeScript
keywords: TypeScript
description: 《TypeScript编程》笔记。原书第4章：函数。主要内容：函数的声明与调用，包括函数上下文类型推导与类型重载等；泛型，包括泛型声明、推导、别名。
excerpt: 《TypeScript编程》笔记。原书第4章：函数。主要内容：函数的声明与调用，包括函数上下文类型推导与类型重载等；泛型，包括泛型声明、推导、别名。
abbrlink: 414e05a8
date: 2023-04-18 09:21:54
index_img: ../images/封面图/typescript.png
---

# 函数

## 1. 声明与调用

显式注解函数参数类型：

```typescript
function add(a: number, b: number) {
    return a + b
}
```

TS 可以推导出返回类型，也可以对返回类型进行显式注解：

```typescript
function add(a: number, b: number):number {
    return a + b
}
```

多种函数声明方法：

```typescript
// 具名函数
function fn1(foo: number) {
    return foo + '!'
}

// 函数表达式
let fn2 = function (foo: number) {
    return foo + '!'
}

// 箭头函数
let fn3 = (foo: number) => {
    return foo + '!'
}

// 箭头函数简写形式
let fn4 = (foo: number) => foo + '!'
```

### 1.1 可选参数

可以使用 ? 将参数标记为可选的。可选参数必须放在参数列表的末尾：

```typescript
function add(a:number, b:number, c?:number){
    return a + b + (c || 0)
}
```

### 1.2 参数默认值

可以为参数提供默认值，这样调用时可以不用传入参数的值，达到可选参数的效果：

```typescript
function add(a: number, b: number, c = 0) {
    return a + b + c
}
```

带默认值的参数不要求放在参数列表的末尾：

```typescript
function add(a = 1, b: number, c = 0) {
    return a + b + c
}
```

### 1.3 剩余参数

使用 arguments 对象来接收数量不定的参数可能导致不安全：

```typescript
// 将数量不定的参数全放入一个数组
function add1(nums: number[]) {
   	// 安全，TS 可以推导出 sum 和 n 都是 number
    return Array.from(nums).reduce((sum, n) => sum + n)
}

// 使用 arguments 来接收数量不定的参数
function add2() {
    // 不安全， TS 无法推导出 sum 和 n 的类型，只能推导出 any。
    return Array.from(arguments).reduce((sum, n) => sum + n)
}

// TS 会认为 add2 函数不需要参数，这里传参会报错。
// error TS2554: Expected 0 arguments, but got 3.
console.log(add2(1, 2, 3))
```

**剩余参数**会将传入的不定长参数收集为一个数组，使用剩余参数可以保证函数的类型安全：

```typescript
function add(...nums: number[]) {
    return nums.reduce((sum, n) => sum + n)
}

console.log(add(1, 2, 3))	// 6
```

一个参数列表中最多出现一个剩余参数，而且必须放在参数列表的最后：

```typescript
function add(init: number, ...nums: number[]) {
    return nums.reduce((sum, n) => sum + n, init)
}

console.log(add(100, 1, 2, 3))	// 106
```

### 1.4 注解 this 的类型

安全起见，如果在函数中使用 this，需要在参数列表中声明 this 类型：

```typescript
function foo(this: { bar: string }) {
    return this.bar
}
```

这里的 this 不是一般参数，而是保留字。

> TSC 配置项：noImplictThis
>
> 当 noImplictThis 为 true 时，必须显式注解 this。
>
> noImplictThis 属于 strict 配置项。strict 为 true 时，无需单独配置 noImplictThis。

### 1.5 调用签名 / 函数类型

调用签名，也叫类型签名，是表示**函数类型**的句法：

```typescript
type Fn = (a: number, b: number) => number

// 此时，无需再次注解参数和返回值的类型
let bar: Fn = (x, y) => { return x - y }
```

调用签名只包含**类型**层面的代码，可以表示参数、this、返回值、剩余参数和可选参数的类型，但是无法表示默认值。调用签名必须显式注解。

上面的是简写形式，完整形式：

```typescript
type Fn = {
    (a: number, b: number): number
}
```

### 1.6 上下文类型推导

TS 能从上下文中进行类型推导，比如使用调用签名后不必再次注解参数的类型。TS 会尽可能根据上下文来推导出缺失的类型标注。

### 1.7 函数类型重载

TS 支持动态重载函数声明，函数的输出类型取决于输入类型。

## 2. 泛型

泛型参数是在类型层面施加约束的占位类型。

```typescript
// 定义一个过滤函数的调用签名
type Filter = {
    <T>(arr: T[], f:(item: T) => boolean): T[]
}
```

T 就像一个占位类型，类型检查器将根据上下文来填充具体的类型。一般来说，从 T 开始对泛型类型进行命名：T、U、V。

每次调用函数都会对 T 进行重新绑定：

```typescript
type Filter = {
    <T>(arr: T[], f: (item: T) => boolean): T[]
}

let fn: Filter = function filter(arr, f) {
    return arr.filter(f)
}

let arr1 = [1, 2, 3, 4]
console.log(fn(arr1, x => x < 3))

let arr2 = [
    { name: 'foo', age: 10 },
    { name: 'bar', age: 5 },
    { name: 'baz', age: 7 },
]
console.log(fn(arr2, x => x.age > 6))
```

### 2.1 泛型声明位置

声明泛型的位置限定了泛型的**作用域**以及什么时候给泛型绑定**具体**的类型。TS 会在**使用**泛型时为泛型绑定具体类型。

`<T>` 在调用签名中声明，TS 会在调用函数时为 T 绑定具体类型：

```typescript
type Filter = {
    // 调用签名中声明
    <T>(arr: T[], f: (item: T) => boolean): T[]
}

let fn: Filter = function filter(arr, f) {
    return arr.filter(f)
}
```

`<T> ` 在类型别名之后声明，TS 会要求在使用函数时显示绑定类型：

```typescript
// 在类型别名之后声明
type Filter<T> = {
    (arr: T[], f: (item: T) => boolean): T[]
}

// 显式绑定类型
let fn: Filter<number> = function filter(arr, f) {
    return arr.filter(f)
}
```

### 2.2 调用签名中加入泛型

所有调用签名都可以加入泛型：

```typescript
type Filter = {
    <T>(arr: T[], f: (item: T) => boolean): T[]
}

let fn: Filter = //...
```

```typescript
type Filter<T> = {
    (arr: T[], f: (item: T) => boolean): T[]
}

let fn: Filter<number> = //...
```

```typescript
type Filter = <T>(arr: T[], f: (item: T) => boolean) => T[]

let f:Filter = //...
```

```typescript
type Filter<T> = (arr: T[], f: (item: T) => boolean) => T[]

let f:Filter<number> = //...
```

```typescript
function filter<T>(arr: T[], f: (item: T) => boolean): T[] {
    //...
}
```

### 2.3 泛型推导

多数情况下，TS 能够根据传入的参数推导出泛型的具体类型。也可以显式注解泛型，但是要么不注解，要么全注解：

```typescript
function map<T, U>(arr: T[], f: (item: T) => U): U[] {
    let res = []
    for (let item of arr) {
        res.push(f(item))
    }
    return res
}

// 泛型可以推导出类型
map(['a', 'b', 'c'], x => x.length)

// 也可以显式注解类型
map<string, number>(['a', 'b', 'c'], x => x.length)

// 不能只注解部分类型
map<string>(['a', 'b', 'c'], x => x.length)	// error TS2558: Expected 2 type arguments, but got 1.
```

### 2.4 泛型别名

在类型别名中只有一个位置可以声明泛型：

```typescript
type Foo<T> = {
    x: T
    y: number
}
```

使用这样的泛型时，必须要显式绑定类型参数，TS 无法推导出类型：

```typescript
// 显式绑定泛型
let a: Foo<string> = {
    x: 'x',
    y: 10
}
```

### 2.5 泛型默认类型

可以为泛型指定一个默认类型：

```typescript
type MyEvent<T = HTMLElement> = {
    target: T
    type: string
}
```

有默认值的泛型需要放在没有默认值的泛型后面：

```typescript
type MyEvent<Type, Target = HTMLElement> = {
    target: Target
    type: Type
}
```

