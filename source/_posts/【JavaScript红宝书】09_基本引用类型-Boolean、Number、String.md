---
title: 【JavaScript红宝书】9.基本引用类型-Boolean、Number、String
tags:
  - 前端
  - JavaScript
keywords: JavaScript
description: 《JavaScript高级程序设计（第4版）》第5章：基本引用类型。主要内容：Boolean、Number、String
excerpt: 《JavaScript高级程序设计（第4版）》第5章：基本引用类型。主要内容：Boolean、Number、String
abbrlink: 9d23972e
date: 2022-05-02 23:03:09
index_img: ../images/封面图/红宝书.png
---

# 原始值包装类型

为了方便操作原始值，ECMAScript 提供了3种特殊的引用类型：Boolean、Number 和 String。这些类型具有和各自原始类型对应的特殊行为。每当用到某个原始值的方法或者属性时，后台都会创建一个相应的**原始包装类型**对象，从而暴露出操作原始值的方法。

```javascript
let s1 = "string";
let s2 = s1.substring(2);
```

s1 是一个包含字符串的变量，是一个原始值。第二行在 s1 上调用了`subString()`方法。原始值不是对象，按理说不该有方法，而实际上这个例子又能运行。

这是因为后台做了很多的处理：当第二行访问 s1 时，是以**读模式**访问的，也就是要从内存中读取保存变量的值。以读模式访问字符串的时候，后台都会执行三步：

1. 创建一个 String 类型的实例。
2. 调用实例上的特定方法。
3. 销毁实例。

可以想象成下面的代码：

```javascript
let s1 = new String('string')
let s2 = s1.substring(2);
s1 = null
```

这种行为可以让原始值拥有对象的行为，对于布尔值和数值，以上3步也会在后台发生，只不过使用的是 Boolean和 Number 包装类型。

引用类型和原始值包装类型的主要区别在于**生命周期**。通过 new 实例化引用类型后，得到的实例会在离开作用域时销毁。而自动创建的原始值包装对象只存在于访问它的那行代码之间。这意味着不能再运行时给原始值添加属性和方法：

```javascript
let s1 = 'string'
s1.foo = 'bar'			// 不报错
console.log(s1.foo);	// undefined
```

可以显式地使用 Boolean、Number、String 构造函数创建原始值包装对象，但是会让开发者疑惑到底这是原始值还是包装对象。在原始值包装类型的实例上用 typeof 会返回"object"。不推荐显式使用原始值包装对象。

Object() 构造函数作为一个工厂方法，可以根据传入值的类型返回相应的原始值包装类型的实例。如果传给 Object 构造方法的是字符串，则会创建一个 String 实例对象；如果是数值，则会创建 Number 实例对象；布尔值会创建Boolean 实例对象。

```javascript
let obj = new Object('string')
console.log(obj instanceof String);	// true
```

使用 new 调用原始值包装类型的构造函数和调用同名的转型函数**不一样**：

```javascript
let val = "25";
let num = Number(val);		// 转型函数
console.log(typeof num);	// number
let obj = new Number(val);	// 构造函数
console.log(typeof obj);	// object	
```

## 1 Boolean

Boolean 是对应布尔值的引用类型。要创建一个 Boolean 对象，就使用 Boolean() 构造函数并传入 true 或者 false。

```javascript
let obj = new Boolean(true);
console.log(obj);	// Boolean {true}
```

Boolean 重写了 valueOf() 方法。Boolean实例返回一个原始值 true 或者 false。

toLocaleString() 和 toString() 方法也被重写，返回字符串 "true" 或者 "false"。

```javascript
let obj = new Boolean(true);
console.log(obj.valueOf());		// true
console.log(obj.toString());	// "true"
```

Boolean **对象**在布尔表达式中会自动转化成 true，无论创建这个对象时传入的是 true 还是 false。

原始值（true、false）和引用值（Boolean 实例）的几个区别：

1. typeof 操作符对原始值返回"boolean"，对引用值返回"object"。
2. instanceof 操作符对 Boolean 对象返回 true，对原始值返回 false。

## 2 Number

Number 是对应数值的引用类型。要创建一个 Number 对象，就使用 Number 构造函数并传入一个数值。

```javascript
let obj = new Number(10);
console.log(obj);	// Number{10}
```

