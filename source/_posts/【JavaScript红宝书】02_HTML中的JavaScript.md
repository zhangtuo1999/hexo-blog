---
title: 【JavaScript红宝书】2.HTML中的JavaScript
tags:
  - 前端
  - JavaScript
excerpt: 《JavaScript高级程序设计（第4版）》笔记。原书第2章：HTML中的JavaScript。主要内容：script标签的属性，比如：async、crossorigin、defer、integrity、src和type。
abbrlink: a34cc8fd
date: 2022-04-24 15:12:50
index_img: ../images/封面图/红宝书.png
---

# HTML中的JavaScript

## 1 `<script>`元素

使用`<script>`元素在 HTML 中插入 JavaScript。

**`<script>`元素的属性**：

* `src`：（可选）引用外部脚本的 URI，用来代替在文档中插入脚本。
* `type`：（可选）指定脚本语言的内容类型（MIME 类型）。缺失会被视为 JavaScript。**最好不指定 type 属性**。

* `async`：（可选）表示应该**并行**下载该脚本并尽快解析执行。只对**外部脚本文件**生效。这是一个**布尔属性**，存在即为 true 值，缺失即为 false 值。

* `defer`：（可选）表示应该在文档完成解析后执行该脚本。只对**外部脚本文件**生效。布尔属性。

* `crossorigin`：（可选）配置相关请求的 CORS 设置。

  | 可选值          | 描述                                                         |
  | --------------- | ------------------------------------------------------------ |
  | anonymous,""    | 对此元素的 CORS 请求将不设置凭据标志。                       |
  | use-credentials | 对此元素的 CORS 请求将设置凭证标志；这意味着请求将提供凭据。 |
  
* `integrity`：（可选）表示允许将接收到的资源签名和指定的加密签名进行比对，用来验证子资源的完整性。如果收到的资源签名和该属性指定的签名不匹配，则页面报错并且不会执行脚本。

* `nomodule`：（可选）这个布尔属性被设置来标明这个脚本在支持 [ES2015 modules](https://hacks.mozilla.org/2015/08/es6-in-depth-modules/) 的浏览器中**不执行**。 — 实际上，这可用于在不支持模块化 JavaScript 的旧浏览器中提供回退脚本。

* [`nonce`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/script#attr-nonce)：详见 MDN

* [`referrerpolicy`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/script#attr-referrerpolicy)：：详见 MDN

* `charset`：（废弃）如果存在，值必须为 “`utf-8`” 。当然声明 `charset` 是没有必要的，因为页面文档必须使用 UTF-8，而 `script` 元素会从页面文档中继承这个属性。

* `language`：（废弃）和 type 属性类似，这个属性定义脚本使用的语言。 但是与 type 不同的是，这个属性的可能值从未被标准化过。请用`type`属性代替这个属性。

**注意**：

1. 使用 `src`属性的`<script>`元素不应该在标签中包含其它代码。如果两者都提供，浏览器只会下载并执行脚本文件，不会执行标签中的代码。
2. 如果`src`是一个完整的 URL，那么浏览器会向这个 URL 发送一个 **GET** 请求，以获取响应的资源。这个请求**不受同源策略的限制**，但是获取到的 JavaScript 内容是受同源策略限制的。

### 1.1 标签位置

通常将所有的 JavaScript 引用放在`<body>`元素中、页面内容的后面。例如：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Document</title>
</head>
<body>
    <!-- 这里是页面内容 -->
    
    <script src="... ..."></script>
    <script>
        // ... ... 
    </script>
</body>
</html>
```

这样，页面内容会在处理 JavaScript **前**渲染，减少了页面空白时间。

### 1.2 异步执行脚本

如果`<script>`标签使用了`async`属性，浏览器会**立即下载**脚本，并且**不会阻塞**页面的渲染。

注意：

* 只适用于外部脚本。
* 如果有两个使用`async`属性的`<script>`标签，浏览器**不保证**执行顺序。

例如：

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
    <!-- 页面内容 -->
    <script async src="... ..."></script>
    <script async src="... ..."></script>
</body>
</html>
```

这个例子中，第二个脚本**未必**在第一个脚本之后执行。

### 1.3 推迟执行脚本

如果`<script>`标签使用了`defer`属性，浏览器会**立即下载**脚本，但是会在**整个页面解析完成后**再去**执行**脚本。例如：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script defer src="... ..."></script>
</head>
<body>
    <!-- 页面内容 -->
</body>
</html>
```

这个例子中，虽然`script`标签写在`head`标签内。但是因为其具有`defer`属性，浏览器会解析完 DOM 后再去执行该脚本。

**注意**：

* 只适用于外部脚本。

* 如果有两个`defer`属性的脚本，浏览器会**按照次序**去**顺序**执行。

### 1.4 动态加载脚本

除了使用`script`标签，还可以通过向 DOM 中动态添加`script`元素的方式加载指定脚本。例如：

```js
let script = document.createElement("script");
script.src = "https://cdn.bootcdn.net/ajax/libs/vue/3.2.33/vue.cjs.js";
document.head.appendChild(script);
```

**注意**：

1. 默认情况下这种方式是异步形式进行加载的，相当于加上了`async`属性。

2. 以这种形式获取的资源对浏览器**预加载器**是不可见的。想要让预加载器知道这些动态请求文件的存在，可以在文档的头部进行显式声明。

   ```html
   <link rel="preload" href="https://cdn.bootcdn.net/ajax/libs/vue/3.2.33/vue.cjs.js">
   ```

## 2 行内代码和外部文件

虽然可以在 HTML 中嵌入 JavaScript 代码，但是最佳实践时尽可能将 JavaScript 代码放在**外部文件**中。优点有：

* 可维护性。使用一个目录保存所有 JavaScript 代码，比将 JavaScript 代码分散到各个 HTML 中更容易维护。
* 缓存。浏览器可以缓存所有外部链接文件。如果两个页面用到了同一个文件，那么浏览器就可以从缓存中加载该文件。

## 3 `<noscript>`元素

`noscript`元素可以包含在任何出现在`body`中的 HTML 元素，`<scrpit>`除外。在下面两种情况下，浏览器将显示包含在`noscript`中的内容：

1. 浏览器不支持脚本。
2. 浏览器对脚本的支持被禁用。
