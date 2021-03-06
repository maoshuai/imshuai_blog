---
title: deeplearning.ai深度学习笔记（Course5 Week2）：Natural Language Processing & Word Embeddings
date: '2018-07-29 09:48:12'
tags:
- deeplearning-ai-notes
- deep-learning
- ai
---

<script type="text/javascript" async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

本周从语言模型引入了词嵌入，并介绍了常用的词嵌入算法：Word2vec和GloVe；以及应用案例：情感分类和消除偏见。

<!--more-->


# 1- Introduction to Word Embeddings
## 1.1- Word Representation

1. One-hot向量的缺点

上周，我们已经通过one-hot向量表示过单词，它的缺点之一是：**每个one-hot只是为了表示单词自己而已，无法让算法很容易的跨单词泛化，即它无法表示任何两个单词之间的关系**，因为任何两个单词one-hot向量的内积为都是0。

举个例子，如果算法已经学习了下面第一句话是比较合理的一句话，我们把orange换成apple，让算法预测应该填什么单词，它并不容易得知。

> I want a glass of orange juice.
> I want a glass of apple ____.

顺便提一下，我们将用带下标的字母o代表one-hot向量，比如\\(o_{353}\\)，表示这一一个one-hot向量：它的第353个分量是1，其他都是0.

2. 特征化表示（Featurized representation）

与one-hot不同，我们将每个单词按照**多维特征**进行定义，比如下图所示，一共有4个属性（实际应用会更多）：性别、是否为皇室、年龄、是否为食品。每个单词分别从这4个属性给出与这些属性的相关度。**那么任何一个单词就可以用一个4维的特征向量表示**，比如Man表示为(-1, 0.01, 0.03, 0.09)。