Number 类型重写了 valueOf()、toString() 方法。valueOf() 方法返回 Number 对象表示的原始数值，toLocaleString()、toString() 方法返回数值字符串。

toString() 方法可选地接收一个表示基数的参数，并返回相应基数的数值字符串。

```javascript
let num = new Number(10)
console.log(num.toString(2));   // 1010
console.log(num.toString(8));   // 12
console.log(num.toString(16));  // a
```

除了继承的方法，Number 类型还提供了几个用于将数值格式化为字符串的方法。

### 2.1 toFixed() 返回指定小数位数的字符串

toFixed() 方法返回包含**指定小数位数**的字符串。位数不够的填0，位数超出则四舍五入。

```javascript
let num = 12.345
console.log(num.toFixed(5));	// 12.34500
console.log(num.toFixed(2));	// 12.35
```

### 2.2 toExponential() 返回科学计数法表示的字符串

 toExponential() 方法返回**科学计数法**表示的字符串。也接收一个参数，表示结果中**小数的位数**。

```javascript
let num = 12.0000000000345
console.log(num.toExponential());	// 1.20000000000345e+1
console.log(num.toExponential(2));	// 1.20e+1
```

### 2.3 toPrecision() 返回最适合的形式表示的字符串

toPrecision() 根据情况返回**最合理**的输出结果。接收一个参数，表示结果中**数字的总位数**（不包含指数）。

本质上，toPrecision() 会根据数值和精度来决定调用 toFixed() 还是 toExponential()。

```javascript
let num1 = 12.000000000345;
let num2 = 12.345;
console.log(num1.toPrecision(1)); // 1e+1	（舍入为10）
console.log(num1.toPrecision(2)); // 12		（舍入为12）
console.log(num1.toPrecision(3)); // 12.0	（舍入为12.0）
console.log(num2.toPrecision(3)); // 12.3
```

解释：因为 num1 不能用1位数精确表示，所以这个方法会将 num1 舍入为 10。

### 2.4 Number.isInteger() 辨别是否保存为整数

Number.isInteger() 方法可以用来辨别一个数是否**保存为整数**。

```javascript
console.log(Number.isInteger(10));      // true
console.log(Number.isInteger(10.0));    // true
console.log(Number.isInteger(10.1));    // false
```

### 2.5 Number.isSafeInteger() 辨别是否安全整数

IEEE 754 格式有一个特殊的数值范围，这个范围中的二进制值可以表示为一个整数。这个数值范围是：`Number.MIN_SAFE_INTEGER` 到`Number.MAX_SAFE_INTEGER`。超过了这个范围，即使保存为整数，IEEE 754 格式也意味着表示为完全不同的数值。

为了鉴别一个数是否在这个数值内，可以使用 Number.isSafeInteger() 方法。

```javascript
console.log(Number.isSafeInteger(2 ** 53 -1));	// true
console.log(Number.isSafeInteger(2 ** 54));		// false
```

## 3 String

String 是对应布尔值的引用类型。要创建一个 String 对象，就使用 String() 构造函数并传入字符串原始值。

```javascript
let obj = new String('string')
console.log(typeof obj);        // object
```

String 对象继承的三个方法 valueOf()、toLocaleString()、toString() 都返回对象的原始字符串表示。

### 3.1 字符串拼接和截取

#### 3.1.1 concat() 字符串拼接

concat() 方法，用于将一个或多个字符串拼接成一个**新**的字符串。concat() 会返回一个新值，而不会改变原始值。

```javascript
let s = "hello ";
console.log(s.concat("world"));    // hello world
console.log(s);                    // hello 
```

concat() 方法可以接受任意个数的参数，因此可以一次拼接多个字符串。

```javascript
let s = "hello";
console.log(s.concat(" ", "world", "!"));	// hello world!
```

虽然 concat() 方法可以拼接字符串，但是更多时候使用加号操作符（+）。多数情况下，使用加号更加方便。

```javascript
let s = "hello";
console.log(s + " " + "world" + "!");		// hello world! 
```

#### 3.1.2 slice() 提取子字符串

`slice()` 方法提取某个字符串的一部分，并返回一个**新**的字符串，且不会改动原字符串。slice() 方法接收两个参数，第一个参数表示子字符串的开始位置，第二个参数表示子字符串的结束位置。**含头不含尾**。

