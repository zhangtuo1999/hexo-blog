---
title: 【JavaScript红宝书】19.代理与反射
tags:
  - 前端
  - JavaScript
keywords: JavaScript
description: 《JavaScript高级程序设计（第4版）》第9章：代理与反射。
excerpt: 《JavaScript高级程序设计（第4版）》第9章：代理与反射。
abbrlink: a7feff9c
date: 2022-05-16 09:27:39
index_img: ../images/封面图/红宝书.png
---

# 1 代理基础

ECMAScript 6 新增的**代理和反射**为开发者提供了**拦截**并向基本操作中嵌入额外行为的能力。可以给目标对象定义一个关联的代理对象，这个代理对象可以作为抽象的目标对象来使用。在对目标对象的各种操作影响到目标对象之前，可以在代理对象中对这些操作加以控制。

## 1.1 创建空代理

代理是使用 Proxy 构造函数创建的。这个构造函数接收两个参数：目标对象和处理程序对象。要创建空代理可以传一个简单的对象字面量作为处理程序对象，从而让所有操作都能无阻地抵达目标对象。

定义目标对象和代理对象：

```javascript
const target = {	// 目标对象
  foo: "bar",
};
const proxy = new Proxy(target, {});	// 代理对象
```

访问代理对象等同于访问目标对象：

```javascript
console.log(target.foo);	// bar	
console.log(proxy.foo);		// bar
```

修改目标对象或者代理对象的属性会同时反映在两个对象上：

```javascript
proxy.foo = "baz";
console.log(target.foo);	// baz
console.log(proxy.foo);		// baz
```

注意：

1. Proxy.prototype 是 undefined，因此不能使用 instanceof 操作符。

   ```javascript
   console.log(proxy instanceof Proxy);	// 报错
   ```

2. 严格相等可以用来区分代理和目标

   ```javascript
   console.log(proxy === target);			// false
   ```

## 1.2 定义捕获器

**捕获器**就是处理程序对象中定义的基本操作拦截器。每个处理程序对象可以包含多个捕获器，每个捕获器对应一种基本操作，可以直接或者间接在代理对象上调用。每次在**代理对象**上调用这些基本操作时，代理会调用捕获器函数，从而拦截并修改默认行为。

```javascript
const target = {
  foo: "bar",
};
const handler = {
  get() {			// 定义一个 get() 捕获器
    return "baz";	// 修改 get() 行为
  },
};
const proxy = new Proxy(target, handler);
console.log(proxy.foo);		// baz （捕获器起作用了）
```

## 1.3 捕获器参数和反射 API

所有捕获器都可以访问相应的参数，基于这些参数可以重建被捕获方法的默认行为。比如，get() 捕获器会接收到目标对象、要查询的属性和代理对象三个参数。

```javascript
const target = {
  foo: "bar",
};
const handler = {
  get(trapTarget, property, receiver) {
    return trapTarget[property] + "!";		// 在原有属性上加一个叹号
  },
};
const proxy = new Proxy(target, handler);

console.log(proxy.foo);		// bar!
```

开发者并不需要手写重建行为，而是可以通过调用全局的 **Reflect 对象**（封装了原始行为）的同名方法来轻松重建。处理程序对象中所有可以捕获的方法都有对应的反射（Reflect）方法。这些方法和捕获器拦截的方法具有相同的名称和函数签名，而且也具有与被拦截方法相同的行为。

因此，使用反射 API 也可以像下面这样定义出空代理对象：

```javascript
const target = {
  foo: "bar",
};
const handler = {
  get() {
    return Reflect.get(...arguments);
  },
};

const proxy = new Proxy(target, handler);
console.log(proxy.foo);		// bar
```

事实上，如果想创建一个可以捕获所有方法，然后将每个方法转发给对应反射 API 的空代理，那么甚至不需要定义处理程序对象。

```javascript
const target = {
  foo: "bar",
};
const proxy = new Proxy(target, Reflect);
console.log(proxy.foo);		// bar
```

反射 API 为开发者准备好了样板代码，在此基础上开发者可以用最少的代码修改捕获的方法。

