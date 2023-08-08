---
title: 【JavaScript】Promise-2-自定义封装
tags:
  - JavaScript
  - 前端
  - Promise
keywords: [JavaScript, Promise]
description: 自定义封装 Promise。
excerpt: 自定义封装 Promise。
abbrlink: bdb1cf7f
date: 2022-05-24 16:16:26
index_img: ../images/封面图/js.jpg
---

## 1 初始结构搭建

### 1.1 目录结构

目录结构：

```
─ 自定义封装Promise		// 根文件夹
    ├─ index.html		// 将两个js文件放入html中，便于浏览器打开
    ├─ index.js			// 测试 Promise 效果的地方
    └─ promise.js		// 重写 Promise 构造函数
```

index.html 中：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>
    <script src="./promise.js"></script>
    <script src="./index.js"></script>
</body>

</html>
```

### 1.2 起步

首先，在 index.js 中写一个基本的 Promise 使用方法：

```javascript
const p = new Promise((resolve, reject) => {
  resolve("OK");
});

p.then(
  (value) => {
    console.log(value);
  },
  (reason) => {
    console.log(reason);
  }
);

```



然后在 Promise.js 中重写 Promise 构造函数，并为 Promise 的原型添加 then() 方法。

Promise 构造函数接收一个执行器函数作为参数，这个执行器函数需要两个函数参数（这里命名为 resolve 和 reject）。 resolve() 和 reject() 都需要一个参数。

在 Promise 构造函数中定义好 resolve() 和 reject() 这两个函数参数，并同步执行执行器：

```javascript
function Promise(executor) {
  // 声明 resolve 和 reject 两个函数参数
  function resolve(data) {}
  function reject(data) {}
    
  // 同步调用执行器函数
  executor(resolve, reject);
}

