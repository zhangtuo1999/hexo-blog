---
title: 【CSS】CSS基础笔记
tags:
  - 前端
  - CSS
excerpt: 学习 CSS 时所记录的要点
abbrlink: 8074a0ef
date: 2021-04-25 23:53:45
---

# CSS基础笔记

# 概念

* Cascading Style Sheets 层叠样式表

* 层叠：多个样式作用在同一个HTML元素上，同时生效

# 优点

* 功能强大
* 将内容展示和样式控制分离
  * 降低耦合度
  * 让分工更加容易
  * 提高效率
    

# 样式

* 内联样式。只在当前标签生效。在标签内使用style属性指定css代码

  ```html
  <div style="color:red"> hello css</div>
  ```

* 内部样式。只在当前页面生效。在`<head>`标签中定义`<style>`标签，`<style>`标签的标签体就是css代码

    ```html
    <head>
        <style>
            div{
                color:blue;
            }
        </style>
     </head>
    ```

    ```html
    <div> hello css </div>
    ```

* 外部样式。在所有引入css资源文件的页面有效。在`<head>`标签内，定义`<link>`标签，引入外部的资源文件。

    ```html
    <head>
     	<link rel="stylesheet" href="./css/demo.css"> 
    </head>
    ```
    
    或者,
    ```html
    <style>
      	@import "css/demo.css"
    </style>
    ```

# 格式

```
选择器 {
	属性名1:属性值1;
	属性名2:属性值2;
}
```

* 选择器：筛选具有相同特征的元素
* 注意：每一对属性都要用`;`隔开，最后一对可以不用

## 选择器

### 基础选择器

#### id选择器

* 选择具体的id属性值，建议在一个html页面中id值唯一

* 语法：

  ```css
  #id属性值{
  
  }
  ```
#### 元素选择器

* 选择具有相同标签名称的元素

* 语法：

  ```css
  标签名称{
  
  }
  ```

* 注意：id选择器的优先级高于元素选择器

#### 类选择器

* 选择具有相同class属性值的元素

* 语法：

  ```css
  .class属性值{
  
  }
  ```

* 优先级：id选择器>类选择器>元素选择器

### 扩展选择器

* 选择所有元素

  * 语法：`*{}`

* 并集选择器

  * 语法：`选择器1,选择器2{}`

* 子选择器

  * 语法：`选择器1 选择器2{}`

* 父选择器

  * 语法：`选择器1>选择器2{}`

* 属性选择器

  * 语法：元素名称[属性名="属性值"]

    ```css
    input[type="text"]{
    	boder: 5px;
    }
    ```

    ```html
    <input type="text">
    ```

* 伪类选择器

  * 语法：`元素:状态{}`

    ```css
    /*超链接初始化状态：*/
    a:link{
    	color:pink;
    }
    /*鼠标悬浮状态：*/
    a:hover{
        color:green;
    }
    /*正在访问状态：*/
    a:active{
        color:blue;
    }
    /*访问过状态：*/
    a:visited{
    color:red;
    }
    
    ```

## 属性

### 字体、文本

* `font-size`：字体大小
* `color`：文本颜色
* `text-algin`：对其方式
* `line-height`：行高

### 背景

* `background`：设置背景，复合属性

### 边框

* `border`：设置边框，复合属性

### 尺寸

* `width`：宽度
* `height`：高度

### 盒子模型

* 用于控制布局
* `margin`：外边距
* `padding`：内边距
* 内边距和外边距是相对的，内边距会影响整个盒子的大小
* `box-sizing:border-box`：设置盒子的属性，让`width`和`height`就是最终盒子的大小

* `float`：浮动
  *  `float:left`：左浮动
  * `float:right`：右浮动