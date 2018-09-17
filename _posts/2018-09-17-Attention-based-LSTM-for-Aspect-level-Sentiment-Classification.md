---
layout:         post
title:          "Note - Attention-based LSTM for Aspect-level Sentiment Classification"
subtitle:       "如何把侧面词(Aspect)与Attention机制结合，拓展基础LSTM？"
data:           2018-09-17
author:         "William"
header-img:     "img/post-bg-sentiment-analysis.jpg"
tags:
    - NLP
    - Paper Notes
    - Sentiment Analysis
---



## Catalog

1. [Before Reading](#before-reading)
2. [Paper core](#paper-core)
3. [Model](#model)
   1. [LSTM with Aspect Embedding (AE-LSTM)](#lstm-with-aspect-embedding-(ae-lstm))
   2. [Attention-based LSTM (AT-LSTM)](#attention-based-lstm-(at-lstm))
   3. [Attention-based LSTM with Aspect Embedding (ATAE-LSTM)](#attention-based-lstm-with-aspect-embedding-(atae-lstm))
4. [Something can learn](#something-can-learn)
5. [Reference](#reference)

## Before Reading

1. Attention机制如何加入LSTM？

   > Attention机制在论文的模型中利用侧面词和输入词之间的关系，生成一个权重以及一个带劝求和的句子级表示特征。

2. 相比于原生的LSTM有什么优势？

   > 论文介绍了三种拓展LSTM的模型，分别是LSTM with Aspect Embedding (AE-LSTM)，Attention-based LSTM (AT-LSTM)，Attention-based LSTM with Aspect Embedding (ATAE-LSTM)。
   >
   > ATAE-LSTM相比于原生的LSTM的优势：
   >
   > - 考虑了侧面词的词向量；
   > - 考虑了侧面词与句子词之间的关系，关注句子词中根据有决定性因素的词；
   > - 原生的LSTM仅用隐层的最后一个输出作为句子的表示，而本论文对一个句子的表示加入了Attention生成的带权重的句子表示，将其加入句子在结合侧面词后在原生LSTM的隐层输出中。

3. Aspect的信息如何加入LSTM？

   > 侧面词的信息在两个地方加入了LSTM：
   >
   > - 直接将侧面词的词向量与输入词的词向量拼接，输入LSTM中得到每个词的隐层输出；
   > - 侧面词的词向量与输入词的隐层输出拼接后，计算attention的权重，最后计算得到一个带权求和的句子表示；

4. 在Attention机制中，隐层输出和侧面词所做的全连接各自的权重参数是否分别共享？如侧面词都共享一个参数，而隐层输出都共享另外一个参数。



## Paper Core

论文提出把侧面词用词向量来表示，在基础LSTM上结合了侧面词的词向量信息，以及加入了Attention机制，使得模型能够把侧面词以及输入词中的关键词（Attention权重较高的词）的信息编码到最后的句子表示中。



## Model

论文对LSTM拓展了三个模型。



### LSTM with Aspect Embedding (AE-LSTM)

在LSTM中结合了侧面词，并同时将侧面词的embedding加进去。



### Attention-based LSTM (AT-LSTM)

加入Attention机制的LSTM，具体流程：

1. 句子有序传入LSTM，得到每个词的隐层输出；
2. 每个词的隐层输出经过一个全连接层，与侧面词的词向量经过一个全连接层的输出拼接（拼接N次）后，经过激活层；
3. 将步骤2得到的矩阵再经过一个全连接，以及一个Softmax，得到每个词的权重向量；
4. 将权重向量与原LSTM隐层输出相乘，得到一个带权重的句子表示；
5. 将带权重的句子表示经过一个全连接，和原LSTM最后一个隐层输出经过全连接的输出做一个拼接，然后激活输出；
6. 将步骤5的输出再经过一个全连接后做一个Softmax，输出最后的结果。

> 加入了Attention机制的LSTM

![image1](/img/in-post/ATAE-LSTM/image1.jpg)



### Attention-based LSTM with Aspect Embedding (ATAE-LSTM)

在AT-LSTM的基础上，输入原生LSTM之前，将输入词的词向量与侧面词的词向量拼接后再输入LSTM得到隐层输出，其它部分与AT-LSTM一致。

> ATAE-LSTM

![image2](/img/in-post/ATAE-LSTM/image1.jpg)



## Something can learn

1. 如何在模型中结合侧面词的信息？

   > 论文提出了两种方式：
   >
   > 1. 侧面词的词向量直接和输入词的词向量进行拼接，然后输入原生LSTM中得到带侧面词信息的隐层输出；
   > 2. 在Attention机制中，根据侧面词和输出词之间的关系，计算Attention的权重以及带权求和的句子表示，使句子具有Attention信息（输入中的哪些词对侧面词起决定性作用）

2. 如何使用Attention机制？

   > Attention机制其实就是寻找输入词中对输出词或者侧面词起着决定性作用的词，计算其权重，最后根据权重来计算最终的句子表示特征。



## Reference

*Y. Wang, M. Huang, L. Zhao, and X. Zhu, “Attention-based LSTM for Aspect-level Sentiment Classification,” Proc. 2016 Conf. Empir. Methods Nat. Lang. Process., pp. 606–615, 2016.*