// 给 Promise 添加 then() 方法
Promise.prototype.then = function (onResolved, onRejected) {};
```

## 2 resolve() 和 reject()

resolve() 和 reject() 函数主要完成两个功能，第一是修改 Promise 对象的状态，第二是返回结果值或原因。

注意 resolve() 和 reject() 两个函数中 this 的指向不是实例对象，而是 window。因此，需要先保存实例对象的 this 值。

```javascript
function Promise(executor) {
  // 添加属性
  this.PromiseState = "pending";
  this.PromiseResult = undefined;

  //   保存实例对象的 this
  const self = this;

  // 声明 resolve 和 reject 两个函数参数
  function resolve(data) {
    // 1. 修改 Promise 对象的状态
    self.PromiseState = "fulfilled";

    // 2. 返回结果值
    self.PromiseResult = data;
  }
  function reject(data) {
    // 1. 修改 Promise 对象的状态
    self.PromiseState = "rejected";

    // 2. 返回结果值
    self.PromiseResult = data;
  }

  // 同步调用执行器函数
  executor(resolve, reject);
}
```

## 3 throw 抛出异常

throw 抛出异常之后，Promise 对象的状态改为 rejected，并且失败的原因即 throw 抛出的参数。

```javascript
function Promise(executor) {
  // 添加属性
  this.PromiseState = "pending";
  this.PromiseResult = undefined;

  //   保存实例对象的 this
  const self = this;

  // 声明 resolve 和 reject 两个函数参数
  function resolve(data) {
    // 1. 修改 Promise 对象的状态
    self.PromiseState = "fulfilled";

    // 2. 返回结果值
    self.PromiseResult = data;
  }
  function reject(data) {
    // 1. 修改 Promise 对象的状态
    self.PromiseState = "rejected";

    // 2. 返回结果值
    self.PromiseResult = data;
  }

  // 同步调用执行器函数
  try {
    executor(resolve, reject);
  } catch (e) {
    // 捕获异常，修改状态
    reject(e);
  }
}
```

## 4 状态只允许修改一次

Promise 状态只能由 pending 改为 fulfilled或由 pending 改为 rejected，并且只能修改一次。一旦 Promise 的状态发生修改，再次修改状态会静默失败。

每次修改 Promise 状态前需要加一个判断，如果 Promise 状态不是 pending，则不执行状态转换。

```javascript
function Promise(executor) {
  // 添加属性
  this.PromiseState = "pending";
  this.PromiseResult = undefined;

  //   保存实例对象的 this
  const self = this;

  // 声明 resolve 和 reject 两个函数参数
  function resolve(data) {
    // 如果状态不是 pending 则不执行函数
    if (self.PromiseState !== "pending") return;

    // 1. 修改 Promise 对象的状态
    self.PromiseState = "fulfilled";

    // 2. 返回结果值
    self.PromiseResult = data;
  }
  function reject(data) {
    // 如果状态不是 pending 则不执行函数
    if (self.PromiseState !== "pending") return;

    // 1. 修改 Promise 对象的状态
    self.PromiseState = "rejected";

    // 2. 返回结果值
    self.PromiseResult = data;
  }

  // 同步调用执行器函数
  try {
    executor(resolve, reject);
  } catch (e) {
    // 捕获异常，修改状态
    reject(e);
  }
}
```

## 5 执行同步任务回调

Promise 实例上的 then 方法，接收两个回调函数作为参数。如果 Promise 实例对象的状态为 fulfilled，则调用第一个回调函数；如果 Promise 实例对象的状态为 rejected，则调用第二个回调函数。这两个函数的参数都是实例身上的结果。

```javascript
// 给 Promise 添加 then() 方法
Promise.prototype.then = function (onResolved, onRejected) {
  // 调用回调函数
  if (this.PromiseState === "fulfilled") {
    onResolved(this.PromiseResult);
  }

  if (this.PromiseState === "rejected") {
    onRejected(this.PromiseResult);
  }
};
```

## 6 执行异步任务回调

现在自定义的 Promise 构造函数的构造器中只能执行同步的代码。如果执行异步代码（比如定时器），then() 处理程序无法执行。

```javascript
const p = new Promise((resolve, reject) => {
  resolve("ok");		// 同步代码
});
p.then(
  (value) => {
    console.log(value);	// ok  then()处理程序可以正常执行
  },
  (reason) => {
    console.log(reason);
  }
);
```

```javascript
const p = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("ok");		// 异步代码
  }, 1000);
});
p.then(
  (value) => {
    console.log(value);	// 无法执行正常输出
  },
  (reason) => {
    console.log(reason);
  }
);
```

这是因为，执行 p.then() 的时候 p 的状态是 pending，此时 p.then() 方法还不能处理 pending 状态，因此不会执行任何代码。

想要在 p 将 pending 状态修改为 fulfilled 或者 rejected 之后再调用 p.then() 方法中的 onResolved 或者 onRejected，就必须将 onResolved 和 onRejected 都保存下来，并且在 resolve() 和 reject() 方法的最后调用  onResolved 和 onRejected。

```javascript
function Promise(executor) {
  // 添加属性
  this.PromiseState = "pending";
  this.PromiseResult = undefined;

  // 保存回调
  this.callback = null;

  // 保存实例对象的 this
  const self = this;

  // 声明 resolve 和 reject 两个函数参数
  function resolve(data) {
    // 如果状态不是 pending 则不执行函数
    if (self.PromiseState !== "pending") return;

    // 1. 修改 Promise 对象的状态
    self.PromiseState = "fulfilled";

    // 2. 返回结果值
    self.PromiseResult = data;

    // 调用 onResolved
    if (self.callback.onResolved) {
      self.callback.onResolved(data);
    }
  }
  function reject(data) {
    // 如果状态不是 pending 则不执行函数
    if (self.PromiseState !== "pending") return;

    // 1. 修改 Promise 对象的状态
    self.PromiseState = "rejected";

    // 2. 返回结果值
    self.PromiseResult = data;

    // 调用 onRejected
    if (self.callback.onRejected) {
      self.callback.onRejected(data);
    }
  }

  // 同步调用执行器函数
  try {
    executor(resolve, reject);
  } catch (e) {
    // 捕获异常，修改状态
    reject(e);
  }
}

// 给 Promise 添加 then() 方法
Promise.prototype.then = function (onResolved, onRejected) {
  // 调用回调函数
  if (this.PromiseState === "fulfilled") {
    onResolved(this.PromiseResult);
  }

  if (this.PromiseState === "rejected") {
    onRejected(this.PromiseResult);
  }

  if (this.PromiseState === "pending") {
    this.callback = {
      onResolved,
      onRejected,
    };
  }
};

