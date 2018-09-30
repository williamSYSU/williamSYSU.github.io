# 复现Attention-based LSTM for Aspect-level Sentiment Classification



## 数据集

- 训练数据集：[SemEval 2014 Task 4](http://alt.qcri.org/semeval2014/)
- 初始化词向量数据集：[Glove (Pennington et al. 2014)](http://nlp.stanford.edu/projects/glove/)



## 疑惑

1. 验证集上的准确率很高，而测试集的准确率很低。其性质上不是一样的吗？
2. rest_train测试rest_test的结果要比all_train测试rest_test的结果要好，因为rest_train本身数据量也挺多，其有相关性。但是lap_train测试lap_test的结果要比all_train测试lap_test的结果差，因为lap_train本身的训练数据较少，很难拟合模型。
3. 出现2的原因，其中一个是验证集不只是那个类别，是混合类别的，所以去验证集最高，不一定能使对应类别的测试集准确率较高
4. rest的训练集需要lr_decay，因为数据量相对较大，但是lap的不需要lr_decay，因为数据较少



## 论文存在的问题

1. attention最后

