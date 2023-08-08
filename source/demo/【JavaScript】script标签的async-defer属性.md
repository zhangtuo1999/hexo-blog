---
title: 【JavaScript】script标签的async/defer属性
tags: JavaScript
keywords: JavaScript
description: script标签中async/defer属性的作用？
excerpt: script标签中async/defer属性的作用？
abbrlink: 4cc74611
date: 2022-11-03 21:57:09
---

script标签中async/defer属性的作用？

浏览器默认**同步**解析HTML中的内容。当遇到外部脚本时需要进行对脚本下载，这会带来**阻塞**的问题。async和defer属性都是解决这个问题的，并且只针对对**外部脚本**生效。

```html
<script src='./a.js' async></script>
<script src='./b.js' defer></script>
```

如果标签使用了async属性，浏览器会立即下载脚本，但是不会阻塞页面的渲染。下载完成后具体什么时候执行这个脚本是**不确定**的。可能在浏览器没有完全加载完页面时执行，也可能在浏览器已经加载完页面后执行。因此，如果脚本需要对DOM进行修改的话，可能会出错（执行脚本时，页面还没有完全加载）。如果有两个使用了async属性的script标签，浏览器**不保证**执行顺序。

如果标签使用了defer属性，浏览器会应立即下载脚本，但是会在页面解析完成**后**再执行脚本。所以适合用于需要修改DOM的脚本。如果有两个使用了defer属性的script标签，浏览器会保证按顺序执行这两个脚本。
