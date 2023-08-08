---
title: 【JavaScript】原型和原型链
tags: JavaScript
keywords: JavaScript
description: JavaScript中的原型和原型链
excerpt: JavaScript中的原型和原型链
abbrlink: a1345e85
date: 2022-11-13 15:19:44
---

什么是**原型**？

每个构造函数都有一个`prototype`属性，这个属性是一个对象，包含应该由该构造函数构造出来的实例所共享的属性和方法。这个对象就是所创建实例的原型。

什么是**原型链**？

实例的`__proto__`属性指向实例的构造函数的`prototype`属性。同时，构造函数的`prototype`属性也是一个对象，这个对象的属性指向它的构造函数的`prototype`属性。就像这样以`__proto__`属性得到的对象组成了原型链。默认情况下，所有引用类型都继承自Object。按照这样的规则，最后会找到Object这个构造函数的`prototype`属性，这个属性对象的`__proto__`属性指向null。这就是原型链的终点。

原型链的问题，子类型实例化时无法给父类型传参。
