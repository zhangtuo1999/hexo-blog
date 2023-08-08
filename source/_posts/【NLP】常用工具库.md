---
title: 【NLP】常用工具库
tags:
  - NLP
  - NER
keywords:
  - NLP
  - NER
description: NER论文源码中常见的几个工具库：argparse、logging、tqdm、seqeval
excerpt: NER论文源码中常见的几个工具库：argparse、logging、tqdm、seqeval
abbrlink: f9e0efab
date: 2023-01-13 09:14:56
index_img: ../images/封面图/py.png
---

# 概述

总结 NER 代码中常用的工具库如下：

1. argparse 参数解析 https://docs.python.org/3/library/argparse.html
2. logging 日志记录 https://docs.python.org/3/library/logging.html
3. tqdm 显示进度条 https://tqdm.github.io/
4. seqeval 序列标注评估 https://github.com/chakki-works/seqeval

## 1. argparse 参数解析

>argparse 模块使编写用户友好的命令行界面变得容易。程序定义了它需要的参数，argparse 将确定如何从sys.argv 中解析这些参数。argparse 模块还自动生成帮助和用法消息。当用户给程序提供无效参数时，模块也会发出错误。

### 1.1 使用场景

常用 argparse 模块来定义 batch_size 和 lr 等超参数。这样就可以在不改变代码的前提下，通过 shell 命令来指定不同的超参数。

### 1.2 用法

使用 argparse 的步骤：

1. 导包 —— `import argparse`
2. 创建解析器对象 —— `parser = argparse.ArgumentParser()`
3. 给解析器对象添加命令行参数 —— `parser.add_argument('--lr', type=float, default=1e-5)`
4. 解析命令行中的参数 —— `args = parser.parse_args()`
5. 调用参数 —— `lr = args.lr`

举例：

```python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument('--batch_size', type=int, default=32)
parser.add_argument('--lr', type=float, default=1e-5)

parser.add_argument('--use_crf', action='store_true')

args = parser.parse_args()

print(args.batch_size)
print(args.lr)
print(args.use_crf)
```

```shell
#!/bin/bash
python demo1.py \
    --batch_size 64 \
    --lr 2e-5 
```

## 2. logging 日志记录

>该模块定义了为应用程序和库实现灵活事件日志记录系统的函数和类。

### 2.1 使用场景

需要将训练模型过程中的一些关键信息记录下来，同时输出到控制台和文件中。

### 2.2 用法

使用 logging 的步骤：

1. 导包 —— `import logging`
2. 创建日志对象，设置默认输出级别
3. 为日志对象添加控制台处理器和文件处理器
4. 打印日志

举例：

```python
import logging 

logger = logging.getLogger(__name__)
logger.setLevel(logging.INFO)
logger.propagate = False

# 日志输出格式
formatter = logging.Formatter(
    fmt="%(asctime)s - %(levelname)s - %(name)s -   %(message)s", 
    datefmt='%Y/%m/%d %H:%M:%S')

# 添加控制台日志处理器
console_handler = logging.StreamHandler()
console_handler.setLevel(logging.INFO)
console_handler.setFormatter(formatter)
logger.addHandler(console_handler)

# 添加文件日志处理器
file_handler = logging.FileHandler('./demo.log', mode="a", encoding="utf-8")
file_handler.setLevel(logging.INFO)
file_handler.setFormatter(formatter)
logger.addHandler(file_handler)

# 打印不同级别的日志
logger.info('epoch 0, loss: 0.1')
```

## 3. tqdm 显示进度条

>立即让循环显示智能进度表-只需用 tqdm(iterable) 包装任何可迭代的，就完成了！

### 3.1 使用场景

训练模型时，显示训练进度。

### 3.2 用法

1. 安装 tqdm 库，并导包
2. 使用 `tqdm()` 包装迭代器

举例：

```python
from tqdm import tqdm, trange
import time

epoch = 50

for i in tqdm(range(epoch)):
    time.sleep(0.25)

# trange(x) 是 tqdm(range(x)) 的简写
for i in trange(epoch):
    time.sleep(0.25)
```

## 4. seqeval 序列标注评估

### 4.1 使用场景

评估模型的预测效果。

### 4.2 用法

1. 安装 seqeval 库，并导包
2. 传入真实标签和预测标签

举例：

```python
from seqeval.metrics import accuracy_score, precision_score, recall_score, f1_score
from seqeval.metrics import classification_report


y_true = [['O', 'O', 'O', 'B-MISC', 'I-MISC', 'I-MISC', 'O'], ['B-PER', 'I-PER', 'O']]
y_pred = [['O', 'O', 'B-MISC', 'I-MISC', 'I-MISC', 'I-MISC', 'O'], ['B-PER', 'I-PER', 'O']]

print(accuracy_score(y_true, y_pred))
print(precision_score(y_true, y_pred))
print(recall_score(y_true, y_pred))
print(f1_score(y_true, y_pred))


print(classification_report(y_true, y_pred))
```