```javascript
let s = "12345678";
let sub = s.slice(1, 3);
console.log(sub);   // '23'
```

1. 如果第二个参数**缺失**（不是0），则提取到字符串**末尾**。

2. 当参数是**负数**时，slice() 方法将所有的负值参数都当成**字符串长度加上负参数值**。

   ```javascript
   let s = "12345678";
   let sub = s.slice(-5, -2); // 相当于s.slice(3,6)
   console.log(sub); 	// "456"
   ```

   如果加上字符串长度后，第一个参数仍是负数，则从第一位开始截取。

   ```javascript
   let s = "12345678";
   let sub = s.slice(-10, 5);	// -10加上8后仍是负数
   console.log(sub);	// 12345
   ```

   如果加上字符串长度后，第二个参数仍是负数，则返回空串。

   ```javascript
   let s = "12345678";
   let sub = s.slice(1, -10);	// -10加上8后仍是负数
   console.log(sub);	// 空串
   ```


#### 3.1.3 substring() 提取子字符串

 `substring() `方法提取某个字符串的一部分，并返回一个**新**的字符串，且不会改动原字符串。substring() 方法接收两个参数，第一个参数表示子字符串的开始位置，第二个参数表示子字符串的结束位置。**含头不含尾**。

```javascript
let s = "12345678";
let sub = s.substring(1, 3);
console.log(sub);   // '23'
```

1. 如果第二个参数**缺失**（不是0），则提取到字符串**末尾**。

2. 当参数是**负数**时，substring() 方法把所有负参数值都转为0。

   ```javascript
   let s = "12345678";
   let sub = s.substring(-1, 6);
   console.log(sub);	// 123456
   ```

   ```javascript
   let s = "12345678";
   let sub = s.substring(1, -6);
   console.log(sub);	// 1
   ```

3. 如果第二个参数小于第一个参数，则两参数就像调换一样。总是返回较小参数位置到较大参数位置的子串。

#### 3.1.4 substr() 提取子字符串 （废弃）

 `substr() `方法提取某个字符串的一部分，并返回一个**新**的字符串，且不会改动原字符串。substr() 方法接收两个参数，第一个参数表示子字符串的开始位置，第二个参数表示返回的字符串中字符的数量。**含头含尾**。

```javascript
let s = "12345678";
let sub = s.substr(1, 4);	
console.log(sub);		// 2345
```

1. 如果第二个参数**缺失**（不是0），则提取到字符串**末尾**。

2. 当参数是**负数**时，substr() 方法将第一个负参数值当成字符串长度加上该值，将第二个负参数值转换成0。

   ```javascript
   let s = "12345678";
   let sub = s.substr(-4, 2);
   console.log(sub);   // 56
   ```

   ```javascript
   let s = "12345678";
   let sub = s.substr(4, -6);
   console.log(sub); 	// 第二个参数如果为0，相当于空串
   ```

### 3.2 字符串位置方法

#### 3.2.1 indexOf() 查找子串位置

indexOf() 方法从字符串**头**开始查找子字符串，并返回子串的开始位置（如果没有找到，则返回-1）。

```javascript
let s = "aabcabcd";
console.log(s.indexOf("bc"));   	// 2
```

接收可选的第二个参数，表示搜索的开始位置。indexOf() 方法会从这个位置开始向尾搜索。

#### 3.2.2 lastIndexOf() 查找子串位置

lastIndexOf() 方法从字符串**尾**开始查找子字符串，并返回子串的开始位置（如果没有找到，则返回-1）。

```javascript
let s = "aabcabcd";
console.log(s.lastIndexOf("bc"));   // 5
```

接收可选的第二个参数，表示搜索的开始位置。lastIndexOf() 方法会从这个位置开始向头搜索。

#### 3.2.3 找所有子串的位置

使用第二个参数并循环调用 indexOf() 或 lastIndexOf()，可以在字符串中找到所有目标子字符串。

```javascript
let s = "aabcabcdbc";
let sub = "bc";
let posArr = [];
let pos = s.indexOf(sub);

while (pos !== -1) {
  posArr.push(pos);
  pos = s.indexOf(sub, pos + 1);
}

console.log(posArr);	// [ 2, 5, 8 ]
```

