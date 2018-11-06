---
layout:         post
title:          "Note - Multi-grained Attention Network for Aspect-Level Sentiment Classification"
subtitle:       “多粒度的Attention机制"
date:           2018-11-3
author:         "William"
header-img:     "img/post-bg-sentiment-analysis.jpg"
catalog:        true
tags:
    - NLP
    - Paper Notes
    - Sentiment Analysis
typora-root-url: ..
---

## Before Reading

1. 多粒度（Multi-grained）指的是什么？

   > 对于IAN（Interactive Attention Network）[2]中的双向Attention机制，是比较粗糙了，把上下文或者aspect当成一个整体来进行相互考虑，而忽略了他们内在的关系。本文多粒度的Attention机制，在IAN模型的基础上，细化了Attention的相互作用范围，考虑了每个aspect词和上下文词的关系，即称多粒度的Attention。

2. 如何考虑具有相同上下文信息的词之间的关系？

   > 论文中考虑了在具有相同上下文信息（在同一个句子中）而具有不同的情感倾向的aspect，对其加以区分。论文通过在损失函数中加入一个alignment loss，使得这些aspect会被模型考虑，并且对具有不同情感倾向的aspect加以区分。



## Paper Core

论文借鉴了IAN网络中双向Attention的思路，在此基础上，加入了细粒度的Attention机制，进一步考虑了句子中的每个词与每个侧面词之间的关系，利用这种关系得到细粒度的双向Attention表示，最后进行分类。

论文的算法中，其核心是句子中的词与侧面词的相似度矩阵，通过这个矩阵，可以得到句子中的词与侧面词逐个词之间的关系，如到底句子中的词到底与哪个侧面词更相关，侧面词在情感倾向上如何影响以及影响了哪些句子中的词。

- 论文动机

  论文中的细粒度双向Attention机制，其最终目的是为了得到句子（Context）的表示以及侧面词（Aspect）的表示，这两个表示都可以用对方的信息来表示出来。

  > 如在F-Aspect2Context层中，其本质是利用Context word与Aspect word之间的关系，融合了Aspect的信息，最终得到一个Context的表示。
  >
  > 如在F-Context2Aspect层中，其本质是利用Aspect word与Context word之间的关系，融合了Context的信息，最终得到一个Aspect的表示。
  >
  > 上面两层得到的表示都在一个维度上，可以很好地融入模型中。



## Model

> Multi-grained attention network（MGAN）模型流程图

![model structure](/img/in-post/multi-grained-attention/model.png)



### Input Embedding Layer（模型输入）

在模型图中，Context即表示句子序列，Aspect即表示侧面词序列。句子和aspect词的词向量都用GloVe的预训练词向量进行初始化。



### Contextual Layer（特征提取）

句子序列和Aspect序列分别通过两个不同的Bi-LSTM，得到Bi-LSTM的隐层输出。

其中对于句子序列来说，在得到隐层输出后，再通过一个Location Encoding，目的是为了着重考虑句子中越靠近Aspect的词。论文中认为，距离Aspect越近的词，其权重应该越大，所以把位置信息转换成了权重信息。把Aspect看成一个整体（用一个替换符替换掉整个Aspect序列），设句子中第$t$个词与Aspect的距离为$l$，句子序列长度为$N$，Aspect序列长度为$M$，则可以计算得到第$t$个词的权重，然后用权重与Bi-LSTM的隐层输出相乘，得到句子的最终隐层输出：
$$
w_t = 1 = \frac{l}{N-M+1} \\
H = [H_1 * w_1, \dots ,H_N * w_N]
$$


### Multi-grained Attention Layer（多粒度的Attention）

#### Coarse-grained Attention（粗粒度的Attention）

论文在粗粒度的Attention部分，主要借鉴了IAN模型的双向Attention机制，如下是IAN模型的流程图：

![image-20181106112937713](/img/in-post/multi-grained-attention/ian-model.png)

论文中认为，IAN模型的双向Attention是比较粗糙的，因为它把句子的隐层输出或者Aspect的隐层输出直接求和取均值，这样会丢失一部分信息，而没有考虑他们逐个词内在的关系。

论文中借鉴了IAN模型的方法，把双向Attention加入了模型中，分为两部分：

1. C-Aspect2Context（Aspect对Context的Attention）
   $$
   s_{ca}(Q_{avg},H_{i}) = Q_{avg}*W_{ca}*H_{i} \\
   a_{i}^{ca} = \frac{exp(s_{ca}(Q_{avg},H_{i}))}{\sum_{k=1}^{N}exp(s_{ca}(Q_{avg},H_{k}))} \\
   m^{ca} = \sum_{i=1}^{N}a_{i}^{ca} \cdot H_i
   $$