```

## 7 执行多个回调

如果为 Promise 对象指定多个成功回调或失败回调，Promise 对象在成功后会执行所有成功回调，在失败后会执行所有失败回调。

现在的代码中，如果给 Promise 对象指定多个成功回调，那么只会执行最后定义的那个。因为之前定义的回调都被后一个给覆盖了。

```javascript
const p = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("ok");
  }, 1000);
});

p.then((value) => {
  console.log("first", value);		// 不会执行
});

p.then((value) => {
  console.log("second", value);		// second ok
});
```

想要解决这个问题，就要把 Promise 构造函数中只保存一对成功和失败回调的 callback 修改为能保存多个回调的 callbacks 数组。 

```javascript
function Promise(executor) {
  // 添加属性
  this.PromiseState = "pending";
  this.PromiseResult = undefined;

  // 保存回调
  this.callbacks = [];

  // 保存实例对象的 this
  const self = this;

  // 声明 resolve 和 reject 两个函数参数
  function resolve(data) {
    // 如果状态不是 pending 则不执行函数
    if (self.PromiseState !== "pending") return;

    // 1. 修改 Promise 对象的状态
    self.PromiseState = "fulfilled";

    // 2. 返回结果值
    self.PromiseResult = data;

    // 调用 onResolved
    self.callbacks.forEach((item) => {
      item.onResolved(data);
    });
  }
  function reject(data) {
    // 如果状态不是 pending 则不执行函数
    if (self.PromiseState !== "pending") return;

    // 1. 修改 Promise 对象的状态
    self.PromiseState = "rejected";

    // 2. 返回结果值
    self.PromiseResult = data;

    // 调用 onRejected
    self.callbacks.forEach((item) => {
      item.onRejected(data);
    });
  }

  // 同步调用执行器函数
  try {
    executor(resolve, reject);
  } catch (e) {
    // 捕获异常，修改状态
    reject(e);
  }
}

// 给 Promise 添加 then() 方法
Promise.prototype.then = function (onResolved, onRejected) {
  // 调用回调函数
  if (this.PromiseState === "fulfilled") {
    onResolved(this.PromiseResult);
  }

  if (this.PromiseState === "rejected") {
    onRejected(this.PromiseResult);
  }

  if (this.PromiseState === "pending") {
    this.callbacks.push({ onResolved, onRejected });
  }
};

```

## 8 同步修改状态时 then() 方法返回值

then() 方法是有返回值的，then() 方法的返回值是一个 Promise 对象。

传给 then() 方法的成功回调函数参数中，如果返回值是 Promise 对象，那么 then() 方法返回的 Promise 对象的状态取决于函数参数中返回值的状态；如果返回值不是 Promise 对象，那么 then() 方法会将其包装成一个成功的 Promise 对象；如果抛出错误，那么 then() 方法的返回 Promise 对象为失败状态，并且结果值是抛出的信息。

```javascript
// 给 Promise 添加 then() 方法
Promise.prototype.then = function (onResolved, onRejected) {
  return new Promise((resolve, reject) => {
    // 调用回调函数
    if (this.PromiseState === "fulfilled") {
      try {
        // 获取函数执行结果
        const result = onResolved(this.PromiseResult);
        if (result instanceof Promise) {
          // 执行结果是 Promise 实例时
          result.then(
            (v) => {
              resolve(v);
            },
            (r) => {
              reject(r);
            }
          );
        } else {
          // 不是 Promise 实例时
          resolve(result);
        }
      } catch (error) {
        reject(error);
      }
    }

    if (this.PromiseState === "rejected") {
      onRejected(this.PromiseResult);
    }

    if (this.PromiseState === "pending") {
      this.callbacks.push({ onResolved, onRejected });
    }
  });
};

