---
layout:         post
title:          "Torchtext 使用"
subtitle:       "简化NLP数据预处理工作"
data:           2018-09-19
author:         "William"
header-img:     "img/post-bg-tech.jpg"
tags:
    - NLP
    - How to
---

## Catalog

- [简介](#简介)
- [Field类](#field类)
  - [Field类包含的参数](#field类包含的参数)
  - [Field类的重要成员函数](#field类的重要成员函数)
  - [示例](#示例)
- [Dataset类](#dataset类)
  - [示例](#示例-1)
- [Iterator类](#iterator类)
  - [Iterator参数](#iterator参数)
  - [示例](#示例-2)



简介
----

torchtext包含三个组件：

-   Field：主要包含数据预处理的配置信息，比如指定分词方法、是否转成小写、起始结束字符的设置等操作。一个Field对应训练数据中的一类数据，在NLP的情感分析中，Field可以对应句子、Aspect信息、Label信息等。Field能够对指定类型的数据自动构建字典，包含每个词的索引信息。
-   Dataset：继承自Pytorch的Dataset，用于加载数据、把数据划分成训练集、验证集以及测试集等操作。最典型的Dataset类是TabularDataset，可以指定数据文件路径、数据文件类型、以及给训练数据中的不同种类数据指定Field。其中在Dataset中指定的Field名称，后面直接作为Dataset的属性之一，取数据的时候按如下格式：*dataset.Text*（假设在Dataset中的Field如下定义：fields
    = \[(‘Text’), TEXT\]。
-   Iterator：作为Dataset的数据迭代器，即从Dataset中取数据。可以定义Batch
    size。

Field类
-------

包含写文本处理的通用参数的设置，同时还包含一个词典对象，可以把文本数据表示成数字类型，进而可以把文本表示成需要的tensor类型。

### Field类包含的参数

-   *sequential*: 是否把数据表示成序列，如果是False, 不能使用分词
    默认值: True.

> 当sequential设置为True时，会把数据分词，然后把每个词序列化，返回序列化的结果；
>
> 如果设置为False时，则不进行分词，把整个数据存入字典，返回它在字典里面的索引。
>
> *备注*：在Aspect-level的情感分析中，一个Aspect信息可能由多个词组成，如*battery
> life*，则可以设置sequential为False，这样会把整个*battery
> life*变成索引。

-   *use\_vocab*: 是否使用词典对象. 如果是False
    数据的类型必须已经是数值类型. 默认值: True.
-   *init\_token*: 每一条数据的起始字符 默认值: None.
-   *eos\_token*: 每条数据的结尾字符 默认值: None.
-   *fix\_length*: 修改每条数据的长度为该值，不够的用pad\_token补全.
    默认值: None.
-   *tensor\_type*: 把数据转换成的tensor类型 默认值: torch.LongTensor.
-   *preprocessing*: 在分词之后和数值化之前使用的管道 默认值: None.
-   *postprocessing*: 数值化之后和转化成tensor之前使用的管道默认值:
    None.
-   *lower*: 是否把数据转化为小写 默认值: False.
-   *tokenize*: 分词函数. 默认值: str.split.
-   *include\_lengths*:
    是否返回一个已经补全的最小batch的元组和和一个包含每条数据长度的列表
    . 默认值: False.
-   *batch\_first*: Whether to produce tensors with the batch dimension
    first. 默认值: False.
-   *pad\_token*: 用于补全的字符. 默认值: "&lt;pad&gt;".
-   *unk\_token*: 不存在词典里的字符. 默认值: "&lt;unk&gt;".
-   *pad\_first*: 是否补全第一个字符. 默认值: False.

### Field类的重要成员函数

-   *pad (minibatch)*: 在一个batch对齐每条数据
-   *build\_vocab (Dataset, \*args)*:
    建立词典，建立词典时可以用别人预训练的数据进行初始化，参数是*vectors*
-   *numericalize ()*: 把文本数据数值化，返回tensor

### 示例

``` python
def tokenizer(text):
    return [tok.text for tok in spacy_en.tokenizer(text)]

TEXT = data.Field(
    sequential=True,
    tokenize=tokenizer,
    lower=True,
    batch_first=True,
)
ASPECT = data.Field(
    sequential=False,
    lower=True,
    batch_first=True
)
LABEL = data.Field(
    sequential=False,
    use_vocab=False,
    batch_first=True
)
```

Dataset类
---------

torchtext的Dataset是继承自pytorch的Dataset，提供了一个可以下载压缩数据并解压的方法（支持.zip,
.gz, .tgz）。

*Dataset.splits ()* 方法可以同时读取训练集，验证集，测试集。

TabularDataset支持CSV, TSV, or JSON格式的文件（仅此三种）。

> CSV (Comma-separated values):
> 每行表示一条训练数据（包括句子和Label等信息），不同的数据用‘,’（逗号）分隔开。
>
> TSV (Tab-separated values):
> 每行表示一条训练数据（包括句子和Label等信息），不同的数据用‘\t’（Tab制表符）分隔开。

### 示例

-   构建Dataset

``` python
train, val, test = data.TabularDataset.splits(
    path='./data/', 
    train='train.tsv',
    validation='val.tsv', 
    test='test.tsv', 
    format='tsv',
    fields=[
        ('Text', TEXT),     # Text后面直接作为一个属性可以调用
        ('Label', LABEL)
    ]
)
```

当只需要训练集时，不用调用*splits ()*方法。

``` python
train = data.TabularDataset(
    path='data/train.tsv',
    format='tsv',
    fields=[
        ('Text', TEXT),
        ('Label', LABEL)
    ]
)
```

-   构建词典。在使用数据前，必须建立对应Field的词典。

``` python
# 使用vectors参数可以加载预训练的词向量。
# 默认的预训练数据在.vector_cache/文件夹中，可以自己事先下载后对应数据，解压后放到该# 文件夹中，vectors参数中把文件后缀名去掉。

TEXT.build_vocab(train, vectors="glove.6B.100d")
```

Iterator类
----------

Iterator是torchtext到模型的输出，它提供了我们对数据的一般处理方式，比如打乱，排序，等等，可以*动态修改*batch大小，这里也有splits方法，可以同时输出训练集，验证集，测试集。

### Iterator参数

-   dataset: 加载的数据集
-   batch\_size: Batch 大小.
-   batch\_size\_fn: 产生动态的batch大小 的函数
-   sort\_key: 排序的key
-   train: 是否是一个训练集
-   repeat: 是否在不同epoch中重复迭代
-   shuffle: 是否打乱数据
-   sort: 是否对数据进行排序
-   sort\_within\_batch: batch内部是否排序
-   device: 建立batch的设备 -1:CPU ；0,1 ...：对应的GPU

### 示例

``` python
train_iter, val_iter, test_iter = data.Iterator.splits(
    (train, val, test), 
    sort_key=lambda x: len(x.Text),
    batch_sizes=(32, 256, 256), 
    device=-1
)
```

当只需要训练数据时，这里也不需要调用*splits ()*方法。

``` python
train_iter = data.Iterator(
    train,
    sort_key=lambda x: len(x.Text),
    batch_size=32,
    device=-1
)
```
