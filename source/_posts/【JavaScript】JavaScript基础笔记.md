---
title: 【JavaScript】JavaScript基础笔记
tags:
  - 前端
  - JavaScript
excerpt: ES；BOM；DOM
typora-root-url: ..
abbrlink: 3f7cd15d
date: 2021-05-01 19:35:58
---

# JavaScript基础笔记

## 概念

* js是一门客户端脚本语言

* 运行在客户端浏览器中，每个浏览器中都有js的解析引擎

* 不需要编译，直接被浏览器解析执行

## 功能

* 增强用户的html页面的交互过程，用来控制html元素，让页面有一些动态效果

## 发展

* JavaScript=ECMAScript+JavaScript特有的东西（BOM+DOM）

# ECMAScript

* 客户端脚本语言标准

## 基本语法

### 与html结合方式

1. 内部js。

   ```html
   <script>
       alert("hello world")
   </script>
   ```

2. 外部js。

   ```html
   <script src="js/a.js"></script>
   ```

* 可以写在html的任意位置，但是先后顺序有区别
* `<script>`标签可以定义多个

### 注释

1. 单行注释：`//注释`
2. 多行注释：`/*注释*/`

### 数据类型

1. 原始数据类型（基本数据类型）
   * `number`：数字。 整数/小数/NaN

   * `string`：字符串。字符/字符串。单引号，双引号都可

   * `boolean`：true和false

   * `null`：一个对象为空的占位符

   * `undefined`：未定义。如果一个变量没有初始化值，则会被默认赋值为undefined
2. 引用数据类型（对象）
   
   * `object`

### 变量

* 一块存储数据的内存空间
* java是强类型的语言，js是弱类型的语言
* 语法：`var 变量名 = 初始化值;`
* 查看变量的类型：`typeof(变量名)`

### 运算符

1. 一元运算符：++ ，-- ，+

   * 在js中，如果运算数不是运算符要求的类型，那么js会自动将运算数进行类型转化

   其它类型转number：

   * string转number。按字面值进行转换，如果字符串的字面值不是数字，则转为NaN

   * boolean转number。true转成1，false转成0

2. 算数运算符：+，-，*

3. 比较运算符：>，<，>=，===（全等于）

   比较方式

   * 类型相同，直接比较
     * 字符串：按照字典顺序比较

   * 类型不同，先进行类型转换

   * ===全等于。在比较之前判断类型，如果类型不同，返回false

4. 逻辑运算符：&&，||，！

   其它类型转boolean：

   * number：0或NaN是false，其余为true

   * string：空串是都是false，其余为true

   * null或undefined为fasle

   * object：都是true

   ```javascript
   if(obj){
       //防止空指针异常
   	alert("hello world")
   }
   ```

5. 三元运算符：？ ：

### 流程控制语句

1. if else
2. switch
   1. java中可以接受的数据类型：byte int char string emun  ......
   2. js中什类型都可以接受
3. while
4. do while
5. for

### js特殊语法

1. 语句以;结尾，如果一行只有一条语句可以省略
2. 变量的定义使用var关键字，也可以不使用
   * 用：定义变量是局部变量
   * 不用：定义的变量是全局变量

### 对象

#### Function：函数对象

1. 创建

   1. 格式一（不常用）

      ```javascript
      var fun =new Function(形参列表，方法体)；//不常用
      ```

      如：

      ```javascript
      var fun = new Function("a","b","alert(a+b);")
      ```

   2. 格式二

      ```javascript
      function fun(形参列表){
      	方法体
      }
      ```

      如：

      ```javascript
      function fun(a,b){
      	alert(a+b);
      }
      ```

   3. 格式三

      ```javascript
      var fun = function(a,b){
      	alert(a+b);
      }
      ```

2. 方法

3. 属性

   `length`：代表形参的个数 

4. 特点

   * 方法定义时，形参的类型不用写，返回值的类型也不用写

   * 方法名称相同时，后面的方法会覆盖前面的方法

   * 在js中，方法的调用只与方法的名称有关，和参数的列表无关

   * 在方法声明中有一个隐藏的内置对象（数组）：`arguments`，封装所有的实际参数

5. 调用

   格式：

   ```javascript
   fun(形参列表)
   ```

   如：

   ```javascript
   fun(a,b)
   ```


#### Array：数组对象

1. 创建

   ```javascript
   var arr = new Array(元素列表)
   ```

   或：

   ```javascript
   var arr = new Array(默认长度) 
   ```

   或：

   ```javascript
   var arr = [元素列表]
   ```

2. 方法

   * `join(参数)`：将数组的元素按指定的参数拼接成字符串，默认按`,`分割

   * `push(值)`：向array尾部添加一个元素

