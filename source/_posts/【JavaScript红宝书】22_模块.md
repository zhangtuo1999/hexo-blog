---
title: 【JavaScript红宝书】22.模块
tags:
  - 前端
  - JavaScript
keywords:
  - JavaScript
description: 《JavaScript高级程序设计（第4版）》第26章：模块。
excerpt: 《JavaScript高级程序设计（第4版）》第26章：模块。
abbrlink: 871431d2
date: 2022-07-07 15:13:20
index_img: ../images/封面图/红宝书.png
---

## 1 模块模式

在 ECMAScript 2015 模块规范出现之前，使用模块模式的库必须基于 JavaScript 语法特性**伪造**出类似模块的行为。 

**模块模式**的思想：把逻辑分块，各自封装，相互独立，每个块自行决定对外暴露什么，同时自行决定引入哪些外部代码。这些思想是 JavaScript **模块系统**的基础。

### 1.1 模块标识符

每个模块都有一个可用于引用它的标识符。模块的标识符可用于检索模块。完善的模块系统不会存在标识冲突的问题，任何模块都应该无歧义的引用其它模块。

### 1.2 模块依赖

模块系统的核心是管理依赖。本地模块为了正常运行可能声明一组所需的外部模块。模块系统应该检索这些依赖以保证本地模块的运行。

### 1.3 模块加载

当一个外部模块被指定为依赖时，本地模块希望在执行前，依赖就已经准备好并已初始化。

### 1.4 入口

因为 JavaScript 是顺序执行的，因此相互依赖的模块必须指定一个模块作为入口，也就是代码的起点。

### 1.5 异步依赖

因为 JavaScript 可以异步执行，所以可以按需加载。可以让 JavaScript 在必要的时候加载模块，并在模块记载完成后提供回调。

### 1.6 动态依赖

有些模块系统允许开发者在程序中动态添加模块。

```javascript
if(...){
	require('...')
}
```

### 1.7 静态分析

发送到浏览器的代码经常会被静态分析，分析工具会检查代码结构并推断其行为。对静态分析友好的代码，可以让模块打包系统更容易将代码分析为较少的文件。复杂的模块行为，比如动态依赖，会导致静态分析更困难。

### 1.8 循环依赖

在有循环依赖的模块系统中，模块的加载顺序可能出人意料。不过只要恰当封装模块，加载顺序不会影响程序的运行。

## 2 ES6 之前的模块加载器

在 ES6 原生支持模块之前，使用模块的 JavaScript 代码希望使用默认没有的语言特性。因此需要按照符合某种规范的**模块语法**来编写代码。另外还需要单独的**模块工具**将模块语法与运行时连接起来。 这里的模块语法和模块工具通常需要额外加载库或者在构建时完成预处理。

### 2.1 CommonJS（同步模块）

#### 2.1.1 简介

CommonJS 规范概述了**同步声明依赖**的模块定义。这个规范主要用于在**服务端**实现模块化代码，也可以用于定义在浏览器中使用的模块依赖。CommonJS 模块语法**不能**在浏览器中直接运行。

> Node.js 使用了轻微修改版本的 CommonJS。

#### 2.1.2 语法

CommonJS 规范定义使用 require() 指定依赖，使用 module.exports 对象定义自己的公共 API。

```javascript
var moduleB = require("./moduleB");
module.exports = {
  stuff: moduleB.doStuff()
};
```

> Node.js 中模块标识符可能指向文件，也可能指向含有 index.js 文件的目录。

#### 2.1.3 特点

CommonJS 规范的特点：

* 无论一个模块在 require() 中被引用多少次，模块永远是单例，只会被加载一次。
* 模块第一次加载后会被缓存，后续加载会取得缓存的模块。
* 模块加载顺序由**依赖图**决定。
* 模块加载是同步操作。
* 模块**不会**指定自己的标识符，它们的标识符由其在模块文件层级中的位置决定。指向模块定义的路径可能引用一个目录，也可能是一个 JavaScript 文件。

#### 2.1.4 用途

