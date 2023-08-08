---
title: 【JavaScript】valueOf()、toString()和toLocalString()
tags: JavaScript
keywords: JavaScript
description: JavaScript中valueof()、toString()和toLocalString()之间的区别
excerpt: JavaScript中valueof()、toString()和toLocalString()之间的区别
abbrlink: 54e9b57d
date: 2022-11-13 09:30:41
---

JavaScript 中 valueOf()、toString()和toLocalString()之间的区别？

valueOf、toString()、toLocalString()都是object对象身上的默认方法。只要是对象身上都有这三种方法。

valueOf()方法用来返回对象的原始值表示，toString()用来返回对象的字符串表示，toLocalString()用来返回对象的本地化字符串表示。

多数情况下，地区特定方法和通用方法相同，少数地区需要特定方法。



对于Array对象：

valueOf()返回的还是Array对象本身。

toString()返回的是对每个值调用toString()方法，并用逗号拼接起来的字符串。（对于值undefined和null返回空串）

toLocalString()返回的是对每个值调用toLocalString()方法，并用逗号拼接起来的字符串。（对于值undefined和null返回空串）

