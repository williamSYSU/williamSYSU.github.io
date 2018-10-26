---
layout:         post
title:          "Note - An Unsupervised Neural Attention Model for Aspect Extraction"
subtitle:       "用神经网络无监督学习的方法提取aspect信息"
data:           2018-10-24
author:         "William"
header-img:     "img/post-bg-sentiment-analysis.jpg"
catalog:        true
tags:
    - NLP
    - Paper Notes
    - Aspect Extraction
typora-root-url: ..
---

## Before Reading

1. 如何进行无监督？

   > 通过用aspect词向量来重构句子特征表示的思想，得到一组aspect的词向量，每个具体的aspect词在词向量空间中找与这组aspect词向量中最近的词作为aspect，aspect的类型数为预定义好的，每个类型的具体名称由提取出来的对应类型的一系列词人为总结得出。

2. attention机制如何使用？

   > 与传统的attention机制类似，为句子中的每个词向量分配权重，最后得到句子的特征表示，在得到每个词向量的权重时，运用了一个转换矩阵来表示每个词向量与全局上下文信息的关系。



## Paper Core

论文提出的模型主要用于提取句子的aspect信息，其核心思想是，通过attention机制得到一个句子的特征表示，然后用预定义好的多个aspect组成的矩阵来重构这个句子的特征，最后训练得到一个aspect矩阵，通过该矩阵，在词向量空间中找与其最近的几个词向量对应的词，即找到了句子的aspect信息。



## Model

> ABAE model structure

![1540379863785](/img/in-post/attn-asp-extract/model.png)

> 模型中用到的公式

$$
z_s=\sum_{i=i}^{n}a_ie_{w_i}\\
a_i=\frac{exp(d_i)}{\sum_{j=1}^nexp(d_j)}\\
d_i=e_{w_i}^\top · M · y_s\\
y_s=\frac{1}{n} \sum_{i=1}^{n} e_{w_i}\\
$$



- 模型的主要步骤：

  1. 用attention机制将非侧面词的词向量权重降低，得到一个句子的表示。

  2. 将句子的词向量重构为侧面词词向量的线性组合，侧面词的词向量来自侧面词词向量矩阵T。

- 目的：

  上述两个步骤（降维和重构），是为了将attention生成的句子表示用尽量少的变换得到句子的重构，保留了aspect的大部分信息。

- 模型中的attention机制：
  1. 首先将句子中的所有词向量求和求平均，表示一个句子的特征；
  2. 然后为句子中的每个词分配对应的权重，表示其有多大可能是这个句子的侧面词。
  3. 分配权重时，考虑两个因素：
     - 用变换矩阵来描述句子中的词与K个侧面词的关系；
     - 用过滤词与全局上下文信息的内积来描述过滤词与句子的关系

- 模型流程：

  1. 将句子中的词转换成词向量的表示形式；

  2. 词向量输入attention机制中，得到输入句子的特征表示，attention详细流程如下：

     a）对句子中的所有词向量求均值，表示句子的全局上下文信息；

     b）将词向量、转换矩阵和上下文信息矩阵相乘，得到每个词的表示（一个值）；

     c）最后将所有词的表示通过一个softmax，得到每个词的概率；

     d）最后得到一个词向量的加权求和表示，即一个句子的特征表示。

  3. 将句子的特征表示输入一个全连接层，并通过一个softmax，得到一个权值向量；

  4. 将权值向量与aspect矩阵相乘，得到句子的重构特征表示，最后求loss。

- 关于aspect矩阵$T$：

  在模型训练过程中，矩阵$T$为全局共享且可被训练，其aspect的数量预先定义，在本论文中数量定为14，每个aspect对应一组具体的aspect词，具体的aspect词通过在词向量空间中寻找最邻近的词得到。

- 评估提取的aspect：
  1. 输入一个句子，模型首先分配一个推断的标签，该标签是重构时权重最高的对应aspect；
  2. 然后判断选取的aspect是否是对应类别数据的aspect，如果是，则说明预测准确。

## Something can learn

1. 通过重构的思想，把一个由attention机制得到的句子特征表示转换为用aspect矩阵线性组合表示的句子特征，这样在训练的过程中不需要标注数据，也可以把句子中的aspect信息提取出来。
2. 模型训练得到的aspect矩阵不代表一个具体的词，而是进而在其词向量空间中用邻近的词来表示aspect，这样的灵活性更大。



## Shortcomings

1. 模型训练得到的aspect信息，只能是单个词，而不能是短语结构；
2. 根据每个类别训练得到的几组aspect有可能出现不相关的情况，每组的aspect需要人为归纳总结；
3. 是否可以在提取完aspect矩阵后对句子的具体aspect选取提取一个解决方法？



## Reference

*He, R., Lee, W. S., Ng, H. T., & Dahlmeier, D. (2017). An Unsupervised Neural Attention Model for Aspect Extraction. Proceedings of the 55th Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers), 388–397. https://doi.org/10.18653/v1/P17-1036*