```javascript
let s = "aabcabcdbc";
let sub = "bc";
let posArr = [];
let pos = s.lastIndexOf(sub);

while (pos !== -1) {
  posArr.push(pos);
  pos = s.lastIndexOf(sub, pos - 1);
}

console.log(posArr);	// [ 8, 5, 2 ]
```

### 3.3 字符串包含方法

#### 3.3.1 startsWith()

`startsWith()` 方法用来判断当前字符串是否以 另外一个给定的子字符串**开头**，并根据判断结果返回 `true` 或 `false`。`startsWith()`方法接收可选的第二个参数，表示开始搜索的位置。

```javascript
let s = 'afoobar'
console.log(s.startsWith('foo'));		// false
console.log(s.startsWith('foo',1));		// true
```

#### 3.3.2 endsWith()

`endsWith()`方法用来判断当前字符串是否是以另外一个给定的子字符串**结尾**的，根据判断结果返回 `true` 或 `false`。`endsWith()`方法接收可选的第二个参数，表示应该当作字符串结束的位置，如果不提供这个参数，默认是字符串的长度。

```javascript
let s = 'foobara'
console.log(s.endsWith('bar'));		// false
console.log(s.endsWith('bar',6));	// true
```

#### 3.3.3 includes()

`includes()` 方法用于判断一个字符串是否包含在另一个字符串中，根据情况返回 `true `或` false`。`includes()`方法接收可选的第二个参数，表示开始搜索的位置。

```javascript
let s = 'foobarfoo'
console.log(s.includes('bar'));		// true
```

### 3.4 trim() 删除前后空格

`trim()` 方法返回一个从两头去掉空白字符的**新**字符串，并不影响原字符串本身。

```javascript
let s1 = "    foobar ";
let s2 = s1.trim();
console.log(s1);    // 不改变原字符串
console.log(s2);    // foobar
```

1. trimEnd() ，别名 trimRight() 移除字符串末尾空格。
2. trimStart() ，别名 trimLeft() 移除字符串开头空格。

### 3.5 repeat() 字符串复制

repeat() 构造并返回一个**新字符串**，接收一个参数表示将字符串重复多少次，然后返回拼接后的结果。

```javascript
let s = 'foo'
console.log(s.repeat(5));   // foofoofoofoofoo
```

### 3.6 padStart() 和 padEnd() 字符串填充

padStart() 和 padEnd() 方法用另一个字符串**填充**当前字符串(如果需要的话，会重复多次)，以便产生的字符串达到给定的长度。padStart() 从左侧开始填充，padEnd() 从右侧开始填充。这两个方法第一个参数是长度，第二个参数是可选的填充字符串，默认空格。返回**新字符串**。

```javascript
let s = "foo";
let s1 = s.padStart(10, "bar");
console.log(s1);                // barbarbfoo
let s2 = s.padEnd(10, "bar");
console.log(s2);                // foobarbarb
```

### 3.7 字符串迭代和解构

字符串原型上暴露了一个`@@iterator`方法，返回一个新的 Iterator 对象，它遍历字符串的代码点，返回每一个代码点的字符串值。

```javascript
let string = "abcde";
let strIter = string[Symbol.iterator]();
console.log(strIter.next()); // { value: 'a', done: false }
console.log(strIter.next()); // { value: 'b', done: false }
console.log(strIter.next()); // { value: 'c', done: false }
console.log(strIter.next()); // { value: 'd', done: false }
console.log(strIter.next()); // { value: 'e', done: false }
console.log(strIter.next()); // { value: undefined, done: true }
```

使用 for-of 循环，通过迭代器按序访问每个字符。

```javascript
let string = "abcde";
for (const s of string) {
  console.log(s);		// a b c d e
}
```

有了这个迭代器，可以通过**解构操作符**来解构。比如：把字符串分割为字符数组。

```javascript
let string = "abcde";
console.log([...string]);   // [ 'a', 'b', 'c', 'd', 'e' ]
```

### 3.8 toLowerCase()、toUpperCase 字符串大小写转换

 `toLowerCase()` 会将调用该方法的字符串值转为**小写形式**，并返回**新**字符串。

`toUpperCase()`方法将调用该方法的字符串转为**大写形式**，并返回**新**字符串。 

`toLocaleLowerCase()`方法根据任何指定区域语言环境设置的大小写映射，返回调用字符串被转换为小写的格式。