```javascript
const target = {
  foo: "bar",
};

const handler = {
  get() {
    return Reflect.get(...arguments) + "!";	
  },
};

const proxy = new Proxy(target, handler);
console.log(proxy.foo);		// bar!
```

## 1.4 捕获器不变式

使用捕获器几乎可以修改所有基本方法的行为，但是也不是没有限制。每个捕获器都知道目标对象的上下文捕获函数签名，而捕获处理程序的行为必须遵循**捕获器不变式**。捕获器不变式因方法不同而异，但是通常都是防止在捕获器中出现过于反常的行为。

比如，如果目标对象上有一个不可修改的属性，那么 get() 捕获器就不可以返回不同的值：

```javascript
const target = {};
Object.defineProperty(target, "foo", {
  value: "bar",				// 定义一个不可修改属性 foo
});

const handler = {
  get() {
    return "baz";			// 返回的值和定义时的不同
  },
};

const proxy = new Proxy(target, handler);
console.log(proxy.foo);		// 报错
```

## 1.5 可撤销代理

Proxy 暴露了revocable() 方法，这个方法可以支持撤销代理对象和目标对象的关联。撤销函数是幂等的，调用多少次都一样。撤销之后再调用代理会报错。

```javascript
const target = {
  foo: "bar",
};
const { proxy, revoke } = Proxy.revocable(target, {});
console.log(proxy.foo);		// bar
revoke();					// 调用撤销函数
console.log(proxy.foo);		// 再次调用代理会报错
```

## 1.6 优先使用反射 API

在某些情况下应该优先使用反射 API。

### 1.6.1 反射 API 和对象 API

1. 反射 API 并不限于捕获处理程序
2. 大多数反射 API 方法在 Object 类型上有对应的方法

通常，Object 上的方法适用于通用程序，而反射方法适用于细粒度的对象控制和操作。

### 1.6.2 状态标记（？）

很多反射方法返回称为状态标记的布尔值，表示执行的操作是否成功。有时候，状态标记比那些返回修改后的对象或者抛出错误的方法更有用。

初始代码：

```javascript
const obj = {};
try {
  Object.defineProperty(obj, "foo", "bar");
} catch (e) {
  console.log(e);
}
```

使用反射方法重构：

```javascript
const obj = {};
if (Reflect.defineProperty(obj, "foo", 'bar')) {
  console.log("success");		// 此处仍然报错？
} else {
  console.log("failure");
}
```

###  1.6.3 用反射方法替操作符

以下反射方法提供只有通过操作符才能完成的操作。

1. Reflect.get()：可以代替对象属性访问操作符`.`
2. Refilect.set()：可以代替赋值操作符`=`
3. Reflect.has()：可以代替 in 或者 with()
4. Reflect.deleteProperty()：可以代替 delete
5. Reflect.construct()：可以代替 new

### 1.6.4 安全地应用函数

在通过 apply 方法调用函数时，被调用地函数可能也定义了自己的 apply 属性。

为绕过这个问题，可以使用定义在 Function 原型上的 apply 方法：

```javascript
Function.prototype.apply.call(fn,thisVal,argumentList)
```

也可以使用 Reflect.apply 来避免：

```javascript
Reflect.apply(fn,thisVal,argumentList)
```

## 1.7 代理另一个代理

可以创建一个代理来代理另一个代理，在一个目标对象上构建多层拦截网。

```javascript
const target = { foo: "bar" };
const handler1 = {
  get() {
    return Reflect.get(...arguments) + "!";		// 拦截器1
  },
};
const handler2 = {
  get() {
    return Reflect.get(...arguments) + ".";		// 拦截器2
  },
};

const proxy1 = new Proxy(target, handler1);
const proxy2 = new Proxy(proxy1, handler2);

console.log(proxy2.foo);	// bar!.
```

## 1.8 代理的问题和不足

### 1.8.1 代理中的 this 值

大多数情况下，this 值符合预期。但是，如果目标对象依赖于对象标识，那可能遇到意料之外的问题。

