---
layout:         post
title:          "Note - Mining Opinion Features in Customer Reviews"
subtitle:       "从顾客评论中提取aspect（传统方法）"
date:           2018-10-26
author:         "William"
header-img:     "img/post-bg-bird.jpg"
catalog:        true
tags:
    - NLP
    - Paper Notes
    - Aspect Extraction
typora-root-url: ..
---



## Paper Core

论文的核心思想是，aspect词都是名词，情感词（opinion word）都是形容词，并且aspect词都是具体在句子中出现过的，

所以论文方法的核心前提是词性标注。对句子进行一定的预处理后，对句子进行词性标注，然后通过关联规则挖掘算法和侧面词过滤等方法来提取aspect词以及情感词。



## Model

>  论文的模型流程图如下图所示。
>
> 在论文中，feature = aspect

![1540520290725](/img/in-post/mining-opinion-feature/model.png)

论文提取的aspect是具体出现在句子当中的，而不是从整个数据集中提取或者归纳出某个商品的特定aspect信息，所以论文提出的方法不需要特定的领域知识，由模型自动提取。

### Part-of-Speech Tagging(POS)——词性标注

论文中提取的aspect都是*名词*。

并且论文只关注那些在句子中出现的名词或者名词短语。所以需要用词性标注来对句子中词的词性进行标注。

### Frequent Features Generation——关联规则挖掘

论文在这一步中提取比较“热门”的aspect词，即出现次数较多的。

论文借鉴了商品推荐算法中的关联规则挖掘算法，进而判断哪些词才是aspect词。在关联规则挖掘算法中，项目集（itemset）被定义为是一组在句子中共同出现的词或者短语，而在推荐算法中，项目集通常是销售的商品集合。

论文中在此步骤中，主要用到了Apriori算法，并且假设所有aspect词组的单词个数都不大于3。

### Feature Pruning——过滤aspect

由关联规则挖掘算法得到的aspect词或词组，并不是所有都符合客观实际的，所以在此步中，要对Apriori算法得到的aspect进行过滤。

- 主要用到的过滤方法有两种：
  1. 紧凑度过滤；
  2. 冗余过滤；

- 紧凑度过滤

  - 动机：由于关联规则算法不考虑词的位置，则关联规则算法生成的aspect词组有些是没有意义的乱序词组，需要去掉。
  - 具体方法：aspect词组在句子中，每个词的间距不大于3，则称该词组是紧凑的。如果该词组出现在多个句子中，如果在至少两个句子中是紧凑的，则说明该词组是合理的，否则，就过滤掉。

- 冗余过滤

  - 动机：有些只有一词的aspect，最重复出现在其它aspect词组中，而这些单个词的aspect在意思上会与词组中的意思有一定重复，所以需要去掉这些单个词的aspect。

  - 方法：通过$p-support$值来去掉，当某个aspect词或者词组的$p-support$值低于特定的阈值时（论文中设置为3），则把该aspect过滤掉。

    > $p-support$的定义：记$ftr$为aspect词组，$p-support$则为$ftr$是名词时在所有的句子中出现的次数，并且在这些句子中，不包含任何$ftr$的父集合，即句子中不再包含任何$ftr$的aspect词组。
    >
    > 例子：有一个aspect是manual，并且在数据集中出现过10次；manual在整个数据集中是manual mode和manual setting的子集。假设manual mode和manual setting在数据集中的出现次数分别是4次和3次，并且这两个词组没有同时出现在任何一个句子中以及在句子中的词性都是名词。则manual的p-support值为3。
    >
    > note：要保证apsect的词性是名词，是因为aspect都假设为了名词。

### Opinion Words Extraction——提取情感词

- 动机

  情感词一般都出现在aspect词的附近，并且假设情感词都是形容词。

- 具体方法

  得到上一步过滤完的aspect词后，在这些aspect词的附近找形容词。

### Infrequent Feature Identification——提取“冷门”的aspect词

在这一步中，旨在提取出用户不经常提到的aspect信息，这一部分信息同样十分重要。

- 动机

  人通常用同一个形容词来形容不同的方面，所以可以用上一步得到的形容词来发现不那么“热门”的aspect

- 方法

  从上一步得到的形容词中，进一步寻找这些形容词在句子中修饰的名词，把这些名词也作为aspect


## Something can learn

1. 借鉴了推荐算法中的思想，寻找用户经常提到的aspect信息；
2. 论文利用提取好的aspect去提取形容词作为情感词，然后再用情感词去提取出现频率较低的aspect，这样一来一回能较完整地提取aspect。
3. 提供了一种可以提取aspect词组的方法，很多基于深度学习的方法只能提取单个词的aspect



## Shortcomings

对aspect和情感词的假设过于单一，论文认为aspect都是名词，情感词都是形容词，这样提取得到的aspect信息很难符合客观事实，这样提取得到的aspect也比较单一，不全面。但是这个提取任务对于部分任务也可以起到一定的促进作用，比如说在利用用户评论在弱监督深度网络的应用问题中[2]，虽然提取aspect的方法比较简单，但对[2]中的模型来说方法已经足够。



## Reference

*Hu, M., & Liu, B. (2004). Mining opinion features in customer reviews. American Association for Artificial Intelligence, (July 2004), 755–760. https://doi.org/10.1145/1014052.1014073*

[2]*Zhao, W., Guan, Z., Chen, L., He, X., Cai, D., Wang, B., & Wang, Q. (2018). Weakly-supervised Deep Embedding for Product Review Sentiment Analysis. IEEE Transactions on Knowledge and Data Engineering, 30(1), 185–197. https://doi.org/10.1109/TKDE.2017.2756658*