---
title: 【JavaScript红宝书】3.语言基础 - 语法、标识符、关键字和保留字
tags:
  - 前端
  - JavaScript
excerpt: 《JavaScript高级程序设计（第4版）》笔记。原书第3章：语言基础。主要内容：语法、标识符、关键字和保留字。
abbrlink: 9b501bb7
date: 2022-04-25 20:55:12
index_img: ../images/封面图/红宝书.png
---

# 语言基础

## 1 语法

### 1.1 区分大小写

ECMAScript 中一切都区分大小写。无论变量、函数名还是操作符，都区分大小写。`typeof`不能作为函数名，因为它是一个**关键字**，但是`Typeof`是有效的函数名。

### 1.2 标识符

**标识符**，就是变量、函数、属性和函数参数的名称。必须符合以下规定：

* 第一个字符必须是一个字母、`_`或`$`
* 其余字符可以是字母、`_`、`$`或数字

> 这里有一个疑问：**属性名是标识符吗**？
>
> **带引号的**属性的名不需要满足上述的规则，而且关键字，保留字，true，fasle等等都可以作为属性名。
>
> ```javascript
> const a = {'123abc': 1}
> const b = {break: 2}
> const c = {true: 3}
> ```
>
> 但是，如果在命名属性时**不带引号**，则需要满足上述的规则。
>
> ```javascript
> const d = {123abc:1} 	// 语法错误
> ```
>
> 

注意：

1. 这里的字符可以是字母也可以是 [Unicode](https://developer.mozilla.org/zh-CN/docs/Glossary/Unicode) 字母字符，比如汉字。

2. 按照惯例，ECMAScript 中的标识符使用**驼峰大小写**，即第一个单词首字母小写，其余单词首字母大写。
3. 关键字、保留字、`true`、`false`和`null`等等不能作为标识符。

### 1.3 注释

```javascript
// 单行注释

/*
	多行注释
	多行注释
*/
```

### 1.4 语句

1. ECMAScript 中语句以分号结尾。**语句末尾的分号不是必须的**。

2. if 之类的控制语句**只在执行多条语句时**要求有代码块。最佳实践是始终在控制语句中使用代码块：

   ```javascript
   if(test){
   	...
   }
   ```

# 2 关键字和保留字

## 2.1 关键字

ECMAScript 描述了一组关键字，它们有特殊用途。比如：控制语句开始和结束或者执行特定操作。

**关键字不能作为标识符和属性名来使用**。

ECMAScript 第6版规定的关键字有：

```
break、do、in、typeof、case、else、instanceof、var、catch、export、new、void、class、extends、return、while、const、finally、super、with、continue、for、switch、yield、debugger、function、this、default、if、throw、delete、import、try
```

## 2.2 保留字

保留字，在语言中没有特定的用途，但是它们是保留给未来做关键字用的。

同样地，**保留字不能作为标识符和属性名来使用**。

ECMAScript 第6版保留字：

1. 始终保留：`enum`
2. 严格模式下保留：`implements`、`package`、`public`、`interface`、`protected`、`static`、`let`、`private`
3. 模块代码中保留：`await`
