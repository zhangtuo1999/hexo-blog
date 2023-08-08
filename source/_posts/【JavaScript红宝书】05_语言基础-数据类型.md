---
title: 【JavaScript红宝书】5.语言基础 - 数据类型
tags:
  - 前端
  - JavaScript
excerpt: 《JavaScript高级程序设计（第4版）》笔记。原书第3章：语言基础。主要内容：Undefined 类型、Null 类型、Boolean 类型、Number、String 类型、Symbol 类型和 Object 类型。
abbrlink: 52155ef2
date: 2022-04-27 16:30:27
index_img: ../images/封面图/红宝书.png
---

# 数据类型

ECMAScript 2015 **之前**共有 6 种数据类型。其中，有 5 种**简单数据类型**（原始类型)：Undefined，Null，Boolean，String，Number。还有1种**复杂数据类型**（对象类型）：Object。

ECMAScript 2015 新增了 Symbol 原始类型。

ECMAScript 2020 新增了 BigInt 原始类型。

**至此，ECMAScript 共有 8 种数据类型。包括 7 种原始类型：Undefined，Null，Boolean，String，Number，Symbol，BigInt。还有对象类型 Object**。

## 1 typeof 操作符 

使用 typeof 操作符可以确定变量的数据类型。typeof 操作符的使用结果是 8 个字符串之一："undefined"、"boolean"、"number"、"string"、''bigint''、"object"、"function"和"symbol"。

1. 调用 `typeof null` 会返回 `"object"`，因为 null 被认为是**空对象**的引用。

2. 函数并不是一种数据类型，但是调用 typeof 会返回 `"function"`。

3. typeof 是操作符不是函数，但也可以当作函数进行传参：

   ```javascript
   const a = 1
   console.log(typeof(a))
   ```

## 2 Undefined 类型

Undefined 类型只有一个值 undefined。声明变量但未赋予初始值就相当于赋值 undefined。

1. 对已声明但未初始化的变量调用 typeof，返回的是`"undefined"`。对未声明的变量调用 typeof，返回的也是`"undefined"`。

   ```javascript
   let a;
   console.log(typeof a);	// undefined
   console.log(typeof b);	// undefined
   ```