模块的一个主要用途是托管类的定义。

```javascript
// moduleA
class A {}
module.exports = A

// B
var A = require('./moduleA')
var a = new A()
```

也可以将类实例作为导出值。

```javascript
class A {}
module.exports = new A()
```

#### 2.1.5 打包

CommonJS 依赖几个全局属性如 require 和 module.exports。如果想在浏览器中直接使用 CommonJS 模块，就要对其非原生模块语法进行转换。模块级代码也需要和浏览器进行封装隔离，因为没有封装的 CommonJS 代码在浏览器中会创建全局变量，不符合模块模式。

常见解决方法是对模块文件进行打包，将模块代码封装在函数闭包中，最终只提供一个文件。

### 2.2 AMD（异步模块定义）

#### 2.2.1 简介

CommonJS 以服务端为目标环境，而 AMD 以浏览器为目标执行环境。AMD 的一般策略是让模块声明自己的依赖，而运行在浏览器中的模块系统按需获取依赖，并在依赖加载完成后立即执行依赖它们的模块。

AMD 模块实现的核心是用函数包装模块的定义。这样可以防止声明全局变量，并允许加载器库控制合适加载模块。包装函数便于模块代码的移植，因为包装函数内部所有模块代码使用的都是原生 JavaScript 结构。

#### 2.2.2 语法

包装模块的函数是全局 define 的参数，它由 AMD 加载库的实现定义。AMD 模块可以使用字符串标识符指定自己的依赖，而 AMD 加载器会在所有依赖模块加载完毕后立即调用模块工厂函数。与 CommonJS 不同，AMD 支持可选的为模块指定字符串标识符。

```javascript
// ID 为'moduleA'的模块定义。moduleA 依赖moduleB，
// moduleB 会异步加载

define('moduleA', ['moduleB'], function(moduleB) {
  return {
    stuff: moduleB.doStuff()
  };
});
```

AMD 也支持 require 和 exports 对象，通过它们可以在 AMD 模块工厂函数内部定义 CommonJS 风格的模块。

### 2.3  UMD（通用模块定义）

为了统一 CommonJS 和 AMD 生态系统，通用模块定义（UMD）规范应运而生。UMD 可以创建这两个系统都可以使用的模块代码。

## 3 ES6 模块

ES6 引入了模块规范，全方位简化了之前的模块加载器。ES6 模块是 AMD 和 CommonJS 的集大成者。

### 3.1 模块标签及定义

ECMAScript 6 模块是作为一整块 JavaScript 代码而存在的。带有 `type="module"` 属性的 `<script>`标签会告诉浏览器相关代码应该作为模块执行，而不是作为传统的脚本执行。模块可以嵌入在网页中，也可以作为外部文件引入：

```html
<script type="module">
// 模块代码
</script>

<script type="module" src="path/to/myModule.js"></script>
```

即使与常规 JavaScript 文件处理方式不同，JavaScript 模块文件也没有专门的**内容类型**（MIME 类型）。

与传统脚本不同，所有模块都会像`<script defer>`加载的脚本一样按**顺序**执行。解析到 `<script type="module">` 标签后会立即下载模块文件，但执行会延迟到文档解析完成。无论对嵌入的模块代码，还是引入的外部模块文件，都是这样。`<script type="module">` 在页面中出现的顺序就是它们执行的顺序。与 `<script defer>` 一样，修改模块标签的位置，无论是在 `<head>`还是在 `<body>`中，只会影响文件什么时候加载，而不会影响模块什么时候加载。

```html
<script type="module">console.log("A");</script>
<script type="module">console.log("B");</script>
<script>console.log("C");</script>

// 执行结果：C A B
```

也可以给模块标签添加 async 属性。这样影响就是双重的：不仅模块执行顺序不再与`<script>`标签在页面中的顺序绑定，模块也不会等待文档完成解析才执行。不过，入口模块仍必须等待其依赖加载完成。