2. C-Context2Aspect（Context对Aspect的Attention）
   $$
   s_{cc}(H_{avg},Q_{i}) = H_{avg} * W_{cc} * Q_{i} \\
   a_{i}^{cc} = \frac{exp(s_{cc}(H_{avg},Q_{i}))}{\sum_{k=1}^{M}exp(s_{cc}(H_{avg},Q_{k}))} \\
   m^{cc} = \sum_{i=1}^{M}a_{i}^{cc} \cdot Q_{i}
   $$




#### Fine-grained Attention（细粒度的Attention）

论文提出建立一个Context和Aspect的相似度矩阵，表示Context和Aspect之间的关系。相似度矩阵$U \in \mathbb{R}^{N*M}$，其中$U_{ij}$表示句子中第$i$个词和Aspect第$j$个词的相似度，其中$U_{ij}$的计算如下：
$$
U_{ij} = W_{u}([H_{i};Q_{j};H_{i}*Q_{j}])
$$
其中$;$表示拼接操作，$*$表示对应元素相乘。

细粒度中也包含了双向的Attention：

1. F-Aspect2Context（Aspect对Context的Attention）

   这部分用于寻找和每个Context word最相似的一个能决定情感倾向的Aspect word，流程是：

   a）在相似度矩阵$U$中的每一行，取一个与该Context word最相似的Aspect word；

   b）对每行进行Softmax，表示哪些词与Aspect更相关；

   c）最后将得到的权重与句子的隐层输出加权求和，得到最终的Attention表示。

   第一步选最大是看与Context word最相似的那个Aspect，如果那个值也很低，则这个词的权重也应该第一		   点

   - 计算公式如下：

$$
s_{i}^{fa} = max(U_{i,:}) \\
a_{i}^{fa} = \frac{exp(s_{i}^{fa})}{\sum_{k=1}^{N}exp(s_{k}^{fa})} \\
m^{fa} = \sum_{i=1}^{N}a_{i}^{fa} \cdot H_{i}
$$

2. F-Context2Aspect（Context对Aspect的Attention）

   这部分寻找哪些Aspect词语每个Context word最相关，流程如下：

   a）对相似度矩阵$U$的每一行进行Softmax，得到每个Aspect在一个Context word上的权重；

   b）对每个Context word，对所有的Aspect word进行加权求和，得到一个Context word关于所有Aspect word的加权求和表示；

   c）将每个Context word在b）中得到的表示求和求均值，得到该层Attention的表示。

   - 计算公式如下：

$$
a_{ij}^{fc} = \frac{exp(U_{ij})}{\sum_{k=1}^{M}exp(U_{ik})} \\
q_{i}^{fc} = \sum_{j=1}^{M}a_{ij}^{fc} \cdot Q_{j} \\
m^{fc} = Pooling([q_{1}^{fc}, \dots ,q_{N}^{fc}])
$$



### Output Layer（输出层）

最后将粗细粒度的所有Attention输出进行拼接，过一个全连接和Softmax，得到最终结果。



### Aspect Alignment Loss（针对Aspect的损失惩罚）

论文中认为，一些Aspect虽然具有相同的上下文信息（在同一个句子中），但是其情感倾向却是相反的，所以这些Aspect需要加以区分，论文中在交叉熵损失函数中加入了一个惩罚项。论文主要在C-Aspect2Context层中加入了这个惩罚项，惩罚项的计算公式为：
$$
d_{ij} = \sigma (W_{d}([Q_i;Q_j;Q_i * Q_j])) \\
\mathcal{L}_{align} = - \sum_{i=1}^{M-1} \sum_{j=i+1,y_i \neq y_j}^{M} \sum_{k=1}^{N} d_{ij} \cdot (a_{ik}^{ca} - a_{jk}^{ca})^2
$$
其中$\sigma$为$sigmoid$激活函数，最后模型的总损失函数为：
$$
\mathcal{L} = - \sum_{i=1}^{C} y_ilog(p_i) + \beta\mathcal{L_{align}}+\lambda||\Theta||^2
$$
其中最后的为正则项。



## Something can learn

1. 通过细粒度的相似度矩阵，可以改善原来的Attention机制，获得更好的效果。
2. 通过在损失函数中添加惩罚项，考虑更多的关于Aspect的实际情况。



## Reference

*Fan, F., Feng, Y., & Zhao, D. (2018). Multi-grained Attention Network for Aspect-Level Sentiment Classification. *ACL 2018*, 3433–3442.*

[2]*Ma, D., Li, S., Zhang, X., & Wang, H. (2017). Interactive Attention Networks for Aspect-Level Sentiment Classification. *IJCAI International Joint Conference on Artificial Intelligence*, 4068–4074. *

