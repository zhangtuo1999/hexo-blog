---
title: 【TypeScript】基础笔记
tags: TypeScript
keywords: TypeScript
description: TypeScript基础笔记。相关视频教程BV：BV1Xy4y1v7S2。主要内容：类型声明；基本类型；对象类型；类型断言；类型别名；tsc配置；webpack配置；类和接口；继承。
excerpt: TypeScript基础笔记。相关视频教程BV：BV1Xy4y1v7S2。主要内容：类型声明；基本类型；对象类型；类型断言；类型别名；tsc配置；webpack配置；类和接口；继承。
abbrlink: 8a5a935d
date: 2023-02-24 20:23:07
index_img: ../images/封面图/typescript2.png
---

# 1 介绍

## 1.1 什么是 TypeScript

* JavaScript 的超集，一门建立在 JavaScript 上的语言。

* 为 JavaScript 添加了新特性和优势。
* 无法在 JavaScript 环境中运行，但最终会编译成 JavaScript。 

## 1.2 为什么使用 TypeScript

* JavaScript 中没有类型系统。因此，可能写出符合 JavaScript 语法，但是逻辑有错误的代码。
* 使用 TypeScript 提供的类型系统，可以避免因为弱类型带来的错误。

## 1.3 搭建开发环境

1. 安裝 node.js

2. 全局安装 typescript 

   ```shell
   npm i -g typescript
   ```

3. 检查是否安装成功

   ```shell
   tsc -v
   ```

## 1.4 Hello World

1. 编写 TypeScript 代码

   ```typescript
   // 在文件 hello-world.ts 中：
   console.log('hello world')
   ```

2. 编译 TypeScript 代码

   ```shell
   tsc hello-world.ts
   ```

   编译器会将 .ts 文件编译成 .js 文件。在目录下会生成文件：hello-world.js

3. 执行 JavaScript 代码

   ```shell
   node hello-world.js
   ```

# 2 类型

## 2.1 类型声明

### 2.1.1 变量声明

定义变量时，在变量名后声明变量的类型：

```typescript
// number
let count: number = 0

// string
let msg: string = 'hello world'

// boolean
let flag: boolean = true
```

定义变量并初始化时，即使不声明变量的类型，编译器也能被根据初始值判断出变量类型：

```typescript
// number
let count = 0

// string
let msg = 'hello world'

// boolean
let flag = true
```

### 2.1.2 函数参数声明

定义函数时，在形参的后面声明形参的类型：

```typescript
function sum(a: number, b: number) {
    return a + b
}
```

### 2.1.3 函数返回值声明

定义函数时，在形参列表的后面声明函数返回值的类型：

```typescript
function getSumMsg(a: number, b: number): string {
    return (a + b).toString()
}
```

### 2.1.4 箭头函数声明

同时声明箭头函数的参数类型和返回值类型：

```typescript
let sum: (a: number, b: number) => number

// 形参名不必和定义时保持一致，但是类型需要保持一致
sum = function (x, y) {
    return x + y
} 
```

## 2.2 所有类型

### 2.2.1 基本类型

| 数据类型   | 关键字    | 描述                                                         |
| ---------- | --------- | ------------------------------------------------------------ |
| 任意类型   | any       | 声明为 any 的变量可以赋予任意类型的值。                      |
| 未知类型   | unknown   | 未知类型的变量。                                             |
| void       | void      | 用于标识方法返回值的类型，表示该方法没有返回值。             |
| never      | never     | never 是其它类型（包括 null 和 undefined）的子类型，代表从不会出现的值。 |
| 数组       | 无        | 声明变量为数组。                                             |
| 元组       | 无        | 元组类型用来表示已知元素数量和类型的数组，各元素的类型不必相同，对应位置的类型需要相同。 |
| 枚举       | enum      | 枚举类型用于定义数值集合。                                   |
| 数字类型   | number    | 同 JavaScript                                                |
| 字符串类型 | string    | 同 JavaScript                                                |
| 布尔类型   | boolean   | 同 JavaScript                                                |
| null       | null      | 同 JavaScript                                                |
| undefined  | undefined | 同 JavaScript                                                |