2. undefined 是一个假值（[**falsy** 值](https://developer.mozilla.org/zh-CN/docs/Glossary/Falsy)）。

## 3 Null 类型

Null 类型只有一个值 null。表示一个空对象指针。

1. 在定义一个将来要保存**对象值**的变量时，建议使用 null 来初始化。

2. undefined 是由 null 派生来的，因此**表面上**相等：

   ```javascript
   console.log(undefined == null);		// true
   console.log(undefined === null);	// false
   ```

3. null 是一个假值。

## 4 Boolean 类型

Boolean 类型有两个字面值：true 和 false。

1. 任何值都有对应的布尔值等价形式，`Boolean()` 转型函数可以将任意值转成相应的布尔值。

   | 数据类型  | 转换为true的值 | 转换为false的值 |
   | --------- | -------------- | --------------- |
   | String    | 非空字符串     | 空串            |
   | Number    | 非0数值        | 0、NaN          |
   | BigInt    | 非0数值        | 0               |
   | Symbol    | 任何符号       | 无              |
   | Object    | 任意对象       | 无              |
   | Null      | 无             | null            |
   | Undefined | 无             | undefined       |

2. if 等流程控制语句会自动执行上面的转换。

## 5 Number 类型

Number 类型使用 IEEE 754 表示整数和浮点值（双精度值）。

### 5.1 整数

1. 最基本的数值字面量是十进制。

   ```javascript
   let num = 5
   ```

2. 八进制字面量：`0`或`0o`开头，然后是八进制数字（0-7）。

   ```javascript
   let a = 076;
   console.log(a);     	// 62
   
   let b = 0o76;
   console.log(b);     	// 62
   ```

   `0`开头表示八进制数时，如果有数字超出八进制数字范围，引擎会忽略前置0，并将后面内容按十进制数处理。

   ```javascript
   let num = 078;
   console.log(num); 		// 78
   ```

   `0o`开头表示八进制数时，如果有数字超出八进制数字范围，会报错。

   ```javascript
   let num = 0o79;
   console.log(num);  		// 报错
   ```

3. 十六进制字面量：`0x` 开头（这里的`0x`严格小写），然后是十六进制数字（0-9，A-F）。

   ```javascript
   let num = 0x2a;
   console.log(num); 		// 42
   ```

注意：

1. 使用八进制和十六进制格式创建的数值在所有**数学操作**种都被视为十进制。

   ```javascript
   let num1 = 043;
   let num2 = 032;
   console.log(num1, num2);  // 35 26
   console.log(num1 + num2); // 61
   ```

2. 正0等于负0。

### 5.2 浮点数

1. 定义浮点数，数值中必须含有**小数点**，而且小数点后至少有一个数字。小数点前不是必须有整数，但是推荐加上。

2. 浮点数内存空间是整数的两倍。为节省空间，ECMAScript 总是想办法把值转化为整数。比如：小数点后没有数字时会被视为整数；小数点后面都是0时会被视为整数。

   ```javascript
   let num = 1.0;
   console.log(num); // 1
   ```

3. 小数点后至少含有 6 个 0 的浮点数会被转换为科学计数法。

   ```javascript
   let num = 0.0000001;
   console.log(num);     // 1e-7
   ```

4. 因为使用了 IEEE 754数值，存在舍入错误。比如：`0.1 + 0.2 !== 0.3`。因此**不要**测试特定的浮点值。

### 5.3 值的范围

1. 由于内存限制，JavaScript 中可以表示的最大值为`Number.MAX_VALUE`，最小值为`Number.MIN_VALUE`。无法表示的正数用`Infinity`表示，无法表示的负数用`-Infinity`表示。

2. 使用`isFinite()`函数来判断是否在最大值和最小值之间。

   ```javascript
   let num = 2 * Infinity;
   console.log(isFinite(num));		// false
   ```

### 5.4 NaN 和 Infinity

`NaN` 是一个特殊的数（即**不是数值**），表示本来要返回数值的操作失败了。

1. 0、-0、+0之间相除会得到 NaN：

   ```javascript
   console.log(0/0);		// NaN
   console.log(-0/+0);		// NaN
   ```

2. 分母是 0、-0、+0，但分子不是。此时得到 `Infinity`和`-Infinity`

   ```javascript
   console.log(1 / 0);	// Infinity
   console.log(-1 / 0);	// -Infinity
   ```

3. 涉及 `NaN` 的操作都返回 `NaN`。

4. `NaN` 不等于包括自己在内的任何数。

5. `isNaN()` 函数可以判断是否**不是数值**。这个函数接收任意类型的参数，并尝试将转换成数值。不能转换成数值则返回 true。

   ```javascript
   console.log(isNaN(Infinity))	// false
   console.log(isNaN(true));		// false (可以转换成数值1)
   console.log(isNaN(false));		// false (可以转换成数值0)
   console.log(isNaN("10"));		// false (可以转换成数值10)
   console.log(isNaN("1.4"));		// false (可以转换成数值1.4)
   console.log(isNaN(2));			// fasle (2)
   console.log(isNaN("1true"));	// true
   console.log(isNaN("true"));		// true
   ```

### 5.5 数值转换

#### 5.5.1 Number() 转型函数

可用于任何数据类型，规则如下：

1. undefined，返回 NaN。null，返回0。
2. true，返回1。false，返回0。

3. 数值直接返回。
4. 字符串：
   * 如果字符串是表示数值字符（整数浮点数都可以），包括前面的正负号，则会转换成一个十进制的数。此时会**忽略前缀0**，也就是说只识别`0o`开头的八进制数和`0x`开头的十六进制数。不识别`0`开头的八进制数，`0`开头的会被当作十进制。
   * **空串返回0**。
   * 其余情况返回 NaN。
5. 对象。调用`valueOf()`方法，并按照以上规则转换返回的值。如果转换结果是`NaN`，则调用`toString()`方法。再按照转换字符串的规则进行转换。

#### 5.5.2 parseInt() 函数

`parseInt()`函数更专注于**字符串**中是否含有数值。接收两个参数，第一个参数是需要转换的字符串，第二个参数是进制，规则如下：

1. 字符串前面的空格会被忽略，从第一个非零数值开始转换。如果第一个字符不是数值字符、正负号，那么立即返回 NaN。也就是说，**空串返回 NaN**。

   ```javascript
   console.log(parseInt(""));      	// NaN  
   console.log(parseInt('qwe'));		// NaN
   ```

2. 继续检测每一个字符，直到字符串末尾或者是遇到**非整数字符**。（小数点不是有效的整数字符）

   ```javascript
   console.log(parseInt("1234blue"));	// 1234
   ```

3. 可以识别不同进制的整数。比如：`0`开头的整数会被识别成[八进制或十进制](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/parseInt)（多数情况下会被识别成十进制，不同环境的实现不同），`0o`开头的整数会被识别成八进制，`0x`开头的整数会被识别成十六进制。

   ```javascript
   console.log(parseInt('073'));		// 73
   ```

4. 在使用 parseInt() 时，建议**指定第二个参数**，用一个整数规定进制。

   ```javascript
   console.log(parseInt('073',8));		// 59
   ```

#### 5.5.3 parseFloat() 函数

`parseFloat()`函数和`parseInt()`函数类似。

1. `parseFloat()`函数解析到字符串末尾或者解析到第一个无效的字符串。
2. 第一个出现的小数点是有效的，第二个出现的小数点及其之后的内容无效，不会被识别。
3. **只接收一个字符串参数**。只能解析十进制数，不能指定底数。

## 6  String 类型

字符串可以使用单引号`''`、双引号`""`或反引号``标识。

### 6.1 字符字面量

`\n` 、`\t`、`\xnn`（十六进制编码表示的字符）、`\unnnn`（十六进制编码表示的 Unicode 字符）

字符字面量被作为单个字符解释。

### 6.2 字符串的不可变性

字符串是不可变的。如果改变字符串的值，会销毁之前的值，并把新值保存到变量中。这是后台发生的，前台无感知。

### 6.3 转换为字符串

#### 6.3.1 toString() 方法

该函数的作用是返回当前值的字符串等价物。

```javascript
let a = true;
console.log(a.toString());		// "true"
let b = 10;
console.log(b.toString());		// "10"
```

1. **undefined 和 null 没有 toString() 方法**。

2. 可以接收一个参数来指定底数。即以什么底数来输出数值的字符串表示。

   ```javascript
   let num = 10
   console.log(num.toString(10));	// 10
   console.log(num.toString(8));	// 12
   console.log(num.toString(16));	// a
   console.log(num.toString(2));	// 1010
   ```

#### 6.3.2 String() 转型函数

如果不确定一个值是不是 undefined 或者 null，可以使用 String() 转型函数。String() 函数的规则如下：

1. 如果值有 toString() 方法则调用值的 toString() 方法。
2. undefined，返回"undefined"；null，返回"null"。

#### 6.3.3 + "" 转成字符串

给一个值加上空串也可以将其转为字符串。

```javascript
let num = 10;
console.log(num + "");	// 10
```

### 6.4 模板字面量

1. 使用反引号标识的字符串是模板字面量。

2. 可以保留换行符，可以跨行定义字符串。因此定义模板时特别有用。

   ```javascript
   let pageHtml = `
   <div>
   	<span></span>
   </div>
   `
   ```

   模板字面量会保留反引号内部的空格，因此可能会看起来缩进不当。

### 6.5 字符串插值

模板字面量支持字符串插值：

```javascript
let num = 1;
let sum = 2;
let stirng = `${num} + ${num} = ${sum}`;	// 1 + 1 = 2 
```

1. 插入的值会使用 toString() 方法强制转为字符串，undefined 插值显示 "undefined"，null 插值显示 "null"。
2. 任何 **JavaScript 表达式**都可以用于插值表达式，可以调用函数和方法。
3. 可以嵌套使用。
4. 可以插入自己之前的值。

## 7 Symbol 类型

Symbol（符号） 是 ES6 新增的数据类型。符号实例是唯一的不可变的。符号的用途是保证对象属性使用唯一的标识符，不会发生属性冲突。符号的作用是用来创建唯一记号，进而用作非字符串形式的对象属性。

### 7.1 基本用法

1. 使用 Symbol() 函数初始化。

   ```javascript
   let s = Symbol()
   console.log(s);		// Symbol()
   ```

2. 可以传入一个字符串参数作为符号的描述。

   ```javascript
   let s = Symbol("foo");	
   console.log(s)		// Symbol(foo)
   ```
   
   这个字符串参数和符号没有任何关系，仅仅是为了调试代码。
   
   ```javascript
   let s1 = Symbol("foo");
   let s2 = Symbol("foo");
   console.log(s1 === s2);		// false
   ```

3. 符号没有字面量。只要创建 Symbol() 实例并将其作为对象的新属性，就可以保证它不会覆盖原有属性。
4. Symbol() 函数不能用作构造函数，不能与 `new` 关键字一同使用。

### 7.2 全局符号注册表

如果运行时的不同部分需要**共享和重用**符号，可以使用一个字符串为键，在**全局符号注册表**中创建并使用符号。

#### 7.2.1 Symbol.for() 方法

```javascript
let fooSymbol = Symbol.for('foobar')
console.log(fooSymbol);	// Symbol(foobar)
```

Symbol.for() 和 Symbol() 定义的符号不相同。

```javascript
let fooSymbol = Symbol("foobar");
let barSymbol = Symbol.for("foobar");
console.log(fooSymbol === barSymbol);	// false
```

Symbol.for() 对每个字符串执行**幂等操作**。第一次使用某个字符串时，检查全局运行时注册表，发现不存在对应的符号，于是会生成一个符号实例并添加到注册表中。后续使用相同的字符串调用时，会同样检查注册表，发现存在于该字符串对应的符号，则返回这个实例。

```javascript
let fooSymbol = Symbol.for("foobar");	// 创建新符号
let barSymbol = Symbol.for("foobar");	// 返回已有的符号
console.log(fooSymbol === barSymbol);	// true
```

#### 7.2.2 Symbol.keyFor() 方法

使用`Symbol.keyFor()` 来查询全局注册表。这个方法接收符号，返回全局符号对应的字符串。如果查询不到字符串则返回 undefined。

```javascript
let barSymbol = Symbol.for("foobar");	
console.log(Symbol.keyFor(barSymbol));	// foobar
```

### 7.3 使用符号作为属性

```javascript
let s1 = Symbol("foo");
let s2 = Symbol("bar");
let obj = {
	[s1]: "foo val",
};
obj[s2]='bar val'
console.log(obj);	// {Symbol(foo): 'foo val', Symbol(bar): 'bar val'}
```

## 8 BigInt 类型

参考 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/BigInt)。

## 9 Object 类型

ECMAScript 中，对象是一组数据和功能的集合。

1. 对象通过 new 操作符后跟上对象类型的名称来创建。也可以通过创建 Object 类型实例来创建自己的对象，随后添加属性和方法。

   ```javascript
   let o = new Object()
   ```

   ECMAScript 只要求在给构造函数传递参数时使用括号，如果不传参数，可以省略括号。

   ```javascript
   let o = new Object
   ```

2. Object 是其它对象的基类，Object 类型的所有属性和方法在派生的对象上同样存在。
3. 每个 Object 实例上都有下面的属性和方法：
   * `constructor`：用于创建当前对象（函数）。
   * `hasOwnProperty(propertyName)`：判断当前对象实例（而不是原型）上是否存在在给定的属性。
   * `isPrototypeof(object)`：判断当前对象是否为另一个对象的原型。
   * `propertyIsEnumerable(propertyName)`：判断给定的属性是否可枚举。
   * `toLocaleString()`：返回对象的字符串表示。该字符串反映对象所在的**本地化环境**。
   * `toString()`：返回对象的字符串表示。
   * `valueOf()`：返回对象的原始值。
