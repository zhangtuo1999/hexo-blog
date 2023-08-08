---
title: 【JavaScript】var、let和const的区别
tags: JavaScript
keywords: JavaScript
description: JavaScript中var、let和const的区别
excerpt: JavaScript中var、let和const的区别
abbrlink: ec0b5c8e
date: 2022-11-05 21:30:49
---

### 作用域

> 作用域指的是该变量的执行上下文，超过这个范围便无法访问到该变量。

* var声明的变量为函数作用域，省略var声明的变量为全局作用域。
* let和const声明的变量为块作用域。

### 声明提升

> 声明提升是指变量的声明自动提升到函数作用域的顶部。

* var有声明提升。
* let和const没有声明提升。

### 暂时性死区

> 暂时性死区指的是，在声明变量前无法访问到变量。

* var有没有暂时性死区。
* let和const有暂时性死区。

### 冗余声明

> 冗余声明指的是，多次声明同一变量。

* var允许冗余声明。
* let和const不允许冗余声明，也不允许和var混用声明。