与`<script type="module">`标签关联的 ES6 模块被认为是模块图中的入口模块。一个页面上有多少个入口模块没有限制，重复加载同一个模块也没有限制。同一个模块无论在一个页面中被加载多少次，也不管它是如何加载的，实际上都只会加载一次。

```html
<script type="module">
import './moduleA.js'
<script>
    
<script type="module">
import './moduleA.js'
<script>
    
<script type="module" src="./moduleA.js"></script>
<script type="module" src="./moduleA.js"></script>

// moduleA 只会被加载一次
```

嵌入的模块定义代码不能使用 import 加载到其他模块。只有通过外部文件加载的模块才可以使用 import 加载。因此，嵌入模块只适合作为入口模块。

### 3.2 模块加载

ECMAScript 6 模块的独特之处在于，既可以通过浏览器原生加载，也可以与第三方加载器和构建工具一起加载。有些浏览器还没有原生支持 ES6 模块，因此可能还需要第三方工具。事实上，很多时候使用第三方工具可能会更方便。

完全支持 ECMAScript 6 模块的浏览器可以从顶级模块加载整个依赖图，且是异步完成的。浏览器会解析入口模块，确定依赖，并发送对依赖模块的请求。这些文件通过网络返回后，浏览器就会解析它们的内容，确定它们的依赖，如果这些二级依赖还没有加载，则会发送更多请求。这个异步递归加载过程会持续到整个应用程序的依赖图都解析完成。解析完依赖图，应用程序就可以正式加载模块了。

这个过程与 AMD 风格的模块加载非常相似。模块文件按需加载，且后续模块的请求会因为每个依赖模块的网络延迟而同步延迟。即，如果 moduleA 依赖 moduleB，moduleB 依赖 moduleC。浏览器在对 moduleB 的请求完成之前并不知道要请求 moduleC。这种加载方式效率很高，也不需要外部工具，但加载大型应用程序的深度依赖图可能要花费很长时间。

### 3.3 模块行为

ECMAScript 6 模块借用了 CommonJS 和 AMD 的很多优秀特性。

* 模块代码只在加载后执行。
* 模块只能加载一次。
* 模块是单例。
* 模块可以定义公共接口，其他模块可以基于这个公共接口观察和交互。
* 模块可以请求加载其他模块。

* 支持循环依赖。

ES6 模块系统也增加了一些**新行为**。

* ES6 模块默认在严格模式下执行。
* ES6 模块不共享全局命名空间。
* 模块顶级 this 值是 undefined（常规脚本中是 window）。
* 模块中的 var 声明不会添加到 window 对象。
* ES6 模块是异步加载和执行的。

浏览器运行时在知道应该把某个文件当成模块时，会有条件地按照上述 ECMAScript 6 模块行为来施加限制。与`<script type="module">`关联或者通过 import 语句加载的 JavaScript 文件会被认定为模块。

### 3.4 模块导出

ES6 模块支持两种导出：**命名导出**和**默认导出**。

使用 export 关键字导出模块。导出语句必须在模块**顶级**，不能嵌套在某个块中：

```javascript
// 允许
export ...

// 不允许
if (condition) {
	export ...
}
```

导出值对模块内部 JavaScript 的执行没有直接影响，因此 export 语句与导出值的相对位置或者 export 关键字在模块中出现的顺序没有限制。export 语句甚至可以出现在它要导出的值之前：

```javascript
// 允许
const foo = 'foo';
export { foo };

// 允许
export const foo = 'foo';

// 允许，但应该避免
export { foo };
const foo = 'foo';
```

#### 3.4.1 命名导出

**命名导出**（named export）就好像**模块是被导出值的容器**。**行内命名导出**，顾名思义，可以在同一行执行变量声明。下面展示了一个声明变量同时又导出变量的例子。外部模块可以导入这个模块，而 foo 将成为这个导入模块的一个属性：

```javascript
// a.js 导出
export const foo = 'foo';

// b.js 导入，必须和导出的命名一致
import {foo} from './a.js'		
console.log(foo) // foo
```

变量声明跟导出可以不在一行。可以在 export 子句中执行声明并将标识符导出到模块的其他地方：