3. 特点

   * js中，数组元素的类型是可变的

   * js中，数组的长度是可变的

#### Date：日期对象

1. 创建

   ```javascript
   var date=new Date();
   ```

2. 方法

   * `toLocalString()`：改成本地时间格式

   * `getTime()`：获取毫秒值，返回当前时间和1970.1.1,00:00之间的差距

#### Math：数学对象

1. 创建

   不用创建直接使用。Math.方法名()

2. 方法

   * `random()`：返回一个0-1之间的随机数，含头不含尾

   * `ceil(x)`：对数字向上取整

   * `floor(x)`：对数字向下取整

   * `round(x)`：对数字四舍五入为整数

3. 属性

   `PI`

#### RegExp：正则表达式对象

##### 定义字符串表达规则

1. 单个字符：`[]`

   ```
   [a] [ab](a或者b) [a-zA-Z0-9_](字母数字下划线)
   ```

   特殊符号：

   * `\d`:单个数字字符[0-9]

   * `\w`:单个单词字符[a-zA-Z0-9_]

2. 量词符号：

   * `?`：表示出现0次或1次

   * `*`：表示出现0次或多次

   * `+`：表示出现1次或多次

   * `{m,n}`：表示出现次数大于等于m小于等于n

     m缺省表示最多n次，n缺省表示最少m次

3. 开始结束符号：`/^  表达式  $/`

##### 正则对象

1. 创建

   ```javascript
   var reg = new RegExp("正则表达式");
   ```

   或：

   ```javascript
   var reg = /正则表达式/;
   ```

2. 方法
   test(字符串)：验证字符串是否符合正则表达式的规范

   ```javascript
   var flag = reg.test("zhangtuo")
   ```

#### Globle：全局对象

1. 特点

   在这个Globle对象中封装方法不需要对象就可以直接调用。`方法名()`

2. 方法

   * `encodeURI`：url编码

   * `decodeURI`：url解码

   * `encodeURIComponent`：url编码，编码的字符更多

   * `decodeURIComponent`：url解码

   * `parseInt()`：将字符串转为数字。之一判断字符是否是数字，知道不是为止，将前面的数字转为number。如果第一个字符不是数字，转为NaN

# DOM简单学习

1. 功能：控制html文档的内容

2. 代码：获取页面标签（元素）的对象

   `document.getElementById(id值)`：通过元素的id获取元素对象

3. 操作Element对象：

   * 修改属性值：
     1. 明确获取的对象是哪一个
     2. 查看api文档，找到哪些属性可以修改

   * 修改标签体中的内容：

     `innerHTML`方法

# 事件简单学习

1. 功能：某些组件执行了某些操作之后，触发某些代码执行

2. 如何绑定事件

   1. 直接在html标签上，指定事件的属性，属性值是js代码

      ```html
      <img id="light" src="1.gif" onclick="alert("我被点击了");">
      ```

      或：

      ```html
      <script>
          function fun(){
              alert("我被点击了");
          }
      </script>
      
      <img id="light" src="1.gif" onclick="fun();">
      ```

   2. 通过js获取元素对象，指定事件属性，并设置一个函数

      ```html
      <img id="light" src="1.gif">
      <script>
      	function fun(){
              alert("我被点击了");
          }
      	var light=document.getElementById("light");
          light.onclick=fun;
      </script>
      ```

# BOM

### 概念

* Brower Object Model 浏览器对象模型
* 将浏览器的各个部分封装成对象 

### 组成

#### Window：窗口对象

1. 创建（获取）

2. 方法

   与弹出框有关的方法

   * `alert()`：显示一段消息和一个确认按钮的警告框

   * `conform()`：显示一段消息和确认按钮和取消按钮的对话框

     点击确定按钮返回true，点击取消按钮返回false

   * `promote()`：显示可提示用户输入的对话框

     返回值就是用户输入的值

   与打开关闭有关的方法

   * `open()`：打开一个新窗口
   * `close()`：关闭当前窗口，谁调用就关掉谁

   与定时器有关的方法

   * `setTimeout()`：一次性定时器

     参数

     1. js代码
     2. 毫秒值

     反返回值

     * 定时器的编号，用于取消定时器

     ```javascript
     var id = setTimeout("alert("boom")",3000);
     ```

     或：

     ```javascript
     var id = settimeout(fun,3000);
     function fun(){
     	alert("boom");
     }
     ```

   * `clearTimeout()`：取消一次性定时器

     ```javascript
     clearTimeout(id);
     ```

   * `setInterval()`：循环定时器

   * `clearInterval()`：取消循环定时器

