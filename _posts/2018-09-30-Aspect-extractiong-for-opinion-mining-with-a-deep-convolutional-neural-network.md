---
layout:         post
title:          "Note - Aspect extraction for opinion mining with a deep convolutional neural network"
subtitle:       ""
data:           2018-09-30
author:         "William"
header-img:     "img/post-bg-sentiment-analysis.jpg"
catalog:        true
tags:
    - NLP
    - Paper Notes
    - Sentiment Analysis
    - CNN
    - Aspect-level
---


## Before Reading

1. 如何提取aspect信息？

   > 以窗口滑动的形式提取句子的局部特征，最后做传统的最大池化、全连接以及Softmax，达到分类的效果。

2. 如何判定一个句子中的词是不是侧面词（aspect word）？

   > 通过每个窗口的学习得到

3. CNN如何提取特征，以及提取了哪些特征？

   > 依然是才有最经典的CNN处理句子序列的方法，卷积核对几个词的全部词向量进行卷积操作，提取了句子的局部特征。

4. 论文提到CNN的网络为七层，具体如何叠加？

   > 论文的网络结构为七层：一个输入层、两个卷积层、两个最大池化层、一个全连接层、一个Softmax层




## Paper Core

采用复杂的CNN网络结构，提取Aspect词的上下文信息，进而判断一个句子中哪些词是Aspect，哪些词不是。CNN的的卷积操作采用直接对句子的词向量进行操作，目的是提取Aspect的局部上下文信息，以及其它句子的局部信息。



## Model

$$
input \to conv\ layer \to max\ pool\ layer 
\\\to conv\ layer \to max\ pool\ layer \to FC\ layer \to Softmax
$$



## Something can learn

1. 在一个文档中，首先通过一个多项分布找到一个潜在的侧面词，然后根据这个侧面词，具体的词就可以通过另一个多项分布来得到。这里的侧面词更像是一个类别或者一个方面，而具体的词则是根据这个类别确定的具体的词。
2. CNN可以提取句子的局部特征，通过CNN操作，可以提取侧面词的局部上下文信息，用以训练和提取句子中的侧面词。



## Reference

*Poria, Soujanya, Erik Cambria, and Alexander Gelbukh. 2016. “Aspect Extraction for Opinion Mining with a Deep Convolutional Neural Network.” Knowledge-Based Systems 108: 42–49. https://doi.org/10.1016/j.knosys.2016.06.009.*