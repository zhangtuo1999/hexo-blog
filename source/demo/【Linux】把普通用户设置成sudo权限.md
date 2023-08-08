---
title: 【Linux】把普通用户设置成sudo权限
tags: Linux
keywords: Linux
description: 把普通用户设置成sudo权限
excerpt: 把普通用户设置成sudo权限
abbrlink: d7e41c1c
date: 2022-11-19 10:56:05
---

普通用户设置为sudo权限后，可以使用sudo命令临时提权。

1. 切换到root用户。此步骤需要输入密码。

   ```shell
   su -
   ```

2. 编辑`/etc/sudoers`文件。

   ```shell
   vim /etc/sudoers
   ```

3. 在指定位置下面添加一行。

   ```
   ## Allow root to run any commands anywhere 
   root    ALL=(ALL)       ALL
   用户名    ALL=(ALL)       ALL
   ```

   如果想免密码执行sudo指令则添加：

   ```
   用户名    ALL=(ALL)       NOPASSWD:ALL
   ```

4. 使用`:wq!`退出并保存。
