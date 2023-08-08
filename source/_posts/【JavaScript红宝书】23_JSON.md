---
title: 【JavaScript红宝书】23.JSON
tags:
  - 前端
  - JavaScript
keywords: JavaScript
description: 《JavaScript高级程序设计（第4版）》第23章：JSON。主要内容：JSON 对象，序列化和解析。
excerpt: 《JavaScript高级程序设计（第4版）》第23章：JSON。主要内容：JSON 对象，序列化和解析。
abbrlink: a0f7377d
date: 2022-08-14 10:39:55
index_img: ../images/封面图/红宝书.png
---

# JSON

JavaScript 对象简谱（JSON,JavaScript Object Notation）是 JavaScript 的严格子集。理解JSON 最关键的一点是要把它当成一种**数据格式**，而不是编程语言。JSON 属于JavaScript，它们只是拥有相同的语法而已。JSON 也不是只能在JavaScript 中使用，它是一种通用数据格式。很多语言都有解析和序列化 JSON 的内置能力。

## 1 语法

JSON 语法支持表示3 种类型的值。 

* 简单值：字符串、数值、布尔值和 null 可以在 JSON 中出现，就像在 JavaScript 中一样。特殊
  值 undefined 不可以。
* 对象：第一种复杂数据类型，对象表示有序键/值对。每个值可以是简单值，也可以是复杂类型。
* 数组：第二种复杂数据类型，数组表示可以通过数值索引访问的值的有序列表。数组的值可以
  是任意类型，包括简单值、对象，甚至其他数组。

JSON 没有变量、函数或对象实例的概念。JSON 的所有记号都只为表示结构化**数据**。

### 1.1 简单值

最简单的JSON 可以是一个数值。例如，下面这个数值是有效的JSON：

```json
5
```

下面这个字符串也是有效的JSON：

```json
"Hello world!"
```

JavaScript 字符串与JSON 字符串的主要区别是，JSON 字符串必须使用**双引号**（单引号会导致语法错误）。

布尔值和 null 也是有效的 JSON 值：

```json
true
```

```json
null
```

### 1.2 对象

JSON 表示对象的语法是：

```json
{
    "name": "Nicholas",
    "age": 29
}
```

与 JavaScript 对象字面量相比，JSON 主要有两处不同。首先，没有变量声明（JSON 中没有变量）。其次，最后没有分号（不需要，因为不是 JavaScript 语句）。同样，用引号将属性名包围起来才是有效的 JSON。属性的值可以是简单值或复杂数据类型值，后者可以在对象中再嵌入对象，比如：

```json
{
    "name": "Nicholas",
    "age": 29,
    "school": {
        "name": "Merrimack College",
        "location": "North Andover, MA"
    }
}
```

与 JavaScript 不同，JSON 中的**对象属性名**必须始终带**双引号**。

### 1.3 数组

JSON 的第二种复杂数据类型是数组。数组在 JSON 中使用 JavaScript 的数组字面量形式表示。

```json
[25, "hi", true]
```

对象和数组通常会作为JSON 数组的顶级结构（尽管不是必需的），以便创建大型复杂数据结构。

## 2 序列化和反序列化

### 2.1 JSON 对象

JSON 对象有两个方法：stringify() 和 parse()。在简单的情况下，这两个方法分别可以将 JavaScript 序列化为 JSON 字符串，以及将 JSON 解析（反序列化）为原生 JavaScript 值。例如：

```javascript
let book = {
    title: "Professional JavaScript",
    authors: [
        "Nicholas C. Zakas",
        "Matt Frisbie"
    ],
    edition: 4,
    year: 2017
};
let jsonText = JSON.stringify(book);
```

这个例子使用 JSON.stringify() 把一个 JavaScript 对象序列化为一个 JSON 字符串，保存在变量 jsonText 中。默认情况下，JSON.stringify() 会输出**不包含空格或缩进**的 JSON 字符串，因此 jsonText 的值是这样的：