### 1.8.2 代理与内部槽位

有些内置类型可能会依赖代理无法控制的机制，导致出错。

比如，Date 类型方法的执行依赖 this 值上的内部槽位`[[NumberDate]]`。代理对象上不存在这个槽位，于是会出现问题。

```javascript
const target = new Date();
const proxy = new Proxy(target, {});
proxy.getDate();		// 报错
```

# 2 代理捕获器和反射方法

代理可以捕获 **13** 种不同的基本操作。这些操作中各自有不同的反射 API 方法、参数、关联操作和不变式。

## 2.1 get()

get() 捕获器会在获取属性值的操作中被调用。对应的反射 API 方法为 Reflect.get()。

```javascript
const myTarget = {};
const proxy = new Proxy(myTarget, {
  get(target, property, receiver) {
    console.log("get()");
    return Reflect.get(...arguments);
  },
});
proxy.foo;
// get()
```

### 2.1.1 返回值

返回值无限制

### 2.1.2 拦截的操作

* proxy.property
* proxy[property]
* Object.create(proxy)[property]
* Reflect.get(proxy, property, receiver)

### 2.1.3 捕获器处理程序参数

1. target：目标对象。

2. property：引用的目标对象上的字符串（或 Symbol ）键属性。

3. receiver：代理对象或继承代理对象的对象。

### 2.1.4  捕获器不变式

如果 target.property 不可写且不可配置，则处理程序返回的值必须与 target.property 匹配。
如果 target.property 不可配置且` [[Get]] `特性为undefined，处理程序的返回值也必须是 undefined。

## 2.2 set()

set()捕获器会在设置属性值的操作中被调用。对应的反射API 方法为Reflect.set()。

```javascript
const myTarget = {};
const proxy = new Proxy(myTarget, {
  set(target, property, value, receiver) {
    console.log("set()");
    return Reflect.set(...arguments);
  },
});
proxy.foo = "bar";
// set()
```

### 2.2.1 返回值

返回 true 表示成功；返回 false 表示失败，严格模式下会抛出 TypeError。

### 2.2.2 拦截的操作

* proxy.property = value
* proxy[property] = value
* Object.create(proxy)[property] = value
* Reflect.set(proxy, property, value, receiver)

### 2.2.3 捕获器处理程序参数

1. target：目标对象。
2. property：引用的目标对象上的字符串键属性。
3. value：要赋给属性的值。
4. receiver：接收最初赋值的对象。

### 2.2.4  捕获器不变式

如果 target.property 不可写且不可配置，则不能修改目标属性的值。
如果 target.property 不可配置且`[[Set]]`特性为 undefined，则不能修改目标属性的值。
在严格模式下，处理程序中返回 false 会抛出 TypeError。

## 2.3 has()

has()捕获器会在in 操作符中被调用。对应的反射API 方法为 Reflect.has()。

```javascript
const myTarget = {};
const proxy = new Proxy(myTarget, {
  has(target, property) {
    console.log("has()");
    return Reflect.has(...arguments);
  },
});
"foo" in proxy;
// has()
```

### 2.3.1 返回值

has()必须返回布尔值，表示属性是否存在。返回非布尔值会被转型为布尔值。

### 2.3.2 拦截的操作

* property in proxy
* property in Object.create(proxy)
* with(proxy) {(property);}
* Reflect.has(proxy, property)

### 2.3.3 捕获器处理程序参数

1. target：目标对象。
2. property：引用的目标对象上的字符串键属性。

### 2.3.4 捕获器不变式

如果 target.property 存在且不可配置，则处理程序必须返回 true。
如果 target.property 存在且目标对象不可扩展，则处理程序必须返回 true。

## 2.4 其它捕获器

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect

# 3 代理模式

## 3.1 跟踪属性访问

通过捕获 get、set 和 has 等操作，可以知道对象属性什么时候被访问、修改和查询。