```javascript
// a.js 导出
const foo = 'foo';
export { foo };

// b.js 导入
import {foo} from './a.js'
console.log(foo) // foo
```

导出时也可以提供别名，别名必须在 export 子句的大括号语法中指定。因此，声明值、导出值和为导出值提供别名不能在一行完成。在下面的例子中，导入这个模块的外部模块可以使用 myFoo 访问导出的值：

```javascript
// a.js 导出
const foo = 'foo';
export { foo as myFoo };

// b.js 导入
import {myFoo} from './a.js'
console.log(myFoo)	// foo
```

因为 ES6 命名导出可以将模块作为容器，所以可以在一个模块中声明多个命名导出。导出的值可以在导出语句中声明，也可以在导出之前声明：

```javascript
// a.js 导出
export const foo = 'foo';
export const bar = 'bar';
export const baz = 'baz';

// b.js 导入
import {foo, bar, baz} from './a.js'
console.log(foo, bar, baz)	// foo, bar, baz
```

考虑到导出多个值是常见的操作，ES6 模块也支持对导出声明分组，可以同时为部分或全部导出值指定别名：

```javascript
// a.js 导出
const foo = 'foo';
const bar = 'bar';
const baz = 'baz';
export { foo, bar as myBar, baz };

// b.js 导入
import {foo, myBar, baz} from './a.js'
console.log(foo, myBar, baz) // foo, bar, baz
```

#### 3.4.2 默认导出

**默认导出**（default export）就好像**模块与被导出的值是一回事**。默认导出使用 default 关键字将一个值声明为默认导出，**每个模块只能有一个默认导出**。重复的默认导出会导致 SyntaxError。下面的例子定义了一个默认导出，外部模块可以导入这个模块，而这个模块本身就是 foo 的值：

```javascript
// a.js 导出
const foo = 'foo';
export default foo;

// b.js 导入，可以和导出的值命名不一致
import a from './a.js'
console.log(a)	// foo
```

另外，ES6 模块系统会识别作为别名提供的 default 关键字。此时，虽然对应的值是使用命名语法导出的，实际上则会成为默认导出：

```javascript
const foo = 'foo';
export { foo as default }; // 等同于export default foo;
```

#### 3.4.3 同时导出

因为命名导出和默认导出不会冲突，所以ES6 支持在一个模块中同时定义这两种导出：

```javascript
// a.js 导出
const foo = 'foo';
const bar = 'bar';
export { bar };
export default foo;

// b.js 导入
import {bar} from './a.js'
import a from './a.js'
console.log(bar, a) // bar foo
```

这两个 export 语句可以组合为一行：

```javascript
// a.js
const foo = 'foo';
const bar = 'bar';
export { foo as default, bar };
```

#### 3.4.4 export 语句规范

ES6 规范对不同形式的 export 语句中可以使用什么不可以使用什么规定了限制。某些形式允许声明和赋值，某些形式只允许表达式，而某些形式则只允许简单标识符。注意，有的形式使用了分号，有的则没有：

```javascript
// 命名行内导出
export const baz = 'baz';
export const foo = 'foo', bar = 'bar';
export function foo() {}
export function* foo() {}
export class Foo {}
// 命名子句导出
export { foo };
export { foo, bar };
export { foo as myFoo, bar };
// 默认导出
export default 'foo';
export default 123;
export default /[a-z]*/;
export default { foo: 'foo' };
export { foo, bar as default };
export default foo
export default function() {}
export default function foo() {}
export default function*() {}
export default class {}
// 会导致错误的不同形式：
// 行内默认导出中不能出现变量声明
export default const foo = 'bar';
// 只有标识符可以出现在 export 子句中
export { 123 as foo }
// 别名只能在export 子句中出现
export const foo = 'foo' as myFoo;
```

> 注意：什么可以或不可以与 export 关键字出现在同一行可能很难记住。一般来说，声明、赋值和导出标识符最好分开。这样就不容易搞错了，同时也可以让 export 语句集中在一块。

