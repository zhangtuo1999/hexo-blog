---
title: 文献阅读03-基于改进TextRank的关键词抽取算法
tags:
  - 毕设
  - 文献
  - 关键词提取
excerpt: 《基于改进TextRank的关键词抽取算法》
abbrlink: bd3bc374
date: 2021-04-08 19:49:08
---

# 基于改进TextRank的关键词抽取算法_张莉婧

# 网址

[论文地址](https://tsg.buct.edu.cn/https/77726476706e69737468656265737421fbf952d2243e635930068cb8/KCMS/detail/detail.aspx?dbname=cjfd2016&filename=byxb201604013&dbcode=cjfq)（需要北化校园网）

## 论文思路与笔记

### 一、问题的描述

* 关键词提取的概念：关键词抽取就是从一篇文档中抽取出能表达文章主题的词组

* 关键词提取的分类：有监督的和无监督

* TFIDF算法基本思想：若某个词或短语在一篇文章中出现的次数较高( 即 TF 的值较高) ，但在其他文章中很少出现，我们就认为该词或者短语具有很好的分类能力，将其称为该篇文章的关键词。
* TFIDF算法的缺点：TFIDF 算法没有涉及语义间的关系和词语本身的特性。

* TFIDF算法的改进：
  * 徐振强等通过引入词语的分布均衡度和首次出现位置等特征，提出了一种改进的 TF－IDF 算法。其改进后的算法虽然有了一定的提高，但这种效果只有在提取长篇文章关键词时才表现得明显，对于提取短篇文章的关键词效果并不明显。
  * 张雯将TFIDF 算法与基于语义关系的TextRank算法相结合，提出了 TFIDF -TextRank 算法，但该方法在提高准确率的同时增加了时间的消耗。

* TextRank算法，TextRank算法的不足
* TextRank算法的改进：
  * 罗庆平在原有的 TextRank算法中加入了词语位置权重，既考虑了语义间的关系，又考虑了词语本身的位置，显著提高了关键词的准确率。但其单独考虑词语位置的权重不够全面，人为确定的权重也不够准确。
  * 本文将 TFIDF 算法、词语位置、词语长度以及词性 4 种属性结合，采用 G1 赋权法进行以上属性的权重计算，将得到的“综合权重”与 TextRank算法结合，对 TextRank算法进行了改进。即：TextRank-CM算法

### 二、数学模型

#### TextRank算法

* TextRank 算法是一种基于图模型的关键词抽取算法。该算法的核心是衡量图中每个点的全局权重。而权重又通过连接这个点与其他点的边决定。与此同时，全局权重越大的点，与其连接的边的效力也越高。
* TextRank算法的公式、调节系数

#### TextRank-CM 算法

* 将词语本身的属性及最经典的 TFIDF 算法引入TextRank 算法中，并通过 G1 赋权法对各属性赋予不同的权重

### 三、算法描述

### 四、实验分析

* 数据集：知网论文100篇

* 结论：准确率、召回率和F值均有所提升，时间开销更大

### 五、结论

* 针对中文关键词抽取准确度的问题，提出了TFIDF -TextRank  算法。该算法通过 G1 赋权法对词语的属性及TFIDF 值的权重进行了优化，并将其作 为 TextRank算 法 的 初 始 权 重，对 原 始 的TextRank算法进行了改进。TFIDF -TextRank 算法在提高关键词抽取准确率的同时增加了时间的消耗。所以，下一步将研究如何缩短时间。