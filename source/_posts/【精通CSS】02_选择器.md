---
title: 【精通CSS】02.选择器
tags:
  - 前端
  - CSS
excerpt: 《精通CSS：高级Web标准解决方案（第3版）》第二章：添加样式。主要内容：选择器
abbrlink: b27b9dc2
date: 2023-05-26 17:57:40
typora-root-url: ..
index_img: ../images/封面图/精通CSS.jpg
---

# 1 CSS 选择器

## 1.1 基本选择器

### 1.1.1 元素选择器

用于选择特定类型的元素。比如段落或标题元素。例：

```html
<p>这是一个段落</p>
```

```css
p{
	color:black;
}
```

元素选择器适合全面应用基础样式。更精准的选择目标应该使用 ID 选择器和类选择器。

### 1.1.2 ID 选择器

通过 HTML 元素的 id 属性来选择元素。由`#`开头，例：

```html
<p id='p1'>段落1</p>
```

```css
#p1{
	color:black;
}
```

### 1.1.3 类选择器

通过 HTML 元素的 class 属性来选择元素。由`.`开头，例：

```html
<p class="my-p">这是一个段落</p>
```

```css
.my-p{
    color:black;
}
```

## 1.2 组合器选择器 

### 1.2.1 后代选择器

后代选择器用于选择某个或者某组元素的后代，写法是在两个**基本选择器**之间加上空格，例：

```html
<div>
    <p>被div包裹的段落</p>
</div>
```

```css
div p{
    background-color: #a6edb2;
}
```

上面的例子中，只有父元素是 div 的 p 元素才会被影响到样式，其它的 p 元素不会发生变化。

后代选择器会选择一个元素的**所有**层级的后代。并且，后代选择器中的**基本选择器**类型和数量不限，例：

```html
<div>
    <p>段落1</p>
    <span><p>段落2</p></span>
</div>
```

```css
div p {
    background-color: blue;
}
```

两个段落都被应用了样式：![image-20220523101342300](/images/CSS.2.选择器/image-20220523101342300.png)

### 1.2.2 子选择器

与后代选择器会选择一个元素的所有后代不同，子选择器只会选择一个元素的**直接**后代。写法是在两个基本选择器之间加上 `>` 。比如：

```html
<div>
    <p>段落1</p>
    <span><p>段落2</p></span>
</div>
```

```css
div > p {
    background-color: skyblue;
}
```

只有直接后代应用了样式，后代的后代没有应用到样式：

![image-20220523101556742](/images/CSS.2.选择器/image-20220523101556742.png)

### 1.2.3 相邻同辈选择器

使用相邻同辈选择器可以选择位于某个元素后面，与其**最相邻的同辈元素**。写法是在两个基本选择器之间使用 `+` 。比如：

```html
<div>
    <span>段落1</span>
    <p>段落2</p>
</div>
```

```css
span + p {
    background-color: skyblue;
}
```

在 span 后最相邻的同辈元素是 p，所以 p 被应用到样式：

![image-20220523102404149](/images/CSS.2.选择器/image-20220523102404149.png)

### 1.2.4 通用同辈选择器

与相邻同辈选择器不同，通用同辈选择器可以选择某个元素后面的**所有同辈元素**。写法是在两个基本选择器之间使用`~`。比如：

```html
<div>
    <span>段落1</span>
    <p>段落2</p>
    <span><p>段落3</p></span>
    <p>段落4</p>
</div>
```

```css
span ~ p {
    background-color: skyblue;
}
```

span 后面的所有同辈元素都会被应用到样式：

![image-20220523102928742](/images/CSS.2.选择器/image-20220523102928742.png)

## 1.3 通用选择器

通用选择器可以匹配任何元素。使用 `*` 表示通用选择器。

比如，删除所有元素的外边距：

```css
*{
	margin: 0;
}
```

也可以和组合器选择器使用。比如，选择 span 元素下面的所有直接后代：

```css
span > * {
	/* ... */
}
```

## 1.4 属性选择器

属性选择器基于元素是否有某个属性或者属性是否有某个值来选择元素。写法是使用`[]`。

### 1.4.1 元素是否有某个属性

根据元素是否有某个属性来应用样式。比如，给带有 title 属性的 attr 元素加上样式：

```html
<span><abbr title="北京化工大学">BUCT</abbr></span>
```

```css
abbr[title]{
    border-bottom: 1px dotted #999;
}
```

效果：

![image-20220523110737253](/images/CSS.2.选择器/image-20220523110737253.png)

### 1.4.2 属性值是否匹配

除了可以根据某个属性来选择元素，还可以根据特定的属性值来应用样式。比如，所有 type 属性值为 submit 的input 元素在悬停时显示一个手状光标：

```css
input[type="submit"] {
    cursor: pointer;
}
```

有时候，我们关心的是属性值是否匹配某个**模式**，而非特定的值。这时候，可以通过给属性选择符中的等号前面加上特殊字符。

1. 匹配以某些字符**开头**的属性值，在等号前面加上 `^`。

   ```css
   a[href^="http:"]
   ```

2. 匹配以某些字符的**结尾**的属性值，在等号前面加上`$`。

   ```css
   a[src$=".jpg"]
   ```

3. 匹配**包含**某些字符的属性值，在等号前面加上`*`。

   ```css
   a[href*="/about/"]
   ```

