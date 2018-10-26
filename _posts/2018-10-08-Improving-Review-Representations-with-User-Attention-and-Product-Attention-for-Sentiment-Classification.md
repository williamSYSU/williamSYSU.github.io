---
layout:         post
title:          "Note - Improving Review Representations with User Attention
and Product Attention for Sentiment Classification"
subtitle:       "分别考虑用户信息和商品信息的文档级情感分析"
data:           2018-10-08
author:         "William"
header-img:     "img/post-bg-sentiment-analysis.jpg"
catalog:        true
tags:
    - NLP
    - Paper Notes
typora-root-url: ..
---

## Before Reading

1. 如何把用户和商品的信息分开编码？每个部分的特征都包含了什么信息？

   > 分别用两个双向LSTM处理输入数据，在attention机制中分别结合不同的信息，最后用同一个label来进行有监督训练，得到同时含有用户和商品信息的文档表示。

2. 为什么分开编码能有较好的效果？针对不同的数据集？

   > 因为用户信息和商品信息两者的作用不同，对文档的情感倾向起着不同的影响作用，分开提取特征能够是两者的特征更明显，也更容易区分。



## Paper Core

论文基于文档级别的情感分析，构建一个网络，分别提取文档中的用户信息和商品信息，最后文档的特征表示同时包含了用户的信息和文档的信息，使得情感分析的效果更好。



## Model

> The architecture of Hierarchical User Attention and Product Attention neural network.

![1539056162684](/img/in-post/improve-user-product/model.jpg)



*模型流程*：

1. 把文档分成若干个句子，分别输入两个双向LSTM中，其中一个提取用户信息，另一个提取商品信息；
2. 通过双向LSTM得到每个句子的表示，将每个句子的表示再分别输入两个双向LSTM中，得到一个文档的表示；
3. 在两个支线的Attention机制中，提取用户信息的attention加入了用户的信息，通过将用户编码成一个连续的向量表示，在单词级别以及句子级别都加入了attention，最后得到一个含有用户信息的文档向量表示。提取商品直线的模型也以此类推，attention中加入和商品的信息（如某一个特定商品或者某一部电影，因为一个商品下面可能有很多用户评论）；
4. 将提取了用户信息和提取了商品信息的文档向量表示分别做softmax以及与同一个label比较并求loss，并且还将两个表示拼接后求得一个总的loss。



## Something can learn

1. attention机制可以作用在单词级别以及句子级别上，一个好的表示应该包含尽可能多的信息。
2. 用户信息以及商品信息在文档的情感分析中都有着不同的作用，分开提取可以让特征更突出，以及让模型学习到更多的东西。



## Reference

*Wu, Zhen, Xin-Yu Dai, Cunyan Yin, Shujian Huang, and Jiajun Chen. 2018. “Improving Review Representations with User Attention and Product Attention for Sentiment Classification.” AAAI. http://arxiv.org/abs/1801.07861.*