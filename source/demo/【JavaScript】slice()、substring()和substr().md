---
title: 【JavaScript】slice()、substring()和substr()
tags: JavaScript
keywords: JavaScript
description: 字符串的slice、substring和substr方法之间的区别
excerpt: 字符串的slice、substring和substr方法之间的区别
abbrlink: 1a4fd1ee
date: 2022-11-13 09:47:58
---

JavaScript 中字符串的slice、substring和substr方法之间的区别？
这三个方法都是用来从字符串中提取子串的，都接受两个参数，表示字串的头尾位置。

slice、substring方法截取子串是含头不含尾的，substr方法截取的子串含头含尾。substr方法已经废弃了，mdn建议使用substring进行替代。

slice和substring之间最大的区别是在参数值为负时行为不一样。当参数是负数时，slice() 方法将所有的负值参数都当成字符串长度加上负参数值，substring() 方法把所有负参数值都转为0。

当然对于slice来说，当两个负参数值加上字符串长度之后仍然为负时，行为仍然有区别。如果第一个参数是负数，则从第一位开始截取，如果第二个参数是负数，则直接返回空串。

