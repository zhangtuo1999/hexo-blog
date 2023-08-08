---
title: 【JavaScript红宝书】8.基本引用类型：Date、RegExp
tags:
  - 前端
  - JavaScript
keywords: JavaScript
description: 《JavaScript高级程序设计（第4版）》第5章：基本引用类型。主要内容：Date、RegExp
excerpt: 《JavaScript高级程序设计（第4版）》第5章：基本引用类型。主要内容：Date、RegExp
abbrlink: ba185f0c
date: 2022-05-02 20:41:53
index_img: ../images/封面图/红宝书.png
---

# 基本引用类型

对象被认为是某个特定引用类型的实例。新对象通过使用 new 操作符后跟上一个构造函数（constructor）来创建。比如：

```javascript
let date = new Date()
```

这行代码创建了引用类型 Date 的一个新实例，并将其保存到变量 date 中。`Date()` 在这里就是构造函数，它负责创建一个只有默认属性和方法的简单对象。

# 1 Date

创建一个 JavaScript `Date` 实例，该实例呈现时间中的某个时刻。`Date` 对象则基于 [Unix Time Stamp](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap04.html#tag_04_16)，即自1970年1月1日（UTC）起经过的毫秒数。


## [语法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date#语法)

```
new Date();
new Date(value);
new Date(dateString);
new Date(year, monthIndex [, day [, hours [, minutes [, seconds [, milliseconds]]]]]);
```

**备注：**创建一个新`Date`对象的唯一方法是通过[`new`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new) 操作符，例如：`let now = new Date();`
若将它作为常规函数调用（即不加 [`new`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new) 操作符），将返回一个字符串，而非 `Date` 对象。 

### [参数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date#参数)

`Date()`构造函数有四种基本形式

#### 没有参数

如果没有提供参数，那么新创建的Date对象表示实例化时刻的日期和时间。

#### Unix时间戳

- `value`

  一个 Unix 时间戳（[Unix Time Stamp](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap04.html#tag_04_16)），它是一个整数值，表示自1970年1月1日00:00:00 UTC（the Unix epoch）以来的毫秒数，忽略了闰秒。请注意大多数 Unix 时间戳功能仅精确到最接近的秒。

- 时间戳字符串 `dateString`

  表示日期的字符串值。该字符串应该能被 [`Date.parse()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/parse) 正确方法识别（即符合 [IETF-compliant RFC 2822 timestamps](https://tools.ietf.org/html/rfc2822#page-14) 或 [version of ISO8601](https://www.ecma-international.org/ecma-262/5.1/#sec-15.9.1.15)）。**备注：**由于浏览器之间的差异与不一致性，强烈不推荐使用`Date`构造函数来解析日期字符串 (或使用与其等价的`Date.parse`)。对 RFC 2822 格式的日期仅有约定俗成的支持。 对 ISO 8601 格式的支持中，仅有日期的串 (例如 "1970-01-01") 会被处理为 UTC 而不是本地时间，与其他格式的串的处理不同。

#### 分别提供日期与时间的每一个成员

当至少提供了年份与月份时，这一形式的 `Date() `返回的 `Date `对象中的每一个成员都来自下列参数。没有提供的成员将使用最小可能值（对日期为`1`，其他为`0`）。

- `year`

  表示年份的整数值。 0到99会被映射至1900年至1999年，其它值代表实际年份。参见 [示例](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date#例子：将两位数年份映射为_1900_-_1999_年)。

- `monthIndex`

  表示月份的整数值，从 0（1月）到 11（12月）。

- date可选

  表示一个月中的第几天的整数值，从1开始。默认值为1。

- `hours` 可选

  表示一天中的小时数的整数值 (24小时制)。默认值为0（午夜）。

- `minutes` 可选

  表示一个完整时间（如 01:10:00）中的分钟部分的整数值。默认值为0。

- `seconds` 可选

  表示一个完整时间（如 01:10:00）中的秒部分的整数值。默认值为0。

- `milliseconds` 可选

  表示一个完整时间的毫秒部分的整数值。默认值为0。

## [使用注释](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date#使用注释)

**备注：**参数`monthIndex` 是从“0”开始计算的，这就意味着一月份为“0”，十二月份为“11”。

**备注：**当Date作为构造函数调用并传入多个参数时，如果数值大于合理范围时（如月份为 13 或者分钟数为 70），相邻的数值会被调整。比如 new Date(2013, 13, 1)等于new Date(2014, 1, 1)，它们都表示日期2014-02-01（注意月份是从0开始的）。其他数值也是类似，new Date(2013, 2, 1, 0, 70)等于new Date(2013, 2, 1, 1, 10)，都表示同一个时间：`2013-03-01T01:10:00`。

**备注：**当Date作为构造函数调用并传入多个参数时，所定义参数代表的是当地时间。如果需要使用世界协调时 UTC，使用 `new Date(Date.UTC(...))` 和相同参数。

## [简介](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date#简介)

- 如果没有输入任何参数，则Date的构造器会依据系统设置的当前时间来创建一个Date对象。
- 如果提供了至少两个参数，其余的参数均会默认设置为 1（如果没有指定 day 参数）或者 0（如果没有指定 day 以外的参数）。
- JavaScript的时间由世界标准时间（UTC）1970年1月1日开始，用毫秒计时，一天由 86,400,000 毫秒组成。`Date` 对象的范围是 -100,000,000 天至 100,000,000 天（等效的毫秒值）。
- `Date` 对象为跨平台提供了统一的行为。时间属性可以在不同的系统中表示相同的时刻，而如果使用了本地时间对象，则反映当地的时间。
- `Date` 对象支持多个处理 UTC 时间的方法，也相应地提供了应对当地时间的方法。UTC，也就是我们所说的格林威治时间，指的是time中的世界时间标准。而当地时间则是指执行JavaScript的客户端电脑所设置的时间。
- 以一个函数的形式来调用 `Date` 对象（即不使用 [`new`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new) 操作符）会返回一个代表当前日期和时间的字符串。

## [属性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date#属性)

- [`Date.prototype` (en-US)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)

  允许为 `Date` 对象添加属性。

- `Date.length`

  `Date.length` 的值是 7。这是该构造函数可接受的参数个数。

## [方法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date#方法)

- [`Date.now()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/now)

  返回自 1970-1-1 00:00:00  UTC（世界标准时间）至今所经过的毫秒数。

- [`Date.parse()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/parse)

  解析一个表示日期的字符串，并返回从 1970-1-1 00:00:00 所经过的毫秒数。**备注：** 由于浏览器差异和不一致，强烈建议不要使用`Date.parse`解析字符串。

- [`Date.UTC()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/UTC)

  接受和构造函数最长形式的参数相同的参数（从2到7），并返回从 1970-01-01 00:00:00 UTC 开始所经过的毫秒数。

## [JavaScript `Date` 实例](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date#javascript_date_实例)

所有的 `Date` 实例都继承自 [`Date.prototype` (en-US)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)。修改 `Date `构造函数的原型对象会影响到所有的 `Date` 实例。

### [实例属性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date#实例属性)

- `Date.prototype.constructor`

  返回创建了实例的构造函数，默认是 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 构造函数。

### [实例方法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date#实例方法)

- [`Date.prototype.getDate()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getDate)

  根据本地时间，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象为一个月中的哪一日（`1`-`31`）。

- [`Date.prototype.getDay()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getDay)

  根据本地时间，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象是在一周中的第几天（`0`-`6`），0 表示星期天。

- [`Date.prototype.getFullYear()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getFullYear)

  根据本地时间，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的完整年份（四位数年份）。

- [`Date.prototype.getHours()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getHours)

  根据本地时间，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的小时（`0`–`23`）。

- [`Date.prototype.getMilliseconds()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getMilliseconds)

  根据本地时间，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的毫秒数（`0`–`999`）。

- [`Date.prototype.getMinutes()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getMinutes)

  根据本地时间，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的分钟数（`0`–`59`）。

- [`Date.prototype.getMonth()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getMonth)

  根据本地时间，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的月份（`0`–`11`），0 表示一年中的第一月。

- [`Date.prototype.getSeconds()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getSeconds)

  根据本地时间，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的秒数（`0`–`59`）。

- [`Date.prototype.getTime()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getTime)

  返回一个数值，表示从 1970 年 1 月 1 日 0 时 0 分 0 秒（UTC，即协调世界时）距离该 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象所代表时间的毫秒数。（更早的时间会用负数表示）

- [`Date.prototype.getTimezoneOffset()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getTimezoneOffset)

  返回协调世界时（UTC）相对于当前时区的时间差值，单位为分钟。

- [`Date.prototype.getUTCDate()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCDate)

  以协调世界时为标准，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象为一个月中的哪一日（`1`-`31`）。

- [`Date.prototype.getUTCDay()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCDay)

  以协调世界时为标准，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象是在一周中的第几天（`0`-`6`），0 表示星期天。

- [`Date.prototype.getUTCFullYear()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCFullYear)

  以协调世界时为标准，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的完整年份（四位数年份）。

- [`Date.prototype.getUTCHours()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCHours)

  以协调世界时为标准，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的小时（`0`–`23`）。

- [`Date.prototype.getUTCMilliseconds()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCMilliseconds)

  以协调世界时为标准，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的毫秒数（`0`–`999`）。

- [`Date.prototype.getUTCMinutes()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCMinutes)

  以协调世界时为标准，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的分钟数（`0`–`59`）。

- [`Date.prototype.getUTCMonth()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCMonth)

  以协调世界时为标准，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的月份（`0`–`11`），0 表示一年中的第一月。

- [`Date.prototype.getUTCSeconds()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCSeconds)

  以协调世界时为标准，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的秒数（`0`–`59`）。

- [`Date.prototype.getYear()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getYear)

  根据本地时间，返回一个指定的 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的相对年份（相对1900年，通常是 2 到 3 位数字）。请改用 [`getFullYear`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getFullYear) 。

- [`Date.prototype.setDate()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setDate)

  根据本地时间，设置一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象在所属月份中的天数。

- [`Date.prototype.setFullYear()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setFullYear)

  根据本地时间，设置一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的完整年份（四位数年份）。

- [`Date.prototype.setHours()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setHours)

  根据本地时间，设置一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的小时数。

- [`Date.prototype.setMilliseconds()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setMilliseconds)

  根据本地时间，设置一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的豪秒数。

- [`Date.prototype.setMinutes()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setMinutes)

  根据本地时间，设置一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的分钟数。

- [`Date.prototype.setMonth()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setMonth)

  根据本地时间，设置一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的月份。

- [`Date.prototype.setSeconds()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setSeconds)

  根据本地时间，设置一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的秒数。

- [`Date.prototype.setTime()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setTime)

  用一个从1970-1-1 00:00:00 UTC计时的毫秒数来为一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象设置时间。用负数来设置更早的时间。

- [`Date.prototype.setUTCDate()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setUTCDate)

  以协调世界时为标准，设置一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象在所属月份中的天数。

- [`Date.prototype.setUTCFullYear()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setUTCFullYear)

  以协调世界时为标准，设置一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的完整年份（四位数年份）。

- [`Date.prototype.setUTCHours()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setUTCHours)

  以协调世界时为标准，设置一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的小时数。

- [`Date.prototype.setUTCMilliseconds()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setUTCMilliseconds)

  以协调世界时为标准，设置一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的豪秒数。

- [`Date.prototype.setUTCMinutes()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setUTCMinutes)

  以协调世界时为标准，设置一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的分钟数。

- [`Date.prototype.setUTCMonth()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setUTCMonth)

  以协调世界时为标准，设置一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的月份。

- [`Date.prototype.setUTCSeconds()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setUTCSeconds)

  以协调世界时为标准，设置一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的秒数。

- [`Date.prototype.setYear()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setYear)

  根据本地时间，设置一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的相对年份（相对1900年，通常是 2 到 3 位数字）。请改用 [`setFullYear`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setFullYear) 。

- [`Date.prototype.toDateString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/toDateString)

  以美式英语和人类易读的表述形式返回一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象日期部分的字符串。

- [`Date.prototype.toISOString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/toISOString)

  将指定 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象转换成 ISO 格式表述的字符串并返回。

- [`Date.prototype.toJSON()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/toJSON)

  返回指定 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象调用 [`toISOString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/toISOString) 方法的返回值。在 [`JSON.stringify()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 中使用。

- [`Date.prototype.toGMTString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/toGMTString)

  基于 GMT (UTC) 时区来返回一个表述指定 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的字符串形式。请改用 [`toUTCString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/toUTCString)。

- [`Date.prototype.toLocaleDateString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/toLocaleDateString)

  返回一个表述指定 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的日期部分字符串。该字符串格式因不同语言而不同。

- [`Date.prototype.toLocaleString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/toLocaleString)

  返回一个表述指定 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的字符串。该字符串格式因不同语言而不同。

- [`Date.prototype.toLocaleTimeString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/toLocaleTimeString)

  返回一个表述指定 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象时间部分的的字符串。该字符串格式因不同语言而不同。

- [`Date.prototype.toString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/toString)

  返回一个字符串，表示该 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象。覆盖了 [`Object.prototype.toString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/toString) 方法。

- [`Date.prototype.toTimeString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/toTimeString)

  以人类易读形式返回一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象时间部分的字符串，该字符串以美式英语格式化。

- [`Date.prototype.toUTCString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/toUTCString)

  使用UTC时区，把一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象转换为一个字符串。

- [`Date.prototype.valueOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/valueOf)

  返回一个 [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的原始值。覆盖了 [`Object.prototype.valueOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/valueOf) 方法。

## [例子](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date#例子)

### [例子：创建一个日期对象的几种方法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date#>例子：创建一个日期对象的几种方法)

下例展示了用来创建一个日期对象的多种方法。

**备注：** 由于浏览器差异和不一致性，强烈建议不要使用`Date`构造函数（和`Date.parse`，它们是等效的）解析日期字符串。

```
var today = new Date();
var birthday = new Date('December 17, 1995 03:24:00');
var birthday = new Date('1995-12-17T03:24:00');
var birthday = new Date(1995, 11, 17);
var birthday = new Date(1995, 11, 17, 3, 24, 0);
```

Copy to Clipboard

### [例子：将两位数年份映射为 1900 - 1999 年](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date#例子：将两位数年份映射为_1900_-_1999_年)

为了创建和获取 0 到 99 之间的年份，应使用 [`Date.prototype.setFullYear()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/setFullYear) 和 [`Date.prototype.getFullYear()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/getFullYear) 方法。

```
var date = new Date(98, 1); // Sun Feb 01 1998 00:00:00 GMT+0000 (GMT)

// 已弃用的方法, 同样将 98 映射为 1998
date.setYear(98);           // Sun Feb 01 1998 00:00:00 GMT+0000 (GMT)

date.setFullYear(98);       // Sat Feb 01 0098 00:00:00 GMT+0000 (BST)
```

Copy to Clipboard

### [例子：计算经过的时间](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date#例子：计算经过的时间)

下例展示了如何以毫秒精度计算两个日期对象的时间差：

由于不同日期、月份、年份长度的不同（日期长度不同来自夏令时的切换），使用大于秒、分钟、小时的单位表示经过的时间会遇到很多问题，在使用前需要经过详尽的调研。

```
// 使用 Date 对象
var start = Date.now();

// 调用一个消耗一定时间的方法：
doSomethingForALongTime();
var end = Date.now();
var elapsed = end - start; // 以毫秒计的运行时长
```

Copy to Clipboard

```
// 使用内建的创建方法
var start = new Date();

// 调用一个消耗一定时间的方法：
doSomethingForALongTime();
var end = new Date();
var elapsed = end.getTime() - start.getTime(); // 运行时间的毫秒值
```

Copy to Clipboard

```
// to test a function and get back its return
function printElapsedTime (fTest) {
    var nStartTime = Date.now(),
        vReturn = fTest(),
        nEndTime = Date.now();
    alert("Elapsed time: " + String(nEndTime - nStartTime) + " milliseconds");
    return vReturn;
}
yourFunctionReturn = printElapsedTime(yourFunction);
```

Copy to Clipboard

**备注：**在支持 [`Web Performance API`](https://developer.mozilla.org/zh-CN/docs/Web/API/performance_property) 的高精细度（high-resolution）时间功能的浏览器中，[`Performance.now()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Performance/now) 提供的所经过的时间比 [`Date.now()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/now) 更加可靠、精确。

### [获取自 Unix 起始时间以来经过的秒数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date#获取自_unix_起始时间以来经过的秒数)

```
var seconds = Math.floor(Date.now() / 1000);
```

Copy to Clipboard

注意此处需要返回一个整数 （仅做除法得到的不是整数），并且需要返回实际已经经过的秒数（所以这里使用了[`Math.floor()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Math/floor)而不是[`Math.round()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Math/round)).

参考：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date



# 2 RegExp(正则表达式)

**`RegExp`** 对象用于将文本与一个模式匹配。

有关正则表达式的介绍，请阅读 [JavaScript指南](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/)中的[正则表达式章节](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions)。

## [描述](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#描述)

### [字面量和构造函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#字面量和构造函数)

有两种方法可以创建一个 `RegExp` 对象：一种是字面量，另一种是构造函数。

- 字面量

  由斜杠(/)包围而不是引号包围。

- 构造函数的字符串参数

  由引号而不是斜杠包围。

以下三种表达式都会创建相同的正则表达式：

```
/ab+c/i; //字面量形式
new RegExp('ab+c', 'i'); // 首个参数为字符串模式的构造函数
new RegExp(/ab+c/, 'i'); // 首个参数为常规字面量的构造函数
```

Copy to Clipboard

当表达式被赋值时，字面量形式提供正则表达式的编译（compilation）状态，当正则表达式保持为常量时使用字面量。例如当你在循环中使用字面量构造一个正则表达式时，正则表达式不会在每一次迭代中都被重新编译（recompiled）。

而正则表达式对象的构造函数，如 `new RegExp('ab+c')` 提供了正则表达式运行时编译（runtime compilation）。如果你知道正则表达式模式将会改变，或者你事先不知道什么模式，而是从另一个来源获取，如用户输入，这些情况都可以使用构造函数。

### [构造函数中的标志参数(flags)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#构造函数中的标志参数flags)

从 ECMAScript 6 开始，当第一个参数为正则表达式而第二个标志参数存在时，`new RegExp(/ab+c/, 'i')` 不再抛出 [`TypeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError) （`"从另一个RegExp构造一个RegExp时无法提供标志"`）的异常，取而代之，将使用这些参数创建一个新的正则表达式。

当使用构造函数创造正则对象时，需要常规的字符转义规则（在前面加反斜杠 `\`）。

比如，以下是等价的：

```
var re = new RegExp("\\w+");
var re = /\w+/;
```

Copy to Clipboard

### [Perl-like RegExp 属性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#perl-like_regexp_属性)

请注意，[`RegExp`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp)属性有长名称和短名称（类似Perl）。两个名称总是引用同一个值。（Perl是JavaScript为其正则表达式建模的编程语言）。另请参见[不推荐使用的RegExp属性。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Deprecated_and_obsolete_features#regexp_properties)

## [构造函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#构造函数)

- [`RegExp()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/RegExp)

  创建一个新的 `RegExp` 对象。

## [静态属性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#静态属性)

- [`get RegExp[@@species\]`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/@@species)

  该构造函数用于创建派生对象。

- [`RegExp.lastIndex`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/lastIndex)

  该索引表示从哪里开始下一个匹配

## [实例属性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#实例属性)

- [`RegExp.prototype.flags`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/flags)

  含有 `RegExp` 对象 flags 的字符串。

- [`RegExp.prototype.dotAll`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/dotAll)

  `.` 是否要匹配新行（newlines）。

- [`RegExp.prototype.global`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/global)

  针对字符串中所有可能的匹配项测试正则表达式，还是仅针对第一个匹配项。

- [`RegExp.prototype.ignoreCase`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/ignoreCase)

  匹配文本的时候是否忽略大小写。

- [`RegExp.prototype.multiline`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/multiline)

  是否进行多行搜索。

- [`RegExp.prototype.source`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/source)

  正则表达式的文本。

- [`RegExp.prototype.sticky`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/sticky)

  搜索是否是 sticky。

- [`RegExp.prototype.unicode`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/unicode)

  Unicode 功能是否开启。

## [实例方法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#实例方法)

- [`RegExp.prototype.compile()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/compile)

  运行脚本的期间（重新）编译正则表达式。

- [`RegExp.prototype.exec()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec)

  在该字符串中执行匹配项的搜索。

- [`RegExp.prototype.test()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/test)

  该正则在字符串里是否有匹配。

- [`RegExp.prototype[@@match]()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/@@match)

  对给定字符串执行匹配并返回匹配结果。

- [`RegExp.prototype[@@matchAll]()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/@@matchAll)

  对给定字符串执行匹配，返回所有匹配结果。

- [`RegExp.prototype[@@replace]()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/@@replace)

  给定新的子串，替换所有匹配结果。

- [`RegExp.prototype[@@search]()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/@@search)

  在给定字符串中搜索匹配项，并返回在字符串中找到字符索引。

- [`RegExp.prototype[@@split]()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/@@split)

  通过将给定字符串拆分为子字符串，并返回字符串形成的数组。

- [`RegExp.prototype.toString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/toString)

  返回表示指定对象的字符串。重写[`Object.prototype.toString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/toString)方法。

## [示例](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#syntax)

### [使用正则改变数据结构](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#using_a_regular_expression_to_change_data_format)

下例使用 [`String`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String) 的 [`replace()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace) 方法去匹配姓名 *first last* 输出新的格式 *last*, *first*。

在替换的文本中，脚本中使用 `$1` 和 `$2` 指明括号里先前的匹配.

```
let re = /(\w+)\s(\w+)/;
let str = "John Smith";
let newstr = str.replace(re, "$2, $1");
console.log(newstr);
```

Copy to Clipboard

这将显示 "Smith, John".

### [使用正则来划分带有多种行结束符和换行符的文本](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#使用正则来划分带有多种行结束符和换行符的文本)

对于不同的平台（Unix，Windows等等），其默认的行结束符是不一样的. 而下面的划分方式适用于所有平台。

```
let text = 'Some text\nAnd some more\r\nAnd yet\rThis is the end'
let lines = text.split(/\r\n|\r|\n/)
console.log(lines) // logs [ 'Some text', 'And some more', 'And yet', 'This is the end' ]
```

注意：在正则表达式中，以竖线分割的子模式的顺序会影响匹配结果。

### [在多行文本中使用正则表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#在多行文本中使用正则表达式)

```
let s = "Please yes\nmake my day!";

s.match(/yes.*day/);
// Returns null

s.match(/yes[^]*day/);
// Returns 'yes\nmake my day'
```

Copy to Clipboard

### [使用带有 sticky 标志的正则表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#using_a_regular_expression_with_the_sticky_flag)

带有[`sticky`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/sticky)标志的正则表达式将会从源字符串的[`RegExp.prototype.lastIndex`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/lastIndex)位置开始匹配，也就是进行“粘性匹配”。

```
let str = '#foo#'
let regex = /foo/y

regex.lastIndex = 1
regex.test(str)      // true
regex.lastIndex = 5
regex.test(str)      // false (lastIndex is taken into account with sticky flag)
regex.lastIndex      // 0 (reset after match failure)
```

Copy to Clipboard

### [ sticky 标志和 global 标志的不同点](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#sticky_标志和_global_标志的不同点)

如果正则表达式有粘性 `y` 标志，下一次匹配一定在 `lastIndex` 位置开始；如果正则表达式有全局 `g` 标志，下一次匹配可能在 `lastIndex` 位置开始，也可能在这个位置的后面开始。

```
re = /\d/y;
while (r = re.exec("123 456")) console.log(r, "AND re.lastIndex", re.lastIndex);

// [ '1', index: 0, input: '123 456', groups: undefined ] AND re.lastIndex 1
// [ '2', index: 1, input: '123 456', groups: undefined ] AND re.lastIndex 2
// [ '3', index: 2, input: '123 456', groups: undefined ] AND re.lastIndex 3
//   ... and no more match.
```

Copy to Clipboard

如果使用带有全局标志`g`的正则表达式`re`，就会捕获字符串中的所有6个数字，而非3个

### [使用正则表达式和 Unicode 字符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#browser_compatibility)

正如上面表格提到的，`\w` 或 `\W` 只会匹配基本的 ASCII 字符；如 `a` 到 `z`、 `A` 到 `Z`、 `0` 到 `9` 及 `_`。

为了匹配其他语言中的字符，如西里尔（Cyrillic）或 希伯来语（Hebrew），要使用 `\uhhhh`，`hhhh` 表示以十六进制表示的字符的 Unicode 值。

下例展示了怎样从一个单词中分离出 Unicode 字符。

```
let text = "Образец text на русском языке";
let regex = /[\u0400-\u04FF]+/g;

let match = regex.exec(text);
console.log(match[1]);  // prints "Образец"
console.log(regex.lastIndex);  // prints "7"

let match2 = regex.exec(text);
console.log(match2[1]);  // prints "на" [did not print "text"]
console.log(regex.lastIndex);  // prints "15"

// and so on
```

Copy to Clipboard

[Unicode属性转义特性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions/Unicode_Property_Escapes)引入了一种解决方案，它允许使用像\p{scx=Cyrl}这样简单的语句。这里有一个外部资源，用来获取 Unicode 中的不同区块范围：[Regexp-unicode-block](http://kourge.net/projects/regexp-unicode-block)

### [从 URL 中提取子域名](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp#从_url_中提取子域名)

```
var url = "http://xxx.domain.com";
console.log(/[^.]+/.exec(url)[0].substr(7)); // logs "xxx"
```

Copy to Clipboard

**备注：**使用浏览器内建的[URL API](https://developer.mozilla.org/en-US/docs/Web/API/URL_API)而非正则表达式来解析URL是更好的做法

参考：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp

