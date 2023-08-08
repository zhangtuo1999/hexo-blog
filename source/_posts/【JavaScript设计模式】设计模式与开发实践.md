---
title: 【JavaScript设计模式】设计模式与开发实践
tags: JavaScript
keywords: JavaScript
description: JavaScript设计模式
excerpt: JavaScript设计模式
abbrlink: a700808d
date: 2022-11-15 11:43:00
index_img: ../images/封面图/js设计模式.png
---

## 1 原型模式

原型模式是一种用来创建对象的模式。JavaScript没有选择类式继承，而是通过原型委托的方式来实现对象和对象之间的继承。

原型编程泛型的基本原则：

* 所有的数据都是对象

  在JavaScript中，除了undefined外一切皆对象（number、boolean、string类型数据可以通过包装类的方式成为对象）。JavaScript中，所有对象都是从Object.prototype这个对象克隆得到的。

* 得到一个对象的方法不是通过实例化类，而是找到一个对象作为原型并克隆它

  new操作符后面跟上构造函数所执行的动作：

  1首先创建一个对象，2把这个对象的re's属性设置为构造函数的原型，3把构造函数的this指向新对象，4执行构造函数内部代码，5如果构造函数不返回对象那就返回新对象。34两步可以调用构造函数的call或apply方法。

* 对象记住它的原型

  对象通过proto属性来记住它原型。

* 对象在无法响应某个请求时委托给原型

  访问对象的某个属性时，如果对象身上没有这个属性，那就去它的原型身上去找，如果仍然没有找到，那就沿着原型链找下去，直到找到该属性或者到达原型链的终点，也就是Object.prototype的原型null。



this总是指向调用所在函数时的环境（谁调用就指向谁）。this指向有四种：

1. this所在函数作为对象的属性调用。此时this指向该对象。
2. this所在函数作为普通函数被调用。此时this指向全局对象。
3. this所在函数作为构造函数被调用。此时this指向新创建的对象。（通常情况下，this就指向返回的对象。如果构造函数**显式**返回一个**对象**，那么this不指向这个返回的对象。）
4. 函数对象的call和apply方法。此时的this指向传入的参数。



call和apply。都是用来改变函数运行时内部的this值的。区别在于apply接受两个参数，第一个参数是this指向，第二参数是类数组对象。apply会将类数组对象中的所有元素作为参数传给被调用的函数。call参数数量不固定，第一个参数依然是this指向，之后的参数全部传给被调用的函数。



（TODO）手写实现bind函数。



闭包。

高阶函数。高阶函数是指可以作为参数被传递或者作为返回值输出的函数。

## 2 单例模式

单例模式：保证一个类只有一个实例，并提供一个访问它的全局访问点。

### 2.1 标准的单例模式

用一个变量来标志当前是否已经为某个类创建过对象。如果创建过，则在下一次获取该类的实例时，直接返回之前创建的对象。

```javascript
function Singleton(name){
    this.name = name
    this.instence = null
}

Singleton.getInstence = function(name){
    if(!this.instence){
        this.instence = new Singleton(name)
    }
    return this.instence
}

const s1 = Singleton('foo')
const s2 = Singleton('bar')

console.log(s1 === s2)  // true
```

或者将实例封装在闭包中：

```javascript
function Singlelon(name){
    this.name = name
}

Singlelon.getInstance = (function(){
    let instance = null
    return function(name){
        if(!instance){
            instance = new Singlelon(name)
        }
        return instance
    }
})()


const s1 = Singlelon.getInstance('foo')
const s2 = Singlelon.getInstance('bar')

console.log(s1 === s2)	// true
```

**缺点**：用户必须知道这是一个单例类，必须使用特定的Singleton.getIntance方法来获取实例。这和一般情况下使用new 不同，增加了行为的不一致性和类的不透明性。

### 2.2 透明的单例模式

用户从这个类中创建对象时，可以像任何其他普通类一样：

```javascript
let Singlelon = (function(){
    let instance = null
    let Singlelon = function(name){
        if(!instance){
            this.name = name
            instance = this
        }
        return instance
    }
    return Singlelon
})()

const s1 = new Singlelon('foo')
const s2 = new Singlelon('bar')

console.log(s1 === s2) //true
```

缺点：Singlelon 构造函数同时负责创建单例和管理单例（保证单例对象只有一个），违反了单一职责原则。

### 2.3 抽取管理单例的逻辑 - 代理

其中一种解决方案是，将负责管理单例的逻辑移到代理类中：