#### 2.2.1.1 any / unknown

对变量声明为 any 类型，相当于对该变量关闭类型检测：

```typescript
let foo: any

foo = 10
foo = 'bar'
foo = true
```

不声明变量的类型，隐式为 any 类型：

```typescript
let foo

foo = 10
foo = 'bar'
foo = true
```

any 类型的变量可以赋值给任何类型。这可能导致其他类型的变量与声明的类型不符：

```typescript
let count:number = 10
let foo:any = 'bar'

//将 any 类型数据赋值给count，导致count 与声明的类型不符
count = foo
console.log(count) // 'bar'
```

当不知道应该给变量声明为什么类型时，建议将其声明为 unknown：

```typescript
let foo:unknown

foo = 10
foo = 'bar'
foo = true
```

unknown 类型的变量不可以赋值给其他类型变量，但可以赋值给 同样为 unknown 类型的变量：

```typescript
let foo:unknown = 10
let bar:unknown

bar = foo
```

#### 2.2.1.2 void

对函数返回值声明为 void，表示函数没有明确的返回值：

```typescript
function printFn(): void {
    console.log('print...')
}
```

定义函数时，如果不声明函数返回值的类型，那么函数返回值隐式声明为 void：

```typescript
function printFn() {
    console.log('print...')
}
```

但是，如果函数有返回值，即使不声明函数返回值的类型，那么编译器也会自动推断函数返回值的类型：

```typescript
// 自动推断函数返回值的类型：10 | 'foo'
function fn(x: number) {
    if (x > 5) {
        return 10
    } else {
        return 'foo'
    }
}
```

如果对变量声明为 void，只能将其赋值为 null 或 undefined。

#### 2.2.1.3 never

对函数返回值声明为 never，表示函数不会返回结果（比如：死循环、抛出错误）：

```typescript
function fn1(): never {
    while (true) { }
}

function fn2(): never {
    throw new Error()
}
```

#### 2.2.1.4 number / string / boolean

```typescript
// number
let count = 0

// string
let msg = 'hello world'

// boolean
let flag = true
```

可以直接使用字面量进行类型声明。声明后不可以赋值为其他字面量（类似常量）：

```typescript
// 使用单个字面量进行类型声明
let count: 10 = 10

// 使用多个字面量进行类型声明
let msg: 'foo' | 'bar' = 'foo'
msg = 'bar'
```

### 2.2.2 对象类型

#### 2.2.2.1 指定属性

声明一个变量为对象类型的同时，还要指定这个对象应该存在的属性以及该属性的类型：

```typescript
let obj: { a: number, b: string }
```

变量的属性及其类型应该与定义时的规定保持一致。属性多了少了、类型错误都不行：

```typescript
obj = { a: 10, b: 'foo', c: true } // 报错
obj = { a: 10}	// 报错
obj = { a: 10, b: 20 } // 报错
```

#### 2.2.2.2 可选属性

声明变量时，如果不确定某个属性是否一定存在，可以使用`?`来表示该属性可选：

```typescript
let obj: { a: number, b: string, c?: boolean }
obj = { a: 10, b: 'foo', c: true } // 正确
obj = { a: 10, b: 'foo' }	// 正确
```

也可使用`[propName: string]`来表示任意属性名：

```typescript
// 必须有一个number类型的a属性，其余属性类型不限制
let obj: { a: number, [propName: string]: any }
```

#### 2.2.2.3 数组

有两种方式将变量声明为数组：使用`Array`和`[]`。

使用`Array`：

```typescript
let arr1: Array<string> = ['a', 'b']

let arr2: Array<{ a: string }> = [
    { a: 'foo' }, { a: 'bar' }
]
```

在类型后加上`[]`：

```typescript
let arr1: string[] = ['a', 'b']

let arr2: { a: string }[] = [
    { a: 'foo' }, { a: 'bar' }
]
```

#### 2.2.2.4 元组

元组可看作数量和对应位置元素类型固定的数组：

```typescript
let tuple: [number, string, boolean] = [10, 'foo', true]
```

#### 2.2.2.5 枚举