### 3.5 模块导入

模块可以通过使用 import 关键字使用其他模块导出的值。与 export 类似，import 必须出现在模块的**顶级**：

```javascript
// 允许
import ...
// 不允许
if (condition) {
	import ...
}
```

import 语句被提升到模块顶部。因此，与 export 关键字类似，import 语句与使用导入值的语句的相对位置并不重要。不过，还是推荐把导入语句放在模块顶部。

```javascript
// 允许
import { foo } from './fooModule.js';
console.log(foo); // 'foo'

// 允许，但应该避免
console.log(foo); // 'foo'
import { foo } from './fooModule.js';
```

注意：

1. 模块标识符可以是相对于当前模块的相对路径，也可以是指向模块文件的绝对路径。它必须是纯字符串，不能是动态计算的结果。例如，不能是拼接的字符串。

2. 如果在浏览器中通过标识符原生加载模块，则文件必须带有 .js 扩展名，不然可能无法正确解析。不过，如果是通过构建工具或第三方模块加载器打包或解析的 ES6 模块，则可能不需要包含文件扩展名。

3. 不是必须通过导出的成员才能导入模块。如果不需要模块的特定导出，但仍想加载和执行模块以利用其副作用，可以只通过路径加载它：

   ```javascript
   // foo.js
   console.log('foo')
   
   // b.js
   import './foo.js';	// foo
   ```

4. 导入对模块而言是只读的，实际上相当于 const 声明的变量。在使用执行批量导入时，赋值给别名的命名导出就好像使用 Object.freeze() 冻结过一样。直接修改导出的值是不可能的，但可以修改导出对象的属性。

   同样，也不能给导出的集合添加或删除导出的属性。要修改导出的值，必须使用有内部变量和属性访问权限的导出方法。

   ```javascript
   import foo, * as Foo './foo.js';
   foo = 'foo'; // 错误
   Foo.foo = 'foo'; // 错误
   foo.bar = 'bar'; // 允许
   ```

命名导出和默认导出的区别也反映在它们的导入上。

#### 3.5.1 命名导入

命名导出可以使用**批量**获取并赋值给保存导出集合的别名，而无须列出每个标识符：

```javascript
// foo.js 导出
const foo = 'foo', bar = 'bar', baz = 'baz';
export { foo, bar, baz }

// bar.js 导入
import * as Foo from './foo.js';
console.log(Foo.foo); // foo
console.log(Foo.bar); // bar
console.log(Foo.baz); // baz
```

要导入时**指定**，需要把标识符放在 import 子句中。使用 import 子句可以为导入的值指定别名：

```javascript
// foo.js 命名导出
const foo = 'foo', bar = 'bar', baz = 'baz';
export { foo, bar, baz }

// bar.js 导入
import { foo, bar, baz as myBaz } from './foo.js';
console.log(foo); // foo
console.log(bar); // bar
console.log(myBaz); // baz
```

#### 3.5.2 默认导入

默认导出就好像整个模块就是导出的值一样。可以使用 default 关键字并提供别名来导入。也可以不使用大括号，此时指定的标识符就是默认导出的别名：

```javascript
// foo.js 默认导出
const foo = 'foo'
export default foo

// 等效
import { default as foo } from './foo.js';
import foo from './foo.js';
```

#### 3.5.3 同时导入

如果模块同时导出了命名导出和默认导出，则可以在import 语句中同时取得它们。可以依次列出特定导出的标识符来取得，也可以使用*来取得：

```javascript
import foo, { bar, baz } from './foo.js';
import { default as foo, bar, baz } from './foo.js';
import foo, * as Foo from './foo.js';
```

### 3.6 模块转移导出

模块导入的值可以直接通过管道转移到导出。此时，也可以将默认导出转换为命名导出，或者相反。
如果想把一个模块的所有命名导出集中在一块，可以像下面这样在 bar.js 中使用 * 导出：

```javascript
// 在 bar.js 中
export * from './foo.js';
```

