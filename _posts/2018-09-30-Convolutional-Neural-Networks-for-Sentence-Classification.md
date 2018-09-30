---
layout:         post
title:          "Note - Convolutional Neural Networks for Sentence Classification"
subtitle:       "CNN处理序列数据"
data:           2018-09-30
author:         "William"
header-img:     "img/post-bg-sentiment-analysis.jpg"
tags:
    - NLP
    - Paper Notes
    - Sentiment Analysis
    - CNN
---

Catalog
-------

{:toc}



Before Reading
--------------

1.  CNN如何处理句子序列？

    > 句子序列变成词向量序列，在几个词的词向量之间做卷积。并且卷积核有不同的大小以及不同的权重。通过卷积得到不同大小的feature
    > map，进行最大池化后，得到最终的句子表示。



Paper Core
----------

论文参考图像处理的方法，将一个句子的词向量拼成一个大矩阵，用卷积核在这个矩阵上做卷积以及最大池化，提取句子的特征。卷积核的高度为词向量的大小，宽度可变，即同时提取几个词的特征。



Model
-----

> 模型流程

![model](/img/in-post/conv-sen/model.png)



### 模型具体流程

1.  初始化句子中词的词向量，组成一个$n \times k$
    的矩阵，其中$n$为句子长度，$k$为词向量维度；
2.  取一个大小为$h \times k$的卷积核，其中$h$为卷积核窗口的宽度，即卷积$h$个词。经过卷积以及$tanh$的激活函数，得到一个feature
    map。卷积核可以有很多个，包含不同的权重，以及不同的窗口大小；
3.  在feature map上做最大池化，得到每个feature
    map的一个最大值，最后得到由全部feature map组成的句子表示；
4.  最后经过全连接以及$Softmax$进行分类。



### 卷积操作的优势

1.  得到一个定长的输出。不同的大小的卷积核得到的feature
    map虽然大小不一样，但是做最大池化后，变成一个值，不同的feature
    map都做最大池化后，得到一个定长的一维向量表示。
2.  降低输出的维度。通过卷积以及最大池化，可以在降低维度时并保留充分的特征。



### 卷积操作的缺点

忽略了句子的上下文信息。卷积操作发生在句子的局部，丢失了句子的远距离上下文信息。



Something can learn
-------------------

1.  如何在处理序列信息中运用CNN操作？

    > CNN操作的对象是矩阵，其实质是同时考虑了几个元素的特征。
    >
    > 处理序列信息时，可以有两种CNN操作：
    >
    > - 将词序列转换成词向量序列，在词向量拼成的矩阵上进行卷积；
    > - 通过某种方式计算句子中每两个词之间的相关性，得到相关性矩阵，在该矩阵上进行卷积；

2. 如何得到一个定长的句子表示？

   >  除了LSTM，CNN虽然中间过程得到不同大小的feature
   > map，但是最后经过最大池化后，都能得到一个定场的句子表示。



Reference
---------

*Kim, Yoon. 2014. “Convolutional Neural Networks for Sentence
Classification.” https://doi.org/10.3115/v1/D14-1181.*