```typescript
enum Gender {Male, Female}
let person: { gender: Gender } = { gender: Gender.Male }
```

### 2.2.3 联合类型

使用`|`来连接多种类型，表示多种类型满足其一即可：

```typescript
let foo: number | string = 10

foo = 'bar'
```

使用`&`来连接多种类型，表示多种类型要同时满足：

```typescript
let foo: { a: number } & { b: string }

foo =  = { a: 10, b: 'foo' }
```

## 2.3 类型断言

可以对变量的类型进行断言，来告诉编译器不知道类型的变量其实际类型。

有两种方式对变量的类型进行断言：`as` 和 `<>`。

使用 `as`：

```typescript
let foo: string = 'foo'

let bar: unknown = 'bar'

foo = bar as string
```

使用`<>`：

```typescript
let foo: string = 'foo'

let bar: unknown = 'bar'

foo = <string>bar
```

## 2.4 类型别名

使用 type 关键字可以给类型起一个别名：

```typescript
type myType = {
    a: number,
    b: string,
    c: boolean
}

let foo: myType = { a: 10, b: 'foo', c: true }
```

# 3 tsc 编译选项

## 3.1 -w 监视文件变化

使用 `-w` 选项来监视文件的变化，当文件发生变化时，自动将 .ts 文件编译成 .js 文件：

```shell
tsc <文件名>.ts -w
```

## 3.2 生成 tsconfig.json

tsconfig.json 中记录着 ts 编译器的配置。使用 `--init` 参数可以生成默认配置。

```shell
tsc --init
```

当前目录下存在 tsconfig.json 文件时，使用 tsc 命令即可编译当前目录下所有文件。加上 `-w` 参数即可监视所有文件。

```shell
tsc -w
```

## 3.3 tsconfig.json 配置项

配置文档 https://www.typescriptlang.org/zh/tsconfig

### 3.3.1 include / exclude / files

include 用来指定哪些 ts 文件需要被编译，。

```json
{
    "include": [
        "./src/*"
    ]
}
```

> 注：`**`匹配任意目录，`*`匹配任意文件，"./src/\*\*/*"匹配当前目录下的 src 目录下的任意目录下的任意文件

exclude 用来指定哪些 ts 文件不需要被编译。

```json
{
    "include": [
        "./src/demo/*"
    ]
}
```

files 用来指定哪些文件需要被编译，需要将文件路径。

```json
{
  "files": [
    'foo.ts', 'bar.ts'
  ]
}
```

### 3.3.2 compilerOptions

compilerOptions 编译器选项中常用子选项如下：

1. target：指定 ts 被编译为 ES 的版本。
2. module：指定编译后使用的模块化规范。
3. lib：指定需要使用的库。
4. outDir：指定编译生成的代码输出的目录。
5. outFile：将所有编译后的代码合并为一个文件。
6. allowJs：是否编译 js 文件。
7. checkJs：是否检查 js 文件是否符合 ts 规范。
8. removeComments：是否一处注释。
9. noEmit：是否生成编译后的代码。
10. noEmitOnError：编译出错时是否生成代码。
11. strict：严格模式。
12. alwaysStrict：是否开启严格模式。
13. noImplicitAny：是否允许隐式 any 类型。
14. noImplicitThis：是否允许不明确的 this。
15. ...

# 4 webpack 打包 ts 文件

## 4.1 准备工作

1. 初始化 node 项目，生成 package.json 文件

   ```shell
   npm install -y
   ```

2. 安装 webpack、webpack-cli、typescript、ts-loader

   ```shell
   npm install -D webpack webpack-cli typescript ts-loader     
   ```

   > 注：-D 参数表示安装的是开发依赖。

