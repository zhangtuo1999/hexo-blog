---
title: 【Vue2】Vue基础笔记
tags:
  - 前端
  - Vue.js
excerpt: 学习 Vue 2.x 时所记录的要点
abbrlink: 65ab80b7
date: 2021-06-09 16:12:42
index_img: ../images/封面图/vue2.jpg
---

# Vue笔记

### Hello World

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Vue</title>
</head>
<body>
<div id="app">
    <span>{{msg}}</span>
</div>
</body>
</html>
<script src="js/vue.js"></script>//引入Vue
<script>
    const app = new Vue({
        el: "#app",     
        data: {        
            msg: 'Hello World',
            name: 'zhangtuo',
            age: 21,
            code:'<h1>标题</h1> 正文 <br> ',
            imgSrc:'https://cn.vuejs.org/images/logo.svg',
            school: {s1: '清华', s2: '北大', s3: '北化'},
            colleges: [
               	{id: 0, s1: '清华', s2: '北大', s3: '北化'},
                {id: 1, s1: '清华', s2: '北大', s3: '北化'},
                {id: 2, s1: '清华', s2: '北大', s3: '北化'},
            ]
        },
        methods: {
            // es5写法
            fun1:function () {
                this.age++;//this 对象指的是vue实例对象本身
                console.log("年龄加1");
            },
            // es6写法 （简化）
            fun2() {
                this.age--;
                if (this.age < 1)
                    this.age = 1;
                console.log("年龄减1");
            },
        }
    });
</script>
```

`el:"#app"`:

* el即element 
* 作用：指定Vue实例的作用范围。在作用范围内直接使用`{{数据名}}`（插值表达式）可以取到属性值
*  可以使用类选择器，但是推荐使用id选择器。注意：不要把作用域指向body或html

`data:{msg:'Hello World'}`:

* 作用：给Vue实例对象绑定一系列数据

`methods: {fun(){}}`:

* 作用：事件处理函数等

## 插值表达式

* 格式：`{{数据名}}` 

  例：

  ```html
  <span>{{msg}}</span>
  ```

* 作用：把Vue实例中的数据展示到网页上

* 使用`{{数据名}}`时可以进行算数运算和逻辑运算，也可以使用js方法。例：`{{age+12}}`或`{{age===12}}`或`{{msg.toLowerCase()}}`

## v-text和v-html

* 格式：`v-text="数据名"` 

  例：

```html
<span v-text="name"> 你好</sapn>
<span v-html="code"> html代码片段</span>
```

* 作用：`v-text`和 `v-html` 都可以根据属性名获取`data`中的数据，并且都会覆盖原标签中的数据
* `{{   }}`和v-text的区别：
  * `v-text`会覆盖掉标签中原来的内容
  * `v-text`可以避免**插值闪烁**
  * **插值闪烁**：网络差时，使用插值表达式`{{   }}`时，加载会有延迟，网页上会显示插值表达式的代码。

* `v-html`和`v-text`的区别：
  * `v-text`把数据内容当作文本直接展示到页面上，相当于js中的innerText
  * `v-htm`l把数据内容当作html代码，解析html标签后渲染到对应标签内部，相当于js中的innerHtml

## v-on和@

* 格式：`v-on:事件名="函数(参数列表)"`    简化写法：`@事件名`

  例：

```html
<button v-on:click="fun1">年龄加1</button>
注释：函数无参数时不用加()
<button @click="fun1">年龄加1</button>
```

* 作用：给对应标签绑定事件
* js事件三要素：
  * 事件源：发生事件的源头，指html中各种标签
  * 事件：发生的特定动作，指的是单击、双击等动作
  * 事件处理函数（监听器）：事件触发时的响应

* vue的事件处理函数统一声明在vue实例的methods属性中

## 双向绑定

* vue实例中data数据值变化时，页面上的值同步发生变化

* 页面上的值发生变化时，vue实例中data数据值同步变化

## v-bind和:

* 格式：`v-bind:属性名=属性值` 属性名是css的属性名，属性值是Vue实例中data里的数据名        简化写法  `:属性名`

  例:

  ```html
  <img v-bind:src="imgSrc" alt="logo">
  <img :src="imgSrc" alt="logo">
  注：data:{imgSrc:'https://cn.vuejs.org/images/logo.svg'}
  ```

* 作用：绑定html标签的属性

## v-model

* 格式：`v-model="数据名"`

  例：

  ```html
  <input type="text" v-model="name">
  ```

* 作用：绑定form表单标签中的value属性

## v-if和v-show

* 格式：`v-if="布尔值"`或`v-show="布尔值"`

  例：

  ```html
  <div v-if="isShow">{{msg}}</div>
  <div v-show="isShow">{{msg}}</div>
  ```

* 作用：控制页面中的标签展示或隐藏
* v-if和v-show的区别：
  * v-if：底层操作DOM元素，添加或删除DOM控制标签显示或隐藏
  * v-show：底层控制CSS样式，控制标签是否展示。（推荐使用情况：数据量大，显示和隐藏切换频繁）

## v-for

* 格式：`v-for="(value,key,index) in items"`

  例：

  ```html
  1.遍历对象属性
  <div v-for="(value,key,index) in school">
      {{value}}    {{key}}   {{index}}</div>
  2.遍历数组
  <div v-for="(college,index) in colleges">
  {{index}}: {{college.s1}} {{college.s2}} {{college.s3}}
  </div>
  ```

* 作用：用来遍历vue实例的数据

* 注意：最好给v-for绑定一个唯一的key，确保正确渲染数据

  例：

  ```html
  <div v-for="(college,index) in colleges" :key="college.id">
  {{index}}: {{college.s1}} {{college.s2}} {{college.s3}}
  </div>
  ```