这样，foo.js 中的所有命名导出都会出现在导入 bar.js 的模块中。如果 foo.js 有默认导出，则该语法会忽略它。使用此语法也要注意导出名称是否冲突。如果 foo.js 导出 baz，bar.js 也导出baz，则最终导出的是 bar.js 中的值。这个“重写”是静默发生的：

```javascript
// 在 foo.js 中
export const baz = 'origin:foo';

// 在 bar.js 中
export * from './foo.js';
export const baz = 'origin:bar';

// 在 main.js 中
import { baz } from './bar.js';
console.log(baz); // origin:bar
```

此外也可以明确列出要从外部模块转移本地导出的值。该语法支持使用别名：

```javascript
export { foo, bar as myBar } from './foo.js';
```

类似地，外部模块的默认导出可以重用为当前模块的默认导出：

```javascript
export { default } from './foo.js';
```

这样不会复制导出的值，只是把导入的引用传给了原始模块。在原始模块中，导入的值仍然是可用的，与修改导入相关的限制也适用于再次导出的导入。
在重新导出时，还可以在导入模块修改命名或默认导出的角色。比如，可以像下面这样将命名导出指定为默认导出：

```javascript
export { foo as default } from './foo.js';
```

### 3.7 工作者模块

ECMAScript 6 模块与 Worker 实例完全兼容。在实例化时，可以给工作者传入一个指向模块文件的路径，与传入常规脚本文件一样。Worker 构造函数接收第二个参数，用于说明传入的是模块文件。
下面是两种类型的 Worker 的实例化行为：

```javascript
// 第二个参数默认为{ type: 'classic' }
const scriptWorker = new Worker('scriptWorker.js');
const moduleWorker = new Worker('moduleWorker.js', { type: 'module' });
```

在基于模块的工作者内部，self.importScripts() 方法通常用于在基于脚本的工作者中加载外部脚本，调用它会抛出错误。这是因为模块的 import 行为包含了 importScripts()。

### 3.8 兼容

ECMAScript 模块的兼容是个渐进的过程，能够同时兼容支持和不支持的浏览器对早期采用者是有价值的。对于想要尽可能在浏览器中原生使用 ECMAScript 6 模块的用户，可以提供两个版本的代码：基于模块的版本与基于脚本的版本。如果嫌麻烦，可以使用第三方模块系统（如 SystemJS ）或在构建时将 ES6 模块进行转译，这都是不错的方案。

第一种方案涉及在服务器上检查浏览器的用户代理，与支持模块的浏览器名单进行匹配，然后基于匹配结果决定提供哪个版本的 JavaScript 文件。这个方法不太可靠，而且比较麻烦，不推荐。

更好、更优雅的方案是利用脚本的type 属性和 nomodule 属性。浏览器在遇到`<script>`标签上无法识别的 type 属性时会拒绝执行其内容。对于不支持模块的浏览器，这意味着`<script type="module">`不会被执行。因此，可以在`<script type="module">`标
签旁边添加一个回退`<script>`标签：

```javascript
 // 不支持模块的浏览器不会执行这里的代码
<script type="module" src="module.js"></script>

// 不支持模块的浏览器会执行这里的代码
<script src="script.js"></script>
```

当然，这样一来支持模块的浏览器就有麻烦了。此时，前面的代码会执行两次，显然这不是我们想要的结果。为了避免这种情况，原生支持 ECMAScript 6 模块的浏览器也会识别 nomodule 属性。此属性通知支持 ES6 模块的浏览器不执行脚本。不支持模块的浏览器无法识别该属性，从而忽略这个属性的存在。因此，下面代码会生成一个设置，在这个设置中，支持模块和不支持模块的浏览器都只会执行一段脚本：

```javascript
// 支持模块的浏览器会执行这段脚本
// 不支持模块的浏览器不会执行这段脚本
<script type="module" src="module.js"></script>
// 支持模块的浏览器不会执行这段脚本
// 不支持模块的浏览器会执行这段脚本
<script nomodule src="script.js"></script>
```