3. 配置 webpack

   在根目录下新建 webpack.config.js 文件，然后配置如下信息：

   ```javascript
   // 引入 path 包
   const path = require('path')
   
   // 配置信息都应该写在 module.exports 中
   module.exports = {
   
       // 入口文件
       entry: './src/index.ts',
   
       // 打包后的文件
       output: {
           path: path.resolve(__dirname, 'dist'),
           filename: 'bundle.js'
       },
   	
       // 设置 webpack 为开发模式
       mode: 'development',
       
       // webpack 打包时要使用的模块
       module: {
           // 指定加载规则
           rules: [
               {
                   // test 指定规则生效的文件
                   test: /\.ts$/,
   
                   // 指定要使用的 loader
                   use: 'ts-loader',
   
                   // 要排除的文件
                   exclude: /node_modules/
               }
           ]
       }
   
   }
   ```

4. 配置 ts

   根目录下执行：`tsc --init`，生成 tsconfig.json 文件。

5. 在 package.json 中添加配置：

   ```json
   {
       "scripts": {
       	"build": "webpack"
       },
   }
   ```

6. 构建

   ```shell
   npm run build
   ```


## 4.2 进阶配置

### 4.3.1 html-webpack-plugin

html-webpack-plugin 插件可以自动生成 index.html 并引入所 js需文件。

1. 安装插件

   ```shell
   npm install -D html-webpack-plugin
   ```

2. 更新 webpack 配置

   ```javascript
   // 引入 path 包
   const path = require('path')
   
   
   // 引入 html 插件
   const HTMLWebpackPlugin = require('html-webpack-plugin')
   
   
   // 配置信息都应该写在 module.exports 中
   module.exports = {
   
       // 入口文件
       entry: './src/index.ts',
   
       // 打包后的文件
       output: {
           path: path.resolve(__dirname, 'dist'),
           filename: 'bundle.js'
       },
   	
       // 设置 webpack 为开发模式
       mode: 'development',
       
       // webpack 打包时要使用的模块
       module: {
           // 指定加载规则
           rules: [
               {
                   // test 指定规则生效的文件
                   test: /\.ts$/,
   
                   // 指定要使用的 loader
                   use: 'ts-loader',
   
                   // 要排除的文件
                   exclude: /node_modules/
               }
           ]
       },
   
       // 配置插件：
       plugins:[
           new HTMLWebpackPlugin()
       ]
   
   }
   ```

3. 构建

   ```shell
   npm run build
   ```

   可以看到 dist 输出目录下多了一个 index.html 文件。

4. （可选）实例化 html 插件时传入配置，实现自定义页面标题。

   ```javascript
   const HTMLWebpackPlugin = require('html-webpack-plugin')
   
   {
       module.exports = {
           plugins: [
               new HTMLWebpackPlugin({title:""})
           ]
       }
   }
   ```

### 4.3.2 webpack-dev-server

webpack-dev-server 插件能监视文件变化，并自动打包构建。

1. 安装插件

   ```shell
   npm install -D webpack-dev-server 
   ```

2. 在 package.json 中添加如下配置

   ```json
   {
     "scripts": {
       "start": "webpack serve --open index.html"
     },
   }
   ```

3. 运行

   ```shell
   npm run start
   ```

   webpack-dev-server 插件会开启服务器，在浏览器中打开 html-webpack-plugin 插件生成的 index.html，监视其变化并自动打包构建。

### 4.3.3 clean-webpack-plugin

clean-webpack-plugin 插件可以在构建时先把 dist 文件夹下的旧文件清楚，然后再接着构建。

1. 安装插件

   ```shell
   npm install -D clean-webpack-plugin
   ```

2. 更新 webpack 配置

   ```javascript
   // 引入 path 包
   const path = require('path')
   
   
   // 引入 html 插件
   const HTMLWebpackPlugin = require('html-webpack-plugin')
   
   // 引入 clean 插件
   const {CleanWebpackPlugin} = require('clean-webpack-plugin')
   
   
   // 配置信息都应该写在 module.exports 中
   module.exports = {
   
       // 入口文件
       entry: './src/index.ts',
   
       // 打包后的文件
       output: {
           path: path.resolve(__dirname, 'dist'),
           filename: 'bundle.js'
       },
   
       // 设置 webpack 为开发模式
       mode: 'development',
   
       // webpack 打包时要使用的模块
       module: {
           // 指定加载规则
           rules: [
               {
                   // test 指定规则生效的文件
                   test: /\.ts$/,
   
                   // 指定要使用的 loader
                   use: 'ts-loader',
   
                   // 要排除的文件
                   exclude: /node_modules/
               }
           ]
       },
   
       // 配置插件：
       plugins: [
           new CleanWebpackPlugin(),
           new HTMLWebpackPlugin({ title: 'ts-demo' })
       ]
   
   }
   ```

### 4.3.4 配置引用模块

默认配置下，webpack 不知道哪些文件是否可以作为模块被引入。如果想要引入 ts、js 作为模块代码，需要配置 webpack.config.js 如下选项：

```javascript
// 引入 path 包
const path = require('path')


// 引入 html 插件
const HTMLWebpackPlugin = require('html-webpack-plugin')

// 引入 clean 插件
const {CleanWebpackPlugin} = require('clean-webpack-plugin')


// 配置信息都应该写在 module.exports 中
module.exports = {

    // 入口文件
    entry: './src/index.ts',

    // 打包后的文件
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'bundle.js'
    },

    // 设置 webpack 为开发模式
    mode: 'development',

    // webpack 打包时要使用的模块
    module: {
        // 指定加载规则
        rules: [
            {
                // test 指定规则生效的文件
                test: /\.ts$/,

                // 指定要使用的 loader
                use: 'ts-loader',

                // 要排除的文件
                exclude: /node_modules/
            }
        ]
    },

    // 配置插件：
    plugins: [
        new CleanWebpackPlugin(),
        new HTMLWebpackPlugin({ title: 'ts-demo' })
    ],
	
    // 配置引用模块
    resolve: {
        extensions: ['.ts', '.js']
    }

}
```

### 4.3.5 babel

为了让编译后的代码具备更好的兼容性，还需配置 babel 工具。

1. 安装依赖

   ```shell
   npm i -D @babel/core @babel/preset-env babel-loader 
   core-js
   ```

2. 更新 webpack 配置

   ```javascript
   // 引入 path 包
   const path = require('path')
   
   
   // 引入 html 插件
   const HTMLWebpackPlugin = require('html-webpack-plugin')
   
   // 引入 clean 插件
   const { CleanWebpackPlugin } = require('clean-webpack-plugin')
   
   
   // 配置信息都应该写在 module.exports 中
   module.exports = {
   
       // 入口文件
       entry: './src/index.ts',
   
       // 打包后的文件
       output: {
           path: path.resolve(__dirname, 'dist'),
           filename: 'bundle.js',
           environment: {
               arrowFunction: false
           }
       },
   
       // 设置 webpack 为开发模式
       mode: 'development',
   
       // webpack 打包时要使用的模块
       module: {
           // 指定加载规则
           rules: [
               {
                   // test 指定规则生效的文件
                   test: /\.ts$/,
   
                   // 指定要使用的 loader
                   use: [
                       // 配置 babel
                       {
                           loader: 'babel-loader',
                           options: {
                               // 设置预定义环境
                               presets: [
                                   [
                                       '@babel/preset-env',
                                       {
                                           targets: {
                                               "chrome": "100",
                                           },
                                           corejs: "3",
                                           // 按需加载
                                           useBuiltIns: "usage",
                                       }
                                   ]
                               ]
                           }
                       },
                       'ts-loader'
                   ],
   
                   // 要排除的文件
                   exclude: /node_modules/
               }
           ]
       },
   
       // 配置插件：
       plugins: [
           new CleanWebpackPlugin(),
           new HTMLWebpackPlugin({ title: 'ts-demo' })
       ],
   
   
       // 配置引用模块
       resolve: {
           extensions: ['.ts', '.js']
       }
   
   }
   ```

## 5 面向对象

## 5.1 类

### 5.1.1 属性声明

跟 js 相比，不同点在于需要在类中先声明属性及其类型。

```typescript
class Person {
    
    // 声明属性
    name: string
    age: number
    static sum: number = 8

    constructor(name: string, age: number) {
        this.name = name
        this.age = age
    }

    sayName(): void {
        console.log('my name is ' + this.name)
    }

    static getNumber(): number {
        return Person.sum
    }
}


const person = new Person('foo', 10)

console.log(person.age)     // 10
person.sayName()            // my name is foo
console.log(Person.getNumber()) // 8
```