```json
{"title":"Professional JavaScript","authors":["Nicholas C. Zakas","Matt Frisbie"],"edition":4,"year":2017}
```

在序列化 JavaScript 对象时，所有**函数和原型成员**都会有意地在结果中**省略**。此外，值为 **undefined**
的任何属性也会被跳过。最终得到的就是所有实例属性均为有效 JSON 数据类型的表示。

JSON 字符串可以直接传给 JSON.parse()，然后得到相应的 JavaScript 值。比如，可以使用以下代码创建与 book 对象类似的新对象：

```javascript
let bookCopy = JSON.parse(jsonText);
```

注意，book 和 bookCopy 是两个完全不同的对象，没有任何关系。但是它们拥有相同的属性和值。

如果给 JSON.parse() 传入的 JSON 字符串无效，则会导致抛出错误。

### 2.2 JSON.stringify() 序列化

JSON.stringify() 方法除了要序列化的对象，还可以接收两个参数。这两个参数可以用于指定其他序列化 JavaScript 对象的方式。第一个参数是**过滤器**，可以是数组或函数；第二个参数是用于**缩进**结果JSON 字符串的选项。

#### 2.2.1 过滤结果

如果第二个参数是一个**数组**，那么 JSON.stringify() 返回的结果只会包含该数组中列出的对象属性。

```javascript
let book = {
    title: "Professional JavaScript",
    authors: [
        "Nicholas C. Zakas",
        "Matt Frisbie"
    ],
    edition: 4,
    year: 2017
};
let jsonText = JSON.stringify(book, ["title", "edition"]);
```

```json
{"title":"Professional JavaScript","edition":4}
```

如果第二个参数是一个**函数**（称为 replacer）。提供的函数接收两个参数：属性名（key）和属性值（value）。可以根据这个 key 决定要对相应属性执行什么操作。这个 key 始终是字符串，只是在值不属于某个键/值对时会是空字符串。

为了改变对象的序列化，返回的值就是相应 key 应该包含的结果。注意，返回 undefined 会导致属性被忽略。

```javascript
let book = {
  title: "Professional JavaScript",
  authors: ["Nicholas C. Zakas", "Matt Frisbie"],
  edition: 4,
  year: 2017,
};

let jsonText = JSON.stringify(book, (key, value) => {
  switch (key) {
    case "authors":
      return value.join(",");
    case "year":
      return 5000;
    case "edition":
      return undefined;
    default:
      return value;	// 提供默认返回值
  }
});
```

```json
{"title":"Professional JavaScript","authors":"Nicholas C. Zakas,Matt Frisbie","year":5000}
```

最后一定要提供**默认返回值**，以便返回其他属性传入的值。

#### 2.2.2 字符串缩进

JSON.stringify() 方法的第三个参数控制缩进和空格。

在这个参数是**数值**时，表示每一级缩进的空格数。

```javascript
let book = {
    title: "Professional JavaScript",
    authors: [
        "Nicholas C. Zakas",
        "Matt Frisbie"
    ],
    edition: 4,
    year: 2017
};
let jsonText = JSON.stringify(book, null, 4);
```

```json
{
    "title": "Professional JavaScript",
    "authors": [
        "Nicholas C. Zakas",
        "Matt Frisbie"
    ],
    "edition": 4,
    "year": 2017
}
```

注意，除了缩进，JSON.stringify() 方法还为方便阅读插入了换行符。这个行为对于所有有效的缩进参数都会发生。

如果缩进参数是一个**字符串**而非数值，那么 JSON 字符串中就会使用这个字符串而不是空格来缩进。使用字符串，也可以将缩进字符设置为 Tab 或任意字符，如两个连字符：

```javascript
let jsonText = JSON.stringify(book, null, "--" );
```

这样，jsonText 的值会变成如下格式：

```json
{
--"title": "Professional JavaScript",
--"authors": [
----"Nicholas C. Zakas",
----"Matt Frisbie"
--],
--"edition": 4,
--"year": 2017
}
```

