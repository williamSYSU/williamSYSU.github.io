---
layout:         post
title:          "Note - Attention is All You Need"
subtitle:       "Attention的魅力"
date:           2018-10-09
author:         "William"
header-img:     "img/post-bg-allyou.jpg"
catalog:        true
tags:
    - NLP
    - Paper Notes
typora-root-url: ..
---

## Before Reading

1. 论文提出了几种attention？

2. 论文的attention与传统的attention有什么区别？

3. Q, K, V如何确定？

   > - QA问题
   >
   >   Q：输入文本，K：问题，V：答案
   >
   > - self-attention
   >
   >   Q=K=V：输入句子的词向量，即句子中的每个词都要和该句子中的所有词进行attention计算，学习句子内部的依赖关系。
   >
   > - 机器翻译
   >
   >   K=V：源语言的encoder输出，Q：目标语言的隐层状态

4. 蒙版（Mask）如何使用？有什么用？

   > 蒙版只在解码器（Decoder）中使用。为了防止解码器中的信息流向左流动而导致保留了后续词的自回归信息，即只允许当前词的预测依赖于前面的词，而不依赖后续的词。需要在输入softmax之前将不合法的连接用蒙版去掉，将对应的值设置为负无穷。
   >
   > 自回归信息：用同一变量之前各期的表现情况，来预测该变量本期的表现情况。因为是根据以往的表现来预测自己，所以叫做自回归。



## Paper Core



## Model



## Something can learn



## Reference