![Xnip2018-07-24_19-31-52](https://cdn.imshuai.com/images/2018/07/word-embedding.jpg)

此时，可以清晰的看到Apple和Orange极为相似，上面的例子就很容易使得算法在第二句话也填入单词juice。

需要说明的是，上面的特征只是直观的举例，实际上的特征并不是手工设计的，而是算法（即word embedding）学习而来；而且这些学习的特征，**可能并不具有良好的解释性**，但不管怎样，算法都可以快速哪些单词是相似的。

当我们将单词使用这种高维特征表示时，就叫做**词嵌入（word embedding）**。之所以叫做embedding，可以想象成每个单词被嵌入（embed）到了一个高维空间内。词嵌入是NLP最重要的思想之一。

顺便提一下，我们用字母e加下标，标记一个单词的特征向量，比如one-hot为\\(o_{353}\\)的某个单词，表示为\\(e_{353}\\)。

3. 可视化word embedding

算法可以在高维空间学习出单词的特征，比如300维。而t-SNE是一种使用2维可视化词嵌入的方法，比如形成下图：

![Xnip2018-07-24_19-49-27](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-24_19-49-27.jpg)

让我们直观的感受到，词嵌入将一些概念相似的单词映射为了相似的特征向量。

## 1.2- Using word embeddings

本节将介绍如何将word embedding应用到NLP领域：迁移学习。

1. 举例

我们使用命名实体识别为例（即找出语句中的人名），比如有如有如下一句话：

> Sally Johnson is an orange farmer.

我们根据orange farmer可以判断出Sally Johnson是一个人名，我们的RNN模型，通过one-hot向量表示法，也学习到了这一点（1表示人名）：

![Xnip2018-07-24_20-12-36](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-24_20-12-36.jpg)

如果我们改用词嵌入法作为RNN的输入训练的模型，在遇到新的输入，比如：

> Robert Lin is an apple farmer.

也可以容易的识别Robert Lin是一个人名，因为词向量很容易判断apple和orange是类似的。

如果我们输入了冷僻的词汇呢？比如：

> Robert Lin is a durian cultivator.

durian和cultivator可能在我们的命名实体识别的训练集里样本很少，甚至干脆就没有。但一个好的词向量仍可以判断出durian是水果（类似orange和apple），而cultivator类似farmer；因此还是可以判断出Robert Lin是人名。

这是因为，虽然命名实体识别训练集里没有durian和cultivator，**但学习词嵌入的算法一般会参考海量文本，可以从网上寻找几亿甚至几百亿的无标签文本数据，作为自己的训练集**。词嵌入的训练集中总会有大量durian和cultivator，并会发现durian和Orange类似，而cultivator和farmer类似，并把这些知识**迁移到命名实体识别的任务的学习中**，即便后者只有少量的训练集，也可以识别durian和cultivator。

2. 词嵌入做迁移学习的步骤

* 使用大型文本框训练词嵌入，通常需要1-100个billion的单词。当然，可以用已经训练好的词嵌入。
* 将词嵌入迁移到新的任务上，新的任务通常是很小的训练集，比如100k个单词。
* 可选：使用新的数据持续优化词嵌入。

当任务的训练集相对较小时，词嵌入效果很明显，因此广泛应用于NLP领域；但词嵌入在语言模型、机器翻译领域应用的较少，因为这些领域有大量的数据。

3. 词嵌入和人脸编码（face encoding）

之前CNN的课程，我们学习了Siamese做人脸编码，然后用人脸编码判断两张图片的人脸相似性。

![Xnip2018-07-24_20-49-20](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-24_20-49-20.jpg)

词嵌入很像之前我们学习的face encoding，这里的术语embedding和encoding的含义上是类似的，但两者的差别是词嵌入面对的词汇是有限的，而人脸几乎是无限的。
（我的理解：为什么可以embedding？因为词汇是有限的。但为什么人脸也可以encoding，虽然人脸是无限的，但人脸的基本特征是有限的，在这一点上两者是相通的。某种角度，可以将词嵌入看成浅层网络。）


## 1.3- Properties of word embeddings

词嵌入还可以帮助实现**类比推理（analogy reasoning）**。还是以之前的数据为例：


![Xnip2018-07-24_19-31-52](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-24_19-31-52.jpg)

比如我们提出一个问题：Man之于Woman，相当于King之于什么？我们很容易回答出是Queen。而词嵌入，可以实现这个类比推理过程。

我们发现：

$$e_{\text{man}} - e_{\text{woman}} = \begin{bmatrix}
-1 \\\\
0.01 \\\\
0.03 \\\\
0.09 \\\\
\end{bmatrix} - \begin{bmatrix}
1 \\\\
0.02 \\\\
0.02 \\\\
0.01 \\\\
\end{bmatrix} = \begin{bmatrix}
-2 \\\\
-0.01 \\\\
0.01 \\\\
0.08 \\\\
\end{bmatrix} \approx \begin{bmatrix}
-2 \\\\
0 \\\\
0 \\\\
0 \\\\
\end{bmatrix}
$$

$$e_{\text{king}} - e_{\text{queen}} = \begin{bmatrix}
-0.95 \\\\
0.93 \\\\
0.70 \\\\
0.02 \\\\
\end{bmatrix} - \begin{bmatrix}
0.97 \\\\
0.95 \\\\
0.69 \\\\
0.01 \\\\
\end{bmatrix} = \begin{bmatrix}
-1.92 \\\\
-0.02 \\\\
0.01 \\\\
0.01 \\\\
\end{bmatrix} \approx \begin{bmatrix}
-2 \\\\
0 \\\\
0 \\\\
0 \\\\
\end{bmatrix}
$$

即，man和woman的差异主要在gender，而king和queen的差异也主要在gender。

上述类比推理问题就可以转换为，使用一种算法找到一个词嵌入\\(e_?\\)使得：

$$e_{\text{man}} - e_{\text{woman}} \approx e_?$$

即找一个单词w，使得相似性函数sim取值最大：

$$sim(e_w, e_{\text {king}} - e_{\text {man}} + e_{\text {woman}})$$

其中相似度函数sim可以是欧几里得距离：

$$sim(u, v) = || u -v ||^2$$

或余弦相似度：

$$sim(u, v) = \frac{u^T v}{|| u ||\_2 || v ||\_2}$$

paper参考：：[Mikolov et. al., 2013, Linguistic regularities in continuous space word representations](https://www.aclweb.org/anthology/N13-1090)


## 1.4- Embedding matrix

当我们使用算法学习词嵌入时，实际上学习的是一个**嵌入矩阵（Embedding matrix）**，记为\\(E\\)。假如词汇表的大小是10000个单词，词嵌入的特征是300维，则嵌入矩阵是一个300x10000的矩阵。

![Xnip2018-07-24_21-45-23](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-24_21-45-23.jpg)

词汇表中第\\(i\\)个单词的one-hot向量为\\(o_i\\)，则对应的词向量为：

$$e_i = E \cdot o_i$$

虽然，有这样一个数学上的矩阵乘积关系，但这样做效率较低；实际应用中，我们一般使用一个函数直接查找\\(E\\)对应某列。

总结一下，我们的目标是让算法学习出嵌入矩阵\\(E\\)，我们会先随机的初始化\\(E\\)，然后用梯度下降去学习\\(E\\)的每个元素。

# 2- Learning Word Embeddings: Word2vec & GloVe
## 2.1- Learning word embeddings

在深度学习应用词嵌入的历史上，人们开始使用的算法相对复杂的多，但后来人们研究发现可以用越来越简单的算法实现，并且仍然得到非常好的结果（尤其是在大数据集上）。但今天相当流行的算法，已经相当简单，以至于人们第一次看到，会惊讶于它如此简单，简直就像魔术一样。

为了更直观的理解，我们先从传统的复杂算法讲起。我们用构建语言模型为例进行说明。事实上，通过构建语言模型是学习词嵌入的合理方法。

1. 基本方法

如下图，我们根据前面的单词预测最后一个单词是什么，每个单词下面的数字是其在词汇表里的序号。

![Xnip2018-07-25_08-22-42](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-25_08-22-42.jpg)

首先，我们将每个单词用one-hot向量表示（假如词汇表是10000个单词，则每个one-hot向量是10000维）：

![Xnip2018-07-25_08-24-46](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-25_08-24-46.jpg)

然后，将所有one-hot向量和一个矩阵\\(E\\)相乘，得到新的向量\\(e\\)（即\\(e_i = E \cdot o_i\\)）。这里的\\(E\\)就是我们要学习出的嵌入矩阵，在模型中是要学习的参数。这里我们假设嵌入后的词向量是300维，即\\(E\\)的维度是300x10000，每个\\(e_i\\)是300维

![Xnip2018-07-25_08-28-50](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-25_08-28-50.jpg)

然后把向量\\e\\)输入到一个神经网络，最后通过softmax输出10000维（和词汇表大小一样）。如果上面的训练样本里最后一个单词是juice，则让softmax目标输出也是juice。

![Xnip2018-07-25_08-32-43](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-25_08-32-43.jpg)

通过输入大量的训练数据（即一部分单词和对应的目标单词，context target pair），使用梯度下降和反向传播算法可以训练出嵌入矩阵\\(E\\)

在实际操作中，还会采用**固定大小的历史窗口**的做法，即如果窗口设置为4（窗口大小是超参），则总是用前面4个单词预测后面1个单词。比如例子中就用a glass of orange做预测的输入，而不是整句话。这样输入大小总是固定的。

另外值得说明的是，上面的模型中\\(E\\)只有一个，每个单词都是和同一个\\(E\\)相乘。

为什么会有效：
可以想象，在训练集中，Orange和Apple后面经常跟着相同的单词（比如juice），因此算法会倾向于将Orange和Apple的词嵌入设置为相似的，这样会更好的拟合模型。

paper参考：
[Bengio et. al., 2003, A neural probabilistic language model]

2. 扩展思路

前面的做法是，Context为前面4个单词（a glass of orange），而target是紧跟着的1个单词（juice）。

![Xnip2018-07-25_08-48-59](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-25_08-48-59.jpg)

这对构建语言模型来说是很合理的，但如果不是为了构建语言模型，而**仅仅为了学习词嵌入矩阵**，context可以有更多选择：
* 左右相邻的4个单词，比如a glass of orange和to go along with
* 前面的1个单词，比如orange
* 附近的1个单词，比如glass

其中最后一种做法也叫skip gram模型，看起来很神奇，但效果依然很好。

## 2.2- Word2Vec

Word2Vec是一种简单并且计算高效的学习词嵌入的算法。下面将介绍Word2Vec的skip-gram版本。

1. skip-gram模型

假如在训练集里有这样一条数据：

> I want a glass of **orange** juice to go along with my cereal.

我们将生成出几个context和target对，用来训练我们的有监督的学习问题。我们不局限于之前的做法，即：context必须是前几个单词，target是紧接着的一个单词。在skip-gram算法中，我们将**随机的取一个单词作为context，然后在context附近（附近是指在一个window区间，比如window是10，则在context的前后10个单词内选择）再随机选一个单词作为target**。比如上面的句子，选择orange作为context后，可能出现如下context-target对：
* orange <==> juice
* orange <==> glass
* orange <==> my

我们的监督学习问题是给出一个context单词，让算法预测在windows区间内的target单词。这个预测也许并不容易，但我们的目标也并不在与预测，**构造这个监督学习问题的目标并不是想要解决这个监督学习问题本身，而是想要使用这个学习问题来构造出一个好的词嵌入模型**。

下面是skim-gram的细节：

假如词汇表大小是10000，输入context单词的one-hot向量\\(o_c\\)，与嵌入矩阵\\(E\\)相乘，得到嵌入向量\\(e_c\\)，然后将\\(e_c\\)输入到softmax层，输出一个10000维的\\(\hat y\\)，表示预测词汇表每个单词的概率。

\\(o_c\\) ==> \\(E\\) ==> \\(e_c\\) ==> softmax ==> \\(\hat y\\)

可以看出，这个模型是一个相当简单，浅层的神经网络。

预测不同单词的概率是（t表示target，c表示context）：

$$p(t|c) = \frac{e^{\theta_t^T e_c}}{\sum^{10000}\_{j=1}e^{\theta_j^T e_c}}$$

\\(\theta_t\\)是一个和输出\\(t\\)有关的参数（省略了偏差项）。最终损失函数是：

$$L(\hat y, y) = -\sum^{10000}\_{i=1}y_i \log\hat y_i$$

由于\\(y\\)是一个one-hot向量，所以上式实际上10000个项里面只有一项是非0的。

总结一下，上面的一个模型里，有两处需要学习的参数，矩阵\\(E\\)以及softmax的参数\\(\theta_t\\)。通过优化这个模型，可以得到一个相当不错的词嵌入矩阵。

2. 分级softmax分类器

上面的模型有一个主要的缺点是：softmax的计算量太大，每次要计算概率都要计算整个词汇表10000个单词的求和，如果词汇表再大，比如扩充到10万、100万，计算会变得更慢。其中一种解决办法就是分级softmax分类器（Hierarchical Softmax Classifier）。即不是一下子确定属于词汇表中的哪一个，而是若干层的二分类器，先确定是前5000个，还是后5000个，以此类推。

![Xnip2018-07-26_21-31-52](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-26_21-31-52.jpg)

3. context的抽样

上面提到context是随机抽取的，但如何随机抽取呢？如果是以均匀分布抽样，则会使得常用单词背抽到的概率太大，比如context中充满了the, of, a, and, to等单词。实践中，会采用不同的分布去抽样，平衡常见单词和非常见单词。

4. CBOW

Word2Vec除了上面提到的skip-gram模型外，还有一个叫做CBOW，即连续词袋模型（Continuous Bag-Of-Words Model），通过获取中间词两边的上下文，去预测中间词，方向正好和skip-gram相反。

paper参考：[Mikolov et. al., 2013. Efficient estimation of word representations in vector space.](https://arxiv.org/pdf/1301.3781.pdf)


## 2.3- Negative Sampling

由于skip-gram模型中softmax的计算量较大，本节再介绍另外一种方法：负采样（Negative Sampling），和skip-gram类似，但更有效。

我们将定义新的监督学习问题：给定一个单词对（比如orange和juice），预测这两个单词是否是context-target对。

* 首先我们产生一个正样本（Positive Example），正样本的生成方法和skip-gram中类似，选择一个context单词，在一个windows大小附近随机选择一个target单词。比如上例语句中的orange和juice，我们把正样本标记为1。
* 然后使用相同的context，生成负样本（Negative Example），负样本的对应的单词从词汇表里随机选取，比如生成一个负样本orange-king，并将负样本标记为0。同样的方法，生成更多更多的负样本，可能是：orange-book, orange-the, orange-or。由于是随机选择的，我们总认为是负样本，**因此即便上面的orange-or的例子，or其实是Orange的target，我们依然标记为0**。最终形成如下记录：
![Xnip2018-07-27_08-55-09](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-27_08-55-09.jpg)

一个正样本，会选择多个负样本，其个数记为\\(k\\)，在较小数据集下\\(k\\)通常推荐取5-20，如果数据集较大，则\\(k\\)取值较小，比如2-5。

我们要构造的监督学习是，输入context-word对，输出word是否为对应的target，即context-word构成监督学习的x，而target标签构成y。即区分出两种单词对分布，一种是从句子里选择的context-target，一种是随机的从词汇表里选择的词汇对。我们用\\(c\\)代表context，用\\(t\\)代表word，用\\(y\\)代表target标签：

![Xnip2018-07-28_18-09-48](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-28_18-09-48.jpg)

我们将定义一个逻辑回归模型，跟定输入\\(c\\)和\\(t\\)的条件下，\\(y=1\\)的概率：

$$P(y=1 | c, t) = \sigma(\theta_t^Te_c)$$

其中\\(e_c\\)是context单词的词向量。

网络结构如下：

![Xnip2018-07-29_11-14-15](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-29_11-14-15.jpg)


对于嵌入向量\\(e\\)，对应了10000个可能的逻辑回归分类问题，其中就有上述正负抽样的单词，比如juice和king。每个二元逻辑分类用来判断word是否是context-target对。

每次迭代并不是训练10000个全部，而仅训练其中5个（以上面\\(k=4\\)为例），计算成本大幅降低。

这种方法就叫做**负采样（Negative Sampling）**，因为选择一个正样本，随机采样\\(k\\)个负样本。

怎样选择负样本？
这个算法有个重要细节是如何选择负样本，一种办法是根据每个单词在语料库中的经验概率进行采样，但会导致常用词被采样的频率很高；还有一种是均匀分布的采样，完全不考虑单词的实际频率。论文中有一个经验方法，介于上述两个方法之间：

$$p(w_i) = \frac{f(w_i)^{\frac{3}{4}}}{\sum^{10000}\_{j=1}f(w_j)^{\frac{3}{4}}}$$

其中\\(f(w_i)\\)是一个单词在语料库中的观测频率。通过取3/4次方，使得既考虑到单词的语料库频率，又能增加低频单词被选取的概率。

paper参考：[Mikolov et. al., 2013. Distributed representation of words and phrases and their compositionality](https://arxiv.org/pdf/1310.4546.pdf)


## 2.4- GloVe word vectors

之前已介绍了几个词嵌入的算法，NLP领域还有一个有一定势头的算法**GloVe（global vectors for word representation）**，虽然并不如Word2Vec或skip-gram常用，但足够简单。

算法介绍：

使用\\(X_{ij}\\)代表单词\\(i\\)出现在单词\\(j\))的上下文的次数。这里的\\(i\\)和\\(j\\)含义类似之前的\\(t\\)和\\(c\\)。

如果定义上下文的含义是在10个单词前后范围内，显然可以得出\(X_{ij}=X_{ji}\\)，即对称性。如果定义上下文是紧挨着的前一个单词，则没有对称性。但对于GloVe，我们一般选择前者的定义。

因此\\(X_{ij}\\)，就表示单词\\(i\\)和\\(j\))一起出现的频繁程度。

我们定义模型为：

$$J = \sum_{i}\sum_{j}f(X_{ij})(\theta^t_ie_j + b_i + b_j - \log(X_{ij}))^2$$

通过最小化上式，可以学习到一些向量，能够对两个单词同时出现的频率进行预测。另外，式中的\\(f(X_{ij})\\)有两个作用：
* 当\\(X_{ij}=0\\)时，\\(\log(X_{ij})\\)为无穷大，无法计算。此时定义\\(f(X_{ij})=0\\)，即对这样的情况不纳入计算。换句话说，至少要求两个词同时出现过一次。
* 另外，作为权重，调节常用和非常用单词的计算权重。既不给常用词过大的权重，也不给非常用词过小的权重。这一块详细参考GloVe的论文。

另外，从上面式子可以看出\\(\theta\\)和\\(e\\)是对称的，或者说在优化目标中起的作用是一样的，因此最终我们通常将\\(\theta\\)和\\(e\\)的均值作为最终的词向量，即：

$$e_w^{(final)} = \frac{e_w+\theta_w}{2}$$

虽然GloVe算法的优化函数非常简单（仅是一个二次代价函数），但结果确实奏效，可以学习到良好的词嵌入。

# 3- Applications using Word Embeddings
## 3.1- Sentiment Classification

情感分类（Sentiment Classification）是指通过一段文字，判断人们的情感是正面还是负面的。

情感分类的一个挑战是，**可能没有足够的标签数据**，但利用词嵌入，即便使用中等规模的数据量，可以构建一个良好的情感分类器。

下面是一个情感分类的例子，通过客户的评论x，判断客户对服务的打分y。可以用于判断人们的评价。

![Xnip2018-07-29_16-34-06](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-29_16-34-06.jpg)

我们需要创建一个模型，实现x到y的映射，一种简单的模型如下：

![Xnip2018-07-29_16-40-16](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-29_16-40-16.jpg)

首先将所有单词通过嵌入矩阵\\(E\\)转换为词向量，然后间词向量相加或平均，然后通过一个softmax层，输出1-5的评分。但这个模型有个缺点，是**没有考虑到单词的顺序**，对于包含了多个正面词汇的负面评价，可能得到相反的预测，比如下面的句子，虽然有多个good，但其实是完全的负面评价。

> “Completely lacking in good taste, good service, and good ambience.”

为此，我们可以使用RNN改进，如下图：

![Xnip2018-07-29_16-45-34](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-29_16-45-34.jpg)

将单词转化为词向量后，输入到一个many-to-one的RNN网络，然后通过softmax分类。

由于情感分类算法使用了词嵌入，即便语句中出现了训练时没有见过的单词，模型也可以很好的泛化。

## 3.2- Debiasing word embeddings

这里提到的偏见（bias）和之前算法中提到的偏差变量（bias variants）含义完全不同，这里的偏见是存指在于人们认识中的性别偏见、种族偏见等。

举个例子，之前我们通过词嵌入，可以实现Man-Woman到King-Queen的类比推理。同样可能出现如下充满偏见的类别推理，但这不是我们希望的：

* Man之于Computer Programmer与Woman之于Homemaker
* Father之于Doctor与母亲之于护士

词嵌入会反映性别、种族、年龄、性取向以及其他的偏见，这些偏见是由训练文本造成的。由于机器学习算法会被用来做重要的决策，因此消除这种偏见是十分重要的。

本节将分享一个消除性别偏见的例子（方法也适用于其他偏见），来源于论文[Bolukbasi et. al., 2016. Man is to computer programmer as woman is to homemaker? Debiasing word embeddings](https://arxiv.org/pdf/1607.06520.pdf)。

假若我们已经完成了词嵌入的学习。一些单词嵌入情况如下：

* 识别与偏见方向。比如性别偏见，我们可通过一组完全反应性别的单词得到，比如she与he，femal与male等单词的词向量分别做减法，然后做平均，得到的向量就是偏见方向。

    $$\text{average}(e_{he} - e_{she}, e_{male} - e_{famale}, ...)$$

    因此在整个300维的嵌入空间上，我们可以得到偏见的方向和非偏见的方向。假如认为偏见方向是1维（实际上可能不止），其他299维是非偏见方向。
    
* 中立（Neutralization）。即对于所有定义上与性别无关的单词（比如doctor、babysitter），将它们投影到偏见方向上，并在这个方向上消除。
    ![Xnip2018-07-29_20-31-08](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-29_20-31-08.jpg)

* 均衡（Equalization）。即对另外一些内含有性别的单词（比如grandfather、girl、boy、she、he），我们希望词向量上的差别仅仅在于性别方向。
    ![Xnip2018-07-29_20-37-29](https://cdn.imshuai.com/images/2018/07/Xnip2018-07-29_20-37-29.jpg)

最后，还有一个细节是，如何选择那些单词应该是中立的单词，论文的作者是通过训练一个分类器尝试解决。

实际模型要比我们描述的更复杂，可参考论文。