使用字符串时有 10 个字符的长度限制。如果字符串长度超过 10，则会在第 10 个字符处截断。

#### 2.2.3 toJSON() 方法

有时候，对象需要在 JSON.stringify() 之上自定义 JSON 序列化。此时，可以在要序列化的对象中添加 toJSON() 方法，序列化时会基于这个方法返回适当的 JSON 表示。事实上，原生 Date 对象就有一个 toJSON() 方法，能够自动将 JavaScript 的 Date 对象转换为 ISO 8601 日期字符串（本质上与在 Date 对象上调用 toISOString() 方法一样）。

下面的对象为自定义序列化而添加了一个 toJSON() 方法：

```javascript
let book = {
    title: "Professional JavaScript",
    authors: [
        "Nicholas C. Zakas",
        "Matt Frisbie"
    ],
    edition: 4,
    year: 2017,
    toJSON: function() {
        return this.title;
    }
};
let jsonText = JSON.stringify(book);
console.log(jsonText);
```

```json
"Professional JavaScript"
```

这里 book 对象中定义的 toJSON() 方法简单地返回了图书的书名（this.title）。与 Date 对象类似，这个对象会被序列化为简单字符串而非对象。toJSON() 方法可以返回任意**序列化值**，都可以起到相应的作用。如果对象被嵌入在另一个对象中，返回 undefined 会导致值变成 null；或者如果是顶级对象，则本身就是 undefined。注意，箭头函数不能用来定义 toJSON() 方法。主要原因是箭头函数的词法作用域是全局作用域，在这种情况下不合适。

toJSON() 方法可以与过滤函数一起使用，因此理解不同序列化流程的顺序非常重要。在把对象传给 JSON.stringify()时会执行如下步骤。

(1) 如果可以获取实际的值，则调用 toJSON() 方法获取实际的值，否则使用默认的序列化。

(2) 如果提供了第二个参数，则应用过滤。传入过滤函数的值就是第 (1) 步返回的值。

(3) 第 (2) 步返回的每个值都会相应地进行序列化。

(4) 如果提供了第三个参数，则相应地进行缩进。

理解这个顺序有助于决定是创建 toJSON() 方法，还是使用过滤函数，抑或是两者都用。

### 2.3 JSON.parse() 反序列化

JSON.parse() 方法也可以接收一个额外的参数，这个函数会针对每个键/值对都调用一次。为区别于传给JSON.stringify() 的起过滤作用的替代函数（replacer），这个函数被称为还原函数（reviver）。

实际上它们的格式完全一样，即还原函数也接收两个参数，属性名（key）和属性值（value），另外也需要返回值。如果还原函数返回 undefined，则结果中就会**删除**相应的键。如果返回了其他任何值，则该值就会成为相应键的值插入到结果中。还原函数经常被用于把日期字符串转换为 Date 对象。例如：

```javascript
let book = {
  title: "Professional JavaScript",
  authors: ["Nicholas C. Zakas", "Matt Frisbie"],
  edition: 4,
  year: 2017,
  releaseDate: new Date(2017, 11, 1),
};
let jsonText = JSON.stringify(book);
console.log(jsonText);
// {"title":"Professional JavaScript","authors":["Nicholas C. Zakas","Matt Frisbie"],"edition":4,"year":2017,"releaseDate":"2017-11-30T16:00:00.000Z"}

let bookCopy = JSON.parse(jsonText, (key, value) =>
  key == "releaseDate" ? new Date(value) : value
);
console.log(bookCopy.releaseDate.getFullYear());  
// 2017
```

以上代码在 book 对象中增加了 releaseDate 属性，是一个 Date 对象。这个对象在被序列化为 JSON 字符串后，又被重新解析为一个对象 bookCopy。这里的还原函数会查找"releaseDate"键，如果找到就会根据它的日期字符串创建新的 Date 对象。得到的 bookCopy.releaseDate 属性又变回了 Date 对象，因此可以调用其 getFullYear() 方法。
