---
title: 【Shell】Shell基本语法
tags: Shell
keywords: Shell
description: Shell基本语法
excerpt: Shell基本语法
abbrlink: 3fa62252
date: 2022-11-19 10:02:04
---

## 1 Shell 概述

Shell是一个命令行解释器，用于接收应用程序/用户的命名，然后调用操作系统的内核。

查看当前可用的Shell解释器：

```shell
cat /etc/shells
```

查看当前使用的Shell解释器：

```shell
echo $SHELL
```

## 2 Hello World

### 2.0 创建一个脚本

```shell
vim helloworld.sh
```

### 2.1 指定解析器

Shell 脚本的开头必须指定 Shell 脚本的解析器。格式为：

```shell
#!/bin/bash
```

或者

```shell
#!/bin/sh
```

### 2.2 添加命令

添加一行输出"Hello World"命令后保存退出：

```shell
#!/bin/bash
echo "Hello World"
```

### 2.3 执行命令

```shell
sh ./helloworld.sh
```

或者

```shell
bash ./helloworld.sh
```

或者

```shell
./helloworld.sh
```

这种方法执行脚本，必须有脚本的可执行权限。默认情况下，创建脚本是没有可执行权限的，此时可以使用 chmod 赋予权限：

```shell
chmod +x ./helloworld.sh
```

或者

```shell
source ./helloworld.sh
```

或者

```shell
. helloworld.sh
```

这里的`.`是一个命令。

## 3 变量

### 3.1 环境变量

分为系统环境变量和用户环境变量。常见的系统变量：`$HOME`、`$PWD`、`$SHELL`、`$USER`

或者分为全局环境变量和局部环境变量。局部变量只对当前的shell有效，全局变量对当前shell的子shell同样有效。

查看当前所有环境变量：

```shell
env 
```

或者：

```shell
printenv
```

查看某个环境变量：

```shell
printenv HOME
```

### 3.2 自定义变量

格式：`变量名=变量值`，等号的前后不能存在空格，变量值如果存在空格，需要使用