4. 要匹配以空格分割的字符串中的属性值，在等号前面加上`~`。

   ```css
   a[rel~=next]{
   	color: skyblue;
   }
   ```

   ```html
   <a href='' rel="prev next">链接</a>
   ```

   效果：

   ![image-20220523112813211](/images/CSS.2.选择器/image-20220523112813211.png)

5. 要匹配开头是指定值或者指定值后面连着一个短划线的情况，在等号前面加上`|`。

   ```css
   a[lang|=en]
   ```

   这条规则可以匹配属性值为 en 和 en-us 等。

## 1.5 伪元素选择器

有时候，我们想选择的页面区域不是通过元素来表示的，而我们也不想为此给页面添加额外的标记。此时，可以使用**伪元素选择器**。伪元素使用`::`来表示。

比如，可以使用  `::first-letter` 伪元素来选择一段文本的第一个字符。使用 `::first-line` 来选择文本的第一行。使用 `::before` 和 `::after` 选择内容开头和末尾。

```html
<h2>Title</h2>
<section class="chapter">
    <p>this is a text.this is a text.this is a text.this is a text.this is a text.this is a text.this is a text.this is a text.this is a text.</p>
</section>
```

```css
.chapter p::first-letter{
    float: left;
    font-size: 3em;
}

.chapter p::first-line{
    text-transform: capitalize;
}
```

效果：

![image-20220523145954542](/images/CSS.2.选择器/image-20220523145954542.png)

> **注意**：为了和**伪类**区分开，伪元素应该使用**双冒号**语法。但是处于兼容性考虑，某些浏览器支持单冒号语法。

## 1.6 伪类选择器

有时候，我们想基于文档结构以外的情形来为页面添加样式，比如基于超链接或表单元素的状态。此时可以使用伪类选择器。伪类使用`:`开头，用于选择元素特定的状态或者关系。

### 1.6.1 用于超链接的伪类

最常见的用于超链接的伪类如下：

```css
/* 未访问过的链接为蓝色 */
a:link {
    color: blue;
}

/* 访问过的链接为绿色 */
a:visited {
    color: green;
}

/* 鼠标悬停及获取键盘焦点时为红色 */
a:hover,
a:focus {
    color: red;
}

/* 活动状态时为紫色 */
a:active {
    color: purple;
}
```

以上伪类的**先后次序**很重要。`:link` 和 `:visited` 应该排在前面，然后是与用户交互的那些。这样，当用户鼠标悬停或者获取键盘焦点时，`:hover` 和 `:focus` 会覆盖 `:link` 和 `:hover` 规则。

### 1.6.2 目标伪类和反选伪类

**目标伪类**`:target`，匹配的元素有一个 ID 属性，而且该属性的值出现在当前页面 URL 末尾的 `#` 后面。比如，通过目标伪类选择一条评论：

```css
.comment:target{
	/* .. */
}
```

**反选伪类**`:not()` ，用于排除某些选择器。比如，选择 `comment` 类中不带 `comment-downvoted` 类名的元素：

```css
.comment:not(.comment-downvoted){
	/* .. */
}
```

反选伪类可以配合各种放到括号中的选择器使用，不过伪元素和自身除外。

### 1.6.3 结构化伪类

CSS3 新增了一大批与文档结构有关的新伪类。最常使用的是 `:nth-child` 选择器，可以用来交替地为表格行添加样式。

#### :nth-child

`:nth-child`可以接受 `odd`（奇数）或者 `even`（偶数）作为参数。

```css
tr:nth-child(odd) {
    background-color: skyblue;
}
```

```html
<table>
    <tr>
        <td>1</td>
        <td>text</td>
    </tr>
    <tr>
        <td>2</td>
        <td>text</td>
    </tr>
    <tr>
        <td>3</td>
        <td>text</td>
    </tr>
    <tr>
        <td>4</td>
        <td>text</td>
    </tr>
</table>
```

效果：

![image-20220523160632700](/images/CSS.2.选择器/image-20220523160632700.png)

`:nth-child`接收的参数还可以是数值，表示目标元素的序号位置。

```css
/* 表格的第三行会应用样式 */
tr:nth-child(3) {
    background-color: skyblue;
}
```

`:nth-child`接收的参数还可以是数值表达式。

```css
/* 表格的第4、7、10...行会应用样式*/
tr:nth-child(3n+4) {
    background-color: skyblue;
}
```

#### :nth-last-child

`:nth-last-child` 选择器与`:nth-child`类似，只不过是从最后一个元素倒序计算。

### 1.6.4 表单伪类

还有很多伪类专门用于选择表单元素。比如`:required`、`:optional`、`valid`、`invalid`等等。

为**必填**的表单元素添加高亮效果：

```css
input:required{
    outline: 2px solid #000;
}
```

```html
<label for="name">Name:</label>
<input type="text" name="name" id="name" required>
```

此外还有针对**有效**和**无效**控件的伪类。比如，邮箱输入框中的内容有效和无效时添加效果：

```css
input[type="email"]:valid {
    border-color: green;
}

input[type="email"]:invalid {
    border-color: red;
}
```

此外，还有针对 type 值为 number 的 `:in-range`、`:out-of-range` 伪类，针对 readonly 属性的 `:read-only` 伪类，以及针对没有 readonly 属性的 `:read-write` 伪类。