### 5.1.2 属性修饰符

ts 中可以给类中属性添加属性修饰符：public,private,protected

1. public：公有属性。属性可以进行访问或修改。
2. private：私有属性。属性只能在类的内部进行访问或修改。
3. protected：保护属性。属性只能在类的内部或者子类的内部进行访问或修改。

```typescript
class Person {
    public name: string
    public age: number

    constructor(name: string, age: number) {
        this.name = name
        this.age = age
    }
}

const person = new Person('foo', 19)
```

也可以直接将属性声明在构造函数的形参列表中，不必在构造函数中进行属性的初始化赋值，形成以下简写形式：

```typescript
class Person {
    constructor(public name: string, public age: number) {} 
}


const person = new Person('foo', 19)
console.log(person)
```

## 5.2 继承

与 js 无二异。

```typescript
class Person {

    name: string
    age: number

    constructor(name: string, age: number) {
        this.name = name
        this.age = age
    }
}

class Teacher extends Person {
    addr: string
    constructor(name: string, age: number, addr: string) {
        super(name, age)
        this.addr = addr
    }
}

// Teacher {name: 'foo', age: 30, addr: 'bar'}
const teacher = new Teacher('foo', 30, 'bar')
```

## 5.3 抽象类

什么是抽象类：

* 被 abstract 修饰，不能被实例化，只能用作继承。
* 可以包含抽象方法，子类必须对抽象方法进行重写。

```typescript
abstract class Person {
    name: string
    age: number

    constructor(name: string, age: number) {
        this.name = name
        this.age = age
    }

    abstract sayHello(): void
}

class Teacher extends Person {
    constructor(name: string, age: number) {
        super(name, age)
    }
    sayHello(): void {
        console.log('hello, i am a teacher')
    }
}

const teacher = new Teacher('foo', 10)

teacher.sayHello()
```

## 5.4 接口

### 5.4.1 定义

接口用来定义一个类的结构，包括应该存在的属性和方法。

```typescript
interface Person {
    name: string
    age: number
    sayHello(): void
}

const person: Person = {
    name: 'foo',
    age: 18,
    sayHello(): void {
        console.log('hello')
    }
}
```

### 5.4.2 实现

定义在一个类实现一个接口，满足接口的要求。

```typescript
interface Person {
    name: string
    age: number
    sayHello(): void
}

class Teacher implements Person {
    name: string
    age: number
    constructor(name: string, age: number) {
        this.name = name
        this.age = age
    }
    sayHello(): void {
        console.log('hello ')
    }
}
```

### 5.4.3 用于类型声明

接口也可以用来类型声明。

interface 和 type 进行类型声明的区别：

interface 允许重复声明；type 不允许重复声明。

```typescript
type Foo = {
    bar: string
    qux: number
}

// 重复声明，报错
type Foo = {
    baz: string
}
```

```typescript
interface Foo {
    bar: string
    qux: number
}

// 可以重复声明
interface Foo {
    baz: string
}

// 声明多个同名的 interface 时，这些同名的 interface 都要满足。
const foo: Foo = {
    bar: 'bar',
    qux: 10,
    baz: 'baz'
}
```

 ## 5.5 泛型

定义时不指定确切的类型，运行时根据实际情况来确定类型：

```typescript
function fn<T>(x: T):T {
    return x
}

fn<number>(10)
fn<string>('foo')

// 也可以让编译器进行类型推断：
fn(10)
fn('foo')
```

多个泛型：

```typescript
function fn<T, K>(x: T, y: K): { x: T, y: K } {
    return { x, y }
}

fn('foo', 10)
```

给泛型做出一些限制，比如限制实现某些接口的泛型：

```typescript
interface Person {
    name: string
    age: number
}

class Teacher implements Person {
    name: string
    age: number
    constructor(name: string, age: number) {
        this.name = name
        this.age = age
    }
}

function fn<T extends Person>(person: T): number {
    return person.age
}

const teacher = new Teacher('foo', 18)
console.log(fn(teacher))
```