`toLocaleUpperCase()`方法根据任何指定区域语言环境设置的大小写映射，返回调用字符串被转换为大写的格式。

注意：

1. 多数地区下，地区特定方法和通用方法相同，少数地区需要用地区特定方法。
2. 如果不知道代码涉及什么语言，最好使用地区特定方法。

### 3.9 字符串模式匹配

#### 3.9.1 match() 返回匹配到的结果数组

match() 检索返回一个字符串匹配正则表达式的结果。接收一个参数，可以是一个正则表达式，也可以是一个RegExp 对象。返回一个**数组**。

```javascript
const paragraph = 'The quick brown fox jumps over the lazy dog. It barked.';
const regex = /[A-Z]/g;
const found = paragraph.match(regex);
console.log(found);     // [ 'T', 'I' ]
```

#### 3.9.2 search() 返回一个匹配到的位置索引

search() 方法执行正则表达式和 String 对象之间的一个搜索匹配。接收一个参数，可以是一个正则表达式，也可以是一个 RegExp 对象。返回第一个匹配位置的**索引**，如果没有找到则返回-1。

```javascript
const paragraph = "the Quick brown fox jumps over the lazy dog. It barked.";
const regex = /[A-Z]/g;
const found = paragraph.search(regex);
console.log(found);     // 4
```

#### 3.9.3 replace()、replaceAll() 字符串替换

**`replace()`** 方法返回一个由替换值（`replacement`）替换部分或所有的模式（`pattern`）匹配项后的**新**字符串。模式可以是一个字符串或者一个[正则表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp)，替换值可以是一个字符串或者一个每次匹配都要调用的回调函数。**如果`pattern`是字符串，则仅替换第一个匹配项。**

```javascript
const p = 'The quick brown fox jumps over the lazy dog. If the dog reacted, was it really lazy?';

console.log(p.replace('dog', 'monkey'));
// expected output: "The quick brown fox jumps over the lazy monkey. If the dog reacted, was it really lazy?"


const regex = /dog/g;
console.log(p.replace(regex, 'ferret'));
// expected output: "The quick brown fox jumps over the lazy ferret. If the ferret reacted, was it really lazy?"

```

**`replaceAll()`** 方法返回一个**新**字符串，新字符串所有满足 `pattern` 的部分都已被`replacement` 替换。`pattern`可以是一个字符串或一个 [`RegExp`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp)， `replacement`可以是一个字符串或一个在每次匹配被调用的函数。原始字符串保持不变。

```javascript
const p = 'The quick brown fox jumps over the lazy dog. If the dog reacted, was it really lazy?';

console.log(p.replaceAll('dog', 'monkey'));
// expected output: "The quick brown fox jumps over the lazy monkey. If the monkey reacted, was it really lazy?"


// global flag required when calling replaceAll with regex
const regex = /Dog/ig;
console.log(p.replaceAll(regex, 'ferret'));
// expected output: "The quick brown fox jumps over the lazy ferret. If the ferret reacted, was it really lazy?"

```

#### 3.9.4 split() 字符串分割，返回数组

`split() `方法使用指定的分隔符字符串将一个[`String`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String)对象分割成子字符串数组，以一个指定的分割字串来决定每个拆分的位置。 

```javascript
const str = "The quick brown fox";

const words = str.split(" ");
console.log(words);             // [ 'The', 'quick', 'brown', 'fox' ]

const chars = str.split("");
console.log(chars[8]);          // k

const strCopy = str.split();
console.log(strCopy);           // [ 'The quick brown fox' ]
```

### 3.10 localeCompare() 

这个方法比较两个字符串：

1. 按照字母表排序，如果字符串应该排在字符串参数**前面**，则返回**负值**。（通常是 -1，具体看实现）

   ```javascript
   const s1 = 'abc'
   const s2 = 'bcd'
   console.log(s1.localeCompare(s2));  // -1
   ```

2. 字符串和字符串参数**相等**，返回 0。

   ```javascript
   const s1 = 'abcd'
   const s2 = 'abcd'
   console.log(s1.localeCompare(s2));  // 0
   ```

3. 按照字母表排序，如果字符串应该排在字符串参数**后面**，则返回**正值**。（通常是 1，具体看实现）

   ```javascript
   const s1 = 'bcd'
   const s2 = 'abc'
   console.log(s1.localeCompare(s2));  // 1
   ```