```

##  9 异步修改状态时 then() 方法返回值

异步修改状态后，onResolved 和 onRejected 需要根据结果来修改状态。

```javascript
// 给 Promise 添加 then() 方法
Promise.prototype.then = function (onResolved, onRejected) {
  // 保存 this 指针
  const self = this;
  return new Promise((resolve, reject) => {
    // 调用回调函数
    if (this.PromiseState === "fulfilled") {
      try {
        // 获取函数执行结果
        const result = onResolved(this.PromiseResult);
        if (result instanceof Promise) {
          // 执行结果是 Promise 实例时
          result.then(
            (v) => {
              resolve(v);
            },
            (r) => {
              reject(r);
            }
          );
        } else {
          // 不是 Promise 实例时
          resolve(result);
        }
      } catch (error) {
        reject(error);
      }
    }

    if (this.PromiseState === "rejected") {
       try {
        const result = onRejected(this.PromiseResult);
        if (result instanceof Promise) {
          // 执行结果是 Promise 实例时
          result.then(
            (v) => {
              resolve(v);
            },
            (r) => {
              reject(r);
            }
          );
        } else {
          // 不是 Promise 实例时
          resolve(result);
        }
      } catch (error) {
        reject(error);
      }
    }

    if (this.PromiseState === "pending") {
      this.callbacks.push({
        onResolved: function () {
          try {
            const result = onResolved(self.PromiseResult);
            if (result instanceof Promise) {
              result.then(
                (v) => {
                  resolve(v);
                },
                (r) => {
                  reject(r);
                }
              );
            } else {
              resolve(result);
            }
          } catch (error) {
            reject(error);
          }
        },
        onRejected: function () {
          try {
            const result = onRejected(self.PromiseResult);
            if (result instanceof Promise) {
              result.then(
                (v) => {
                  resolve(v);
                },
                (r) => {
                  reject(r);
                }
              );
            } else {
              resolve(result);
            }
          } catch (error) {
            reject(error);
          }
        },
      });
    }
  });
};

```

## 10 封装函数代码

```javascript
// 给 Promise 添加 then() 方法
Promise.prototype.then = function (onResolved, onRejected) {
  // 保存 this 指针
  const self = this;
  return new Promise((resolve, reject) => {
    function callback(type) {
      try {
        // 获取函数执行结果
        const result = type(self.PromiseResult);
        if (result instanceof Promise) {
          // 执行结果是 Promise 实例时
          result.then(
            (v) => {
              resolve(v);
            },
            (r) => {
              reject(r);
            }
          );
        } else {
          // 不是 Promise 实例时
          resolve(result);
        }
      } catch (error) {
        reject(error);
      }
    }
    // 调用回调函数
    if (this.PromiseState === "fulfilled") {
      callback(onResolved);
    }

    if (this.PromiseState === "rejected") {
      callback(onRejected);
    }

    if (this.PromiseState === "pending") {
      this.callbacks.push({
        onResolved: function () {
          callback(onResolved);
        },
        onRejected: function () {
          callback(onRejected);
        },
      });
    }
  });
};
```

## 11 catch() 

```javascript
// 给 Promise 添加 catch() 方法
Promise.prototype.catch = function (onRejected) {
  return this.then(null, onRejected);
};
```

## 12 添加默认函数参数

```javascript
// 给 Promise 添加 then() 方法
Promise.prototype.then = function (onResolved, onRejected) {
  // 保存 this 指针
  const self = this;

  // 判断是否传递了失败回调
  if (typeof onRejected !== "function") {
    onRejected = (reason) => {
      throw reason;
    };
  }
  
  // 判断是否传递了成功回调
  if (typeof onResolved !== "function") {
    onResolved = (value) => {
      return value;
    };
  }
    
  return new Promise((resolve, reject) => {
    function callback(type) {
      try {
        // 获取函数执行结果
        const result = type(self.PromiseResult);
        if (result instanceof Promise) {
          // 执行结果是 Promise 实例时
          result.then(
            (v) => {
              resolve(v);
            },
            (r) => {
              reject(r);
            }
          );
        } else {
          // 不是 Promise 实例时
          resolve(result);
        }
      } catch (error) {
        reject(error);
      }
    }
    // 调用回调函数
    if (this.PromiseState === "fulfilled") {
      callback(onResolved);
    }

    if (this.PromiseState === "rejected") {
      callback(onRejected);
    }

    if (this.PromiseState === "pending") {
      this.callbacks.push({
        onResolved: function () {
          callback(onResolved);
        },
        onRejected: function () {
          callback(onRejected);
        },
      });
    }
  });
};
```

## 13 Promise.resolve() 

```javascript
// 添加 Promise.resolve 方法
Promise.resolve = function (value) {
  return new Promise((resolve, reject) => {
    if (value instanceof Promise) {
      value.then(
        (v) => {
          resolve(v);
        },
        (r) => {
          reject(r);
        }
      );
    } else {
      resolve(value);
    }
  });
};
```

## 14 Promise.reject()

```javascript
// 添加 Promise.reject 方法
Promise.reject = function (value) {
  return new Promise((resolve, reject) => {
    reject(value);
  });
};
```

## 15 Promise.all()

```javascript
// 添加 Promise.all 方法
Promise.all = function (promises) {
  return new Promise((resolve, reject) => {
    let count = 0;
    const results = [];
    for (let i = 0; i < promises.length; i++) {
      promises[i].then(
        (v) => {
          count++;
          results[i] = v;
          if (count === promises.length) {
            resolve(results);
          }
        },
        (r) => {
          reject(r);
        }
      );
    }
  });
};
```

## 16 Promise.race() 

​	

```
// 添加 Promise.race 方法
Promise.race = function (promises) {
  return new Promise((resolve, reject) => {
    for (let i = 0; i < promises.length; i++) {
      promises.then(
        (v) => {
          resolve(v);
        },
        (r) => {
          reject(r);
        }
      );
    }
  });
};

