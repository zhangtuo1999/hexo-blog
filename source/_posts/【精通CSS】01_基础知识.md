---
title: 【精通CSS】01.基础知识
tags:
  - 前端
  - CSS
excerpt: 《精通CSS：高级Web标准解决方案（第3版）》第一章：基础知识。主要内容：组织代码、ID Class 属性、HTML5 结构化元素、div 标签、span 标签。
typora-root-url: ..
abbrlink: 364c3bce
date: 2023-05-25 15:43:23
index_img: ../images/封面图/精通CSS.jpg
---

# 1 组织代码

* 可维护性是所有优秀代码**最重要**的特点。
* CSS 的初衷是把跟 HTML 混在一起的表现性标记提取出来，使其自成体系，达到**结构与表现分离**的目的。
* 判断一个人是不是 CSS 大师，很大程度上要看他是否能游刃有余地处理**向后兼容**的代码与**未来友好**的代码。

## 1.1 渐进增强

**渐进增强**策略：首先为最小公分母准备可用的内容，然后在为支持新特性的浏览器提供更多的交互优化。

CSS 的渐进增强策略反映在如何对待新属性上，任何浏览器无法识别的属性和值都会导致浏览器丢弃相应的声明。因此只要提供合理的后备声明，使用新属性就不会带来不良后果。

比如，使用 rgba 函数方式表示颜色值：

```css
.overlay{
    background-color: #000;
    background-color: rgba(0, 0, 0, 0.2);
}
```

如果浏览器支持 rgba 函数，第二条声明会覆盖第一条。因此，即使不是所有浏览器都支持 rgba 函数，仍然可以使用它，只要在这之前声明后备代码。

### 1.1.1 厂商前缀

在标准名称前面加上特殊字符串，以便不同浏览器厂商引入**实验性特性**。

比如，给相应元素添加变换：

```css
.myThing {
    -webkit-transform: translate(0, 10px);
    -moz-transform: translate(0, 10px);
    -ms-transform: translate(0, 10px);
    transform: translate(0, 10px);
}
```

以  `-webkit-`  开头的适用于基于 WebKit 的浏览器：Safari、Chrome、Opera

以 `-moz-` 开头的适用于基于 Mozilla 的浏览器：Firefox

以 `-ms-` 开头的适用于 IE 浏览器

最后不带前缀的标准属性名称适用于所有支持它的浏览器。

### 1.1.2 条件规则

使用 `@supports` 根据浏览器是否支持某个 CSS 特性来提供不同的样式。比如：

```css
@supports(display:grid){
    /* 应用的规则 */
}
```

它会检查括号中的声明，只有浏览器支持该声明的前提下，才会应用块中的规则。

# 2  创建结构化、语义化富 HTML

* **语义**指的是通过使用某个符号想要表达的含义。**语义化标记**指在正确的地方使用正确的元素。

## 2.1 ID 和 class 属性

* 给元素添加类名的时候，只让类名体现出组件的**类型**，而不是体现出**视觉效果**。比如：

  ```html
  <div class="product-list"></div> 			// 推荐
  <div class="large-centered-list"></div>		// 不推荐
  ```

* 一般来说 class 属性来定义**一类**事物，id 属性来标识**特定实例**。

* 一般不建议将 id 属性作为 CSS 的接入点。id 可以在文档中标识元素，但是通常不会用于样式。

## 2.2 结构化元素

HTML5 中新增的结构化元素：

* section
* header
* footer
* nav
* article
* aside
* main

使用这些新元素来可以增强逻辑性，可以帮助机器人更好地理解文档。

## 2.3 div 和 span

* 确保真的不需要使用语义化元素时，才去使用 div。
* 与 div 不同，span 是**文本级元素**，可以用来在文本流中建立结构。

