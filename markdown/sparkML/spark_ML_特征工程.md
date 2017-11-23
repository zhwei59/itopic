## 分类
- Extraction。从Raw数据中提取特征
- Transformation。
- Selection。
- LSH算法。
http://www.jianshu.com/p/e662daa8970a

## Feature Extractors


##### TF-IDF (HashingTF and IDF)
TF-IDF是文本特征生成的常用方法，sparkml中将TF-IDF分成两个步骤，

TF:`HashingTF` 是一个`Transformer`将分词后的item转化成固定长度的特征向量，TF通过降维过程中计算 `hashing trick`来实现求和

IDF:`IDF` 是一个`Estimator`，用来`fits`数据并生成`IDFModel`,`IDFModel`提取出特征，并缩放每一列。通常是将常出现的列进行权重下调。

##### Word2Vec

##### CountVectorizer
`CountVecotrizer`和`CountVectorizerModel`的目标是将词袋转化成`Token`计数的向量，


## Feature Transformers

#### Tokenizer
分词器。将文本分成词例。
`RegexTokenizer`按照正则表达式来分词。
#### StopWordsRemover
将分词器生成的词，进行停用词处理。
#### N-gram
NGram类可以把特征转化成N-Gram特征。
##### Binarizer
`Binarizer`将离散的特征按照阈值二分化。
##### PCA
PCA是一个统计过程，他使用正交变换将一组可能相关的变量的观察值转换成一组称为主成分的不相关的变量的值，PCA类训练模型使用PCA将特征向量映射到低维空间。

##### 多项式展开

##### 离散余弦变换（DCT）

#### StringIndexer

用于one-hot编码前，用来索引，为字符串编号。
#### OneHotEncoder
独热编码
### VectorIndexer
对向量的类型，进行索引转化，他接受一个最大的类别数的参数，将类别映射到索引上。

#### Normalizer
把生成feature向量进行，l2或者lmax正则化

#### StandardScaler
标准化。将每个特征标准化为0标准差或者0均值
#### MinMaxScaler
最大最小缩放，默认最小值为0，最大值为1
#### Bucketizer
特征桶分割。
#### ElementwiseProduct
加权
#### VectorAssembler
特征组合
#### QuantileDiscretizer
分位点离散化。按照分位点来离散化。


## Feature Selectors

## Locality Sensitive Hashing



