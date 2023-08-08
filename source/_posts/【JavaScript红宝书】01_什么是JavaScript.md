---
title: 【JavaScript红宝书】1.什么是JavaScript
tags:
  - 前端
  - JavaScript
excerpt: 《JavaScript高级程序设计（第4版）》笔记。原书第1章：什么是JavaScript。主要内容有：JavaScript历史回顾、ECMAScript、DOM、BOM等。
abbrlink: 175876eb
date: 2022-04-24 10:44:58
index_img: ../images/封面图/红宝书.png
---

# 什么是 JavaScript

## 简短的历史回顾

1995年，网景公司的工程师 Brendan Eich 为即将发布的浏览器开发了一个叫做 Mocha 的脚本语言。后来这门脚本语言改名叫做 LiveScript。为了赶上发布时间，网景公司和 Sun 公司合作，共同完成 LiveScript 的开发。当时 Java 语言被媒体热烈炒作，因此网景公司将 LiveScript 改名为 JavaScript。

1996年8月，微软 IE 浏览器带着名为 JScript 的 JavaScript 实现重磅进入 Web 浏览器领域。JavaScript 两个不同版本并存的问题日渐突出，随后走上了标准化的征程。

1997年，JavaScript 1.1 作为提案提交给欧洲计算机制造商协会（Ecma）。第39技术委员会（TC39）承担了 JavaScript 标准化的任务，并在数月时间内打造出了 ECMA-262，也就是 ECMAScript 这个**脚本语言标准**。

此后，各家浏览器都以 ECMAScript 为实现的依据，虽然具体实现各有不同。

## JavaScript 实现

JavaScript 远远不限于 ECMAScript 标准，完整的 JavaScript 实现包括：

* 核心（ECMAScript）
* 文档对象模型（DOM），提供与**网页内容**交互的方法和接口
* 浏览器对象模型（BOM），提供与**浏览器**交互的方法和接口

### ECMAScript

ECMAScript 并不局限于 Web 浏览器。Web 浏览器只是 ECMAScript 可能存在于的一种**宿主环境**。宿主环境提供 ECMAScript 的标准实现和与环境本身交互的扩展。宿主环境除了 Web 浏览器，还有 Node.js 等。

### DOM

文档对象模型（DOM, Document Object Model）是一个 API。DOM 通过创建表示文档的**树**，让开发者随心所欲地通过操作**节点**来控制网页的内容。

### BOM

浏览器对象模型（BOM,Browser Object Model）是一个 API，用于支持访问和操作浏览器的窗口。使用 BOM，开发者可以操控浏览器显示页面之外的地方。BOM 没有相关标准。但是 HTML5 以规范的形式涵盖了尽可能多的 BOM 特性。