```

## 17 异步执行 then() 回调

```javascript
function Promise(executor) {
  // 添加属性
  this.PromiseState = "pending";
  this.PromiseResult = undefined;

  // 保存回调
  this.callbacks = [];

  // 保存实例对象的 this
  const self = this;

  // 声明 resolve 和 reject 两个函数参数
  function resolve(data) {
    // 如果状态不是 pending 则不执行函数
    if (self.PromiseState !== "pending") return;

    // 1. 修改 Promise 对象的状态
    self.PromiseState = "fulfilled";

    // 2. 返回结果值
    self.PromiseResult = data;

    // 调用 onResolved
    setTimeout(() => {
      self.callbacks.forEach((item) => {
        item.onResolved(data);
      });
    });
  }

  function reject(data) {
    // 如果状态不是 pending 则不执行函数
    if (self.PromiseState !== "pending") return;

    // 1. 修改 Promise 对象的状态
    self.PromiseState = "rejected";

    // 2. 返回结果值
    self.PromiseResult = data;

    // 调用 onRejected
    setTimeout(() => {
      self.callbacks.forEach((item) => {
        item.onRejected(data);
      });
    });
  }

  // 同步调用执行器函数
  try {
    executor(resolve, reject);
  } catch (e) {
    // 捕获异常，修改状态
    reject(e);
  }
}