```javascript
const user = {
  name: "Jake",
};
const proxy = new Proxy(user, {
  get(target, property, receiver) {
    console.log(`Getting ${property}`);
    return Reflect.get(...arguments);
  },
  set(target, property, value, receiver) {
    console.log(`Setting ${property}=${value}`);
    return Reflect.set(...arguments);
  },
});
proxy.name; // Getting name
proxy.age = 27; // Setting age=27
```

## 3.2 隐藏属性

通过代理可以隐藏目标对象身上的属性。

```javascript
const hiddenProperties = ["foo", "bar"];
const targetObject = {
  foo: 1,
  bar: 2,
  baz: 3,
};
const proxy = new Proxy(targetObject, {
  get(target, property) {
    if (hiddenProperties.includes(property)) {
      return undefined;
    } else {
      return Reflect.get(...arguments);
    }
  },
  has(target, property) {
    if (hiddenProperties.includes(property)) {
      return false;
    } else {
      return Reflect.has(...arguments);
    }
  },
});

// get()
console.log(proxy.foo); // undefined
console.log(proxy.bar); // undefined
console.log(proxy.baz); // 3
// has()
console.log("foo" in proxy); // false
console.log("bar" in proxy); // false
console.log("baz" in proxy); // true
```

## 3.3 属性验证

因为所有赋值操作都会触发set()捕获器，所以可以根据所赋的值决定是允许还是拒绝赋值：

```javascript
const target = {
  onlyNumbersGoHere: 0,
};
const proxy = new Proxy(target, {
  set(target, property, value) {
    if (typeof value !== "number") {
      return false;
    } else {
      return Reflect.set(...arguments);
    }
  },
});
proxy.onlyNumbersGoHere = 1;
console.log(proxy.onlyNumbersGoHere); // 1
proxy.onlyNumbersGoHere = "2";
console.log(proxy.onlyNumbersGoHere); // 1
```

## 3.4 函数与构造函数参数验证

跟保护和验证对象属性类似，也可对函数和构造函数参数进行审查。比如，可以让函数只接收某种类型的值：

```javascript
function median(...nums) {
  return nums.sort()[Math.floor(nums.length / 2)];
}
const proxy = new Proxy(median, {
  apply(target, thisArg, argumentsList) {
    for (const arg of argumentsList) {
      if (typeof arg !== "number") {
        throw "Non-number argument provided";
      }
    }
    return Reflect.apply(...arguments);
  },
});
console.log(proxy(4, 7, 1)); // 4
console.log(proxy(4, "7", 1));
// Error: Non-number argument provided
```

类似地，可以要求实例化时必须给构造函数传参：

```javascript
class User {
  constructor(id) {
    this.id_ = id;
  }
}
const proxy = new Proxy(User, {
  construct(target, argumentsList, newTarget) {
    if (argumentsList[0] === undefined) {
      throw "User cannot be instantiated without id";
    } else {
      return Reflect.construct(...arguments);
    }
  },
});
new proxy(1);
new proxy();
// Error: User cannot be instantiated without id
```

## 3.5 数据绑定与可观察对象

通过代理可以把运行时中原本不相关的部分联系到一起。这样就可以实现各种模式，从而让不同的代码互操作。
比如，可以将被代理的类绑定到一个全局实例集合，让所有创建的实例都被添加到这个集合中：

```javascript
const userList = [];
class User {
  constructor(name) {
    this.name_ = name;
  }
}
const proxy = new Proxy(User, {
  construct() {
    const newUser = Reflect.construct(...arguments);
    userList.push(newUser);
    return newUser;
  },
});
new proxy("John");
new proxy("Jacob");
new proxy("Jingleheimerschmidt");
console.log(userList); // [User {}, User {}, User{}]
```

另外，还可以把集合绑定到一个事件分派程序，每次插入新实例时都会发送消息：

```javascript
const userList = [];
function emit(newValue) {
  console.log(newValue);
}
const proxy = new Proxy(userList, {
  set(target, property, value, receiver) {
    const result = Reflect.set(...arguments);
    if (result) {
      emit(Reflect.get(target, property, receiver));
    }
    return result;
  },
});
proxy.push("John");
// John
proxy.push("Jacob");
// Jacob
```

