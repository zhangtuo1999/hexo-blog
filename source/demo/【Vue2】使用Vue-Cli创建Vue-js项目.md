---
title: 【Vue2】使用 Vue CLI 创建 Vue 2.x 项目
tags:
  - 前端
  - Vue
  - Vue CLI
excerpt: 图文讲解使用 Vue CLI 5.x 创建 Vue 2.x 项目
typora-root-url: ..
abbrlink: b5e9e615
date: 2022-03-20 10:39:56
---

## 什么是 Vue CLI

* Vue CLI 是一个基于 Vue.js 进行快速开发的完整系统。

* Vue CLI 致力于将 Vue 生态中的工具基础标准化。它确保了各种构建工具能够基于智能的默认配置即可平稳衔接，这样你可以专注在撰写应用上，而不必花好几天去纠结配置的问题。

## 使用 Vue CLI 创建 Vue.js 项目

* 本文以使用 **Vue CLI 5.x** 创建 **Vue 2.x** 项目为例。

### 1. 准备好 Node 和 npm

>Vue CLI 4.x 需要 [Node.js](https://nodejs.org/) v8.9 或更高版本 (推荐 v10 以上)。

关于 Node 环境的安装这里不赘述。Node 安装包下载地址：https://nodejs.org/zh-cn/download/

#### 1.1 检查 Node 版本：

```bash
node -v
```

![image-20220320105312316](/images/使用Vue-Cli创建Vue-js项目/image-20220320105312316.png)

#### 1.2 检查 npm 版本：

```bash
npm -v
```

![image-20220320105753440](/images/使用Vue-Cli创建Vue-js项目/image-20220320105753440.png)

### 2. 安装 Vue CLI

```bash
npm install -g @vue/cli
```

![image-20220320123229526](/images/使用Vue-Cli创建Vue-js项目/image-20220320123229526.png)

检查 Vue CLI 版本

```bash
vue --version
```

![image-20220320110324394](/images/使用Vue-Cli创建Vue-js项目/image-20220320110324394.png)

### 3. 创建 Vue 2.x 项目

#### 3.1 创建项目文件夹

在要创建项目的文件夹下：

```bash
vue create 你的项目名
```

这里以创建一个名为 study-vuex 的项目为例：

```bash
vue create study-vuex
```

#### 3.2 选择配置方式

![image-20220320124837270](/images/使用Vue-Cli创建Vue-js项目/image-20220320124837270.png)

这里选择第三个选项，手动选择特性（主要是因为不想使用默认的 eslint ）。点击回车后如下图。

#### 3.3 手动选择配置

![image-20220320125049414](/images/使用Vue-Cli创建Vue-js项目/image-20220320125049414.png)

此时取消选择 Linter / Formatter 。移动方向键，当指针指向 Linter / Formatter 这个选项时，点击空格取消选择。如下图所示。

![image-20220320125354588](/images/使用Vue-Cli创建Vue-js项目/image-20220320125354588.png)

此时点击回车进入下一步。

#### 3.4 选择 Vue 版本

![image-20220320125506806](/images/使用Vue-Cli创建Vue-js项目/image-20220320125506806.png)

移动箭头，选择 2.x 版本的 Vue。点击回车进入下一步。

#### 3.5 选择配置文件位置

![image-20220320125646346](/images/使用Vue-Cli创建Vue-js项目/image-20220320125646346.png)

这句话的意思是：“你想把你的配置文件放在一个独立的配置文件中？还是放到 package.json 中？”

我们选择第一个选项：放在独立的配置文件中。回车进入下一步。

#### 3.6 选择是否保留预设

![image-20220320125842343](/images/使用Vue-Cli创建Vue-js项目/image-20220320125842343.png)

输入 N 后回车。

#### 3.7 自动安装项目所需要的包

![image-20220320130316489](/images/使用Vue-Cli创建Vue-js项目/image-20220320130316489.png)

等待几秒后：

![image-20220320130400219](/images/使用Vue-Cli创建Vue-js项目/image-20220320130400219.png)

此时，Vue 2.x 项目已经成功创建。

## 运行 Vue 项目

进入项目文件夹，并执行npm run serve 即可运行 Vue 项目。

```bash
cd 项目名
npm run serve
```

在本例中：

```bash
cd study-vuex
npm run serve
```

![image-20220320130717384](/images/使用Vue-Cli创建Vue-js项目/image-20220320130717384.png)

此时，Vue 项目已成功启动。

浏览器中输入：http://localhost:8080/ 

![image-20220320130830541](/images/使用Vue-Cli创建Vue-js项目/image-20220320130830541.png)

## 参考

* https://cli.vuejs.org/zh/