// 给 Promise 添加 then() 方法
Promise.prototype.then = function (onResolved, onRejected) {
  // 保存 this 指针
  const self = this;

  // 判断是否传递了失败回调
  if (typeof onRejected !== "function") {
    onRejected = (reason) => {
      throw reason;
    };
  }

  // 判断是否传递了成功回调
  if (typeof onResolved !== "function") {
    onResolved = (value) => {
      return value;
    };
  }

  return new Promise((resolve, reject) => {
    function callback(type) {
      try {
        // 获取函数执行结果
        const result = type(self.PromiseResult);
        if (result instanceof Promise) {
          // 执行结果是 Promise 实例时
          result.then(
            (v) => {
              resolve(v);
            },
            (r) => {
              reject(r);
            }
          );
        } else {
          // 不是 Promise 实例时
          resolve(result);
        }
      } catch (error) {
        reject(error);
      }
    }
    // 调用回调函数
    if (this.PromiseState === "fulfilled") {
      setTimeout(() => {
        callback(onResolved);
      });
    }

    if (this.PromiseState === "rejected") {
      setTimeout(() => {
        callback(onRejected);
      });
    }

    if (this.PromiseState === "pending") {
      this.callbacks.push({
        onResolved: function () {
          callback(onResolved);
        },
        onRejected: function () {
          callback(onRejected);
        },
      });
    }
  });
};
```

## 18 class 形式

```javascript
class Promise {
  // 构造函数
  constructor(executor) {
    // 添加属性
    this.PromiseState = "pending";
    this.PromiseResult = undefined;

    // 保存回调
    this.callbacks = [];

    // 保存实例对象的 this
    const self = this;

    // 声明 resolve 和 reject 两个函数参数
    function resolve(data) {
      // 如果状态不是 pending 则不执行函数
      if (self.PromiseState !== "pending") return;

      // 1. 修改 Promise 对象的状态
      self.PromiseState = "fulfilled";

      // 2. 返回结果值
      self.PromiseResult = data;

      // 调用 onResolved
      setTimeout(() => {
        self.callbacks.forEach((item) => {
          item.onResolved(data);
        });
      });
    }

    function reject(data) {
      // 如果状态不是 pending 则不执行函数
      if (self.PromiseState !== "pending") return;

      // 1. 修改 Promise 对象的状态
      self.PromiseState = "rejected";

      // 2. 返回结果值
      self.PromiseResult = data;

      // 调用 onRejected
      setTimeout(() => {
        self.callbacks.forEach((item) => {
          item.onRejected(data);
        });
      });
    }

    // 同步调用执行器函数
    try {
      executor(resolve, reject);
    } catch (e) {
      // 捕获异常，修改状态
      reject(e);
    }
  }

  // 给 Promise 添加 then() 方法
  then(onResolved, onRejected) {
    // 保存 this 指针
    const self = this;

    // 判断是否传递了失败回调
    if (typeof onRejected !== "function") {
      onRejected = (reason) => {
        throw reason;
      };
    }

    // 判断是否传递了成功回调
    if (typeof onResolved !== "function") {
      onResolved = (value) => {
        return value;
      };
    }

    return new Promise((resolve, reject) => {
      function callback(type) {
        try {
          // 获取函数执行结果
          const result = type(self.PromiseResult);
          if (result instanceof Promise) {
            // 执行结果是 Promise 实例时
            result.then(
              (v) => {
                resolve(v);
              },
              (r) => {
                reject(r);
              }
            );
          } else {
            // 不是 Promise 实例时
            resolve(result);
          }
        } catch (error) {
          reject(error);
        }
      }
      // 调用回调函数
      if (this.PromiseState === "fulfilled") {
        setTimeout(() => {
          callback(onResolved);
        });
      }

      if (this.PromiseState === "rejected") {
        setTimeout(() => {
          callback(onRejected);
        });
      }

      if (this.PromiseState === "pending") {
        this.callbacks.push({
          onResolved: function () {
            callback(onResolved);
          },
          onRejected: function () {
            callback(onRejected);
          },
        });
      }
    });
  }

  // 给 Promise 添加 catch() 方法
  catch(onRejected) {
    return this.then(null, onRejected);
  }

  // 添加 Promise.resolve 方法
  static resolve(value) {
    return new Promise((resolve, reject) => {
      if (value instanceof Promise) {
        value.then(
          (v) => {
            resolve(v);
          },
          (r) => {
            reject(r);
          }
        );
      } else {
        resolve(value);
      }
    });
  }

  // 添加 Promise.reject 方法
  static reject(value) {
    return new Promise((resolve, reject) => {
      reject(value);
    });
  }

  // 添加 Promise.all 方法
  static all(promises) {
    return new Promise((resolve, reject) => {
      let count = 0;
      const results = [];
      for (let i = 0; i < promises.length; i++) {
        promises[i].then(
          (v) => {
            count++;
            results[i] = v;
            if (count === promises.length) {
              resolve(results);
            }
          },
          (r) => {
            reject(r);
          }
        );
      }
    });
  }

  // 添加 Promise.race 方法
  static race(promises) {
    return new Promise((resolve, reject) => {
      for (let i = 0; i < promises.length; i++) {
        promises[i].then(
          (v) => {
            resolve(v);
          },
          (r) => {
            reject(r);
          }
        );
      }
    });
  }
}

```