```javascript
function Singlelon(name){
    this.name = name
}

let ProxySinglelon = (function(){
    let instance = null
    return function(name){
        if(!instance){
            instance = new Singlelon(name)
        }
        return instance
    }
})()

const s1 = new ProxySinglelon('foo')
const s2 = new ProxySinglelon('bar')

console.log(s1 === s2)
```

### 2.4 抽取管理单例的逻辑 - 封装

另一种解决方案是把管理单例的逻辑抽取出来，把这些逻辑封装在 getSingle 函数的内部，创建对象的方法 Constructor 被当作是参数动态传入 getSingle 函数。

```shell
function getSingle(Constructor){
    let result = null
    return function(){
        return result || (result = Constructor(arguments))
    }
}

function Singlelon(name){
    this.name = name
}

const s1 = getSingle(Singlelon)('foo')
const s2 = getSingle(Singlelon)('bar')

console.log(s1 === s2)
```

## 3 策略模式

策略模式：定义一系列算法，把它们一个个封装起来，并且使他们可以相互替换。目的是将算法的使用和算法的实现分离开。

一个基于策略模式的程序至少由两部分组成。第一个部分是一组策略类，策略类封装了具体的算法，并负责具体的计算过程。第二个部分是环境类 Context，Context 接受客户的请求，随后把请求委托给某一个策略类。要做到这点，说明 Context 中要维持对某个策略对象的引用。

优点：

1. 策略模式利用组合、委托和多态等技术和思想，可以有效地避免多重条件选择语句。
2. 策略模式提供了对开放—封闭原则的完美支持，将算法封装在独立的 strategy 中，使得它 们易于切换，易于理解，易于扩展。

在函数作为一等对象的语言中，策略模式是隐形的。 strategy 就是值为函数的变量。实际上在 JavaScript 这种将函数作为一等对象的语言里，策略模式已经融入到了语言本身 当中，我们经常用高阶函数来封装不同的行为，并且把它传递到另一个函数中。当我们对这些函 数发出“调用”的消息时，不同的函数会返回不同的执行结果。

### 3.1 例子 - 计算奖金

计算奖金，奖金的数目由薪水和绩效评级决定。绩效评级为S的，获得4倍薪水；A有3倍；B有2倍。

```javascript
let strategies = {
    S : function(salary){
        return salary * 4
    },

    A : function(salary){
        return salary * 3
    },

    B : function(salary){
        return salary * 2
    }
}

function calculateBonus(level, salary){
    return strategies[level](salary)
}

console.log(calculateBonus('S', 100))   // 400
console.log(calculateBonus('A', 150))   // 450
```

在此代码中，strategies 作为策略对象，calculateBonus 作为 contex，负责接受请求并委托给策略对象。

### 3.2 例子 - 表单校验

注册页的几条校验逻辑：

1. 用户名不能为空
2. 密码长度不能少于6位
3. 手机号码必须符合格式

```javascript
/**
 * 不同的表单校验策略
 */
let strategies = {
    isNotEmpty: function(value, errorMsg){
        if(value === ''){return errorMsg}
    },
    minLength: function(value, length, errorMsg){
        if(value.length < length){return errorMsg}
    },
    isMobile: function(value, errorMsg){
        if('!/(^1[3|5|8][0-9]{9}$)/'.test( value )){return errorMsg}
    }
}

/**
 * 策略类
 */
function Validator(){
    // ValidatorList 中存放的是校验函数
    this.ValidatorList = []
}

Validator.prototype.add = function(dom, rule, errorMsg){
    const array = rule.split(':')
    this.ValidatorList.push(function(){
        const strategy = array.shift()
        array.unshift(dom.value)
        array.push(errorMsg)
        return strategies[strategy].apply(dom, array)
    })
}

Validator.prototype.start = function(){
    this.ValidatorList.forEach(func=>{
        const msg = func()
        if(msg){
            return msg
        }
    })
}

function validateFunc(){
    const validator = new Validator()

    validator.add(registerForm.userName, 'isNonEmpty', '用户名不能为空')
    validator.add(registerForm.password, 'minLength:6', '密码长度不能少于 6 位')
    validator.add(registerForm.phoneNumber, 'isMobile', '手机号码格式不正确')

    const errorMsg = validator.start()
    return errorMsg
}

const registerForm = document.getElementById('registerForm')
registerForm.onsubmit = function(){
    const errorMsg = validateFunc()
    if(errorMsg){
        alert(errorMsg)
        return false
    }
}
```