3. 属性

   * 获取其他BOM对象

   `window.history`或`history`

   * 获取DOM对象

   `window.document`或`document`

4. 特点

   * Window对象不需要创建，可以直接使用。`window.方法名()`或`window.属性名`

   * window也可以省略，直接用`方法名()`或`属性名`

#### Navigation：浏览器对象（不常用）

#### Screen：显示器屏幕对象（不常用）

#### History：历史记录对象

当前页面窗口访问过的页面历史记录

1. 创建（获取）：

   `window.history`或`history`

2. 方法：

   * `back()`：

   * `forward()`：

   * `go`：

3. 属性：

   `length`：返回当前窗口历史列表中的url数量

#### Location：地址栏对象

1. 创建（获取）：

   `window.location`或`location`

2. 方法：

   `reload()`：刷新页面

3. 属性：

   `href`：当前地址，可以设置href属性为新地址，用来跳转到新地址

# DOM

* Document Object Model 文档对象模型
* 通过 HTML DOM，JavaScript 能够访问和改变 HTML 文档的所有元素。

* HTML DOM 模型被结构化为对象树：

  ![U727ed90323e34510805d1d87f00eb17cP](/images/JavaScript基础笔记/U727ed90323e34510805d1d87f00eb17cP.jpg)

* W3C DOM 标准被分为 3 个不同的部分：

  * Core DOM - 所有文档类型的标准模型

    * `Document`：文档对象
    * `Element`：元素对象
    * `Attribute`：属性对象

    * `Text`：文本对象
    * `Comment`：注释对象
    * `Node`：节点对象，是其它五种对象的父对象

  * XML DOM - XML 文档的标准模型

  * HTML DOM - HTML 文档的标准模型

## 核心DOM模型

### Document：文档对象

1. 创建（获取）：在html dom模型中可以使用window对象获取

   `window.document`或`document`

2. 方法：
   * 获取Element对象：
     * `getElementById()`：根据id属性值获取元素对象。id属性值一般唯一
     * `getElementsByTagName()`：根据元素名称获取元素对象们，返回的是一个数组
     * `getElementsByClassName()`：根据class属性值获取元素对象们，返回的是一个数组
     * `getElementsByName()`：根据name属性值获取元素对象们，返回的是一个数组
   * 创建其它DOM对象：
     * `createAttribute(name)` ：创建新的属性对象
     * `createComment()` ：创建新的注释对象
     * `createElement()` ：创建新的元素对象
     * `createTextNode()`：创建新的文本对象

### Element：元素对象

1. 创建（获取）：

   通过document获取，见上面四种方法

2. 方法：
   * `setAttribute()`：设置属性
   * `removeAttribute()`：删除属性

### Node：节点对象

1. 特点：

   所有的dom对象都可以认为是一个节点

2. 方法：

   CRUD dom树

   * `appendChild()`：向节点的子节点列表的结尾添加新的子节点。
   * `removeChild()`：删除（并返回）当前节点的指定子节点。
   * `replaceChild()`：用新节点替换一个子节点。

3. 属性：

   `parentNode`：返回节点的父节点。

## HTML DOM

1. 标签体的设置和获取：`innerHTML`

2. 使用html元素对象的属性

3. 设置样式

   * 使用style属性

   ```html
   <div>使用style属性</div>
   
   <script>
   	div.style.border="1px solid red";
   	div.style.fonSize="20px";
   </script>
   ```

   * 提前定义好类选择器的样式，通过元素的className属性来设置其class的样式

## 事件

1. 功能：某些组件执行了某些操作之后，触发某些代码执行

2. 事件：某些操作，如：单击，双击，键盘按下，鼠标移动了
3. 事件源：组件，如：按钮，文本输入框
4. 监听器：代码
5. 注册监听：将事件，事件源，监听器结合在一起。当事件源上发生了某个事件，则触发某个监听器代码

6. 常见事件：
   * 点击事件
     * `onclick`：单击事件
     * `ondbclick`：双击事件
   * 焦点事件
     * `onblur`：失去焦点
     * `onfocus`：元素获得焦点
   * 加载事件
     * `onload`：一张图片或页面加载完成
   * 鼠标事件
     * `onmousedown`：鼠标按钮被按下
     * `onmouseover`：鼠标移动到某元素上
   * 键盘事件
     * `onkeydown`：某个键盘按键被按下
     * `onkeyup`：某个键盘按键被松开
     * `onkeypress`：某个键盘按键被按下并松开。
   * 选择和改变
     * `onchange`：内容被改变
     * `onselect`：文本被选中
   * 表单事件
     * `onsubmit`：确认按钮被点击。