---
layout: postclass: post-templatenavigation: True
title: Coursera Machine Learning机器学习课程总结
date: '2018-05-21 13:28:40'
tags:
- machine-learning
- coursera
- ai
---

Andrew Ng在Coursera上的[Machine Learning](https://www.coursera.org/learn/machine-learning)可以算是Coursera的镇店之宝。我从今年3月份才正式学习，经过两个月的努力终于刷完了11个week的课程。

# 课程难度
课程是极为基础的入门课程，无需对机器学习有任何基础。理解偏导数和矩阵的基本运算，基本就没问题了。对于偏导数的部分更是只借用这个概念，而不涉及任何推导。

编程方面使用了MATLAB，但也极为简单，不用担心。基本上是在一个搭好的框架上实现几个函数，而这些函数基本上都是几行代码即可搞定。
真正的难度不在于编码（尤其是对码农而言），主要是如何将运算矩阵化，尤其是多年不摸线性代数的情况下，有必要复习一下。比如下面的两个求梯度的函数，还是挺绕的。

![Coursera-translate-matrix](/content/images/2018/05/Coursera-translate-matrix.png)

# 主要内容总结

课程被拆分为11个星期完成，内容包含**教学**和**复习**，其中教学包含视频讲解和对应的Lecture Slides课件，复习分为quiz（皆为选择题）和programming assignment（使用matlab编程）。估摸着每周花6-8个小时应该能完成。

教学内容是全英文的，但视频有中文字幕。但并不推荐看中文字幕，建议还是使用英文字幕。主要是：所有文字材料都是英文的，要用两套术语来回切换很别扭；另外，也便于以后深入学习，英文的术语基本是统一的，但中文翻译就比较多。

视频我要吐槽的是字幕位置太坑爹了，会挡着PPT的内容，比如：
![transcript-on-lecture](/content/images/2018/05/transcript-on-lecture.png)
所以，能听懂是多么的重要啊。

编程作业使用MATLAB或Octave（可理解为开源版Matlab，语法兼容）完成。2018年以前提供的是本地试用版的MATLAB，2018年之只能用online版，不如前者方便；所以我索性就用了开源的Octave。

## Week 1
第一周，分为三个部分：
* Introduction
包括机器学习的主要概念：什么是Machine Learning，什么是Supervised Learning与Unsupervised Learning；以及怎么使用Coursera系统提交作业。
* Linear Regression with One Variable
这部分的例子本身很简单，说白了就是线性回归，基本上使用最小二乘法是完全可以解决。但==这里引入了整个课程最重要的部分：Hypothesis Function、Cost Function以及Gradient Descent==。后面的算法基本上都是一样的套路。
* Linear Algebra Review
选修，结合MATLAB快速复习了矩阵的基本运算。说道复习线性代数，我到觉得这几节讲解效果不好，推荐几个材料：
[MIT 18.06 Linear Algebra, Spring 2005](https://www.youtube.com/watch?v=ZK3O402wf1c&list=PLE7DDD91010BC51F8) MIT的线性代数公开课，我只看了前8课，有了新的认识。
[Essence of linear algebra](https://www.youtube.com/watch?v=kjBOesZCoqc&index=2&list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab&t=0s) 完全从Intuition角度让你理解线性代数的概念，配上精彩的动画，非常赞。

另外不得不说，国外的数学教学很重视Intuition直觉的培养，人对已概念要有感性的理解，首先要觉得make sense，才能高效的学习。纵观Andrew Ng的课程，几乎每个课程都会先讲概念，然后再补一堂XXX intuition.随便拿第一期的课程感受一下：Cost Function本身讲8分钟，接下来补了两个视频将Intuition，一共19分钟！Gradient Descent也补了11分钟的Intuition！

![coursera-intuition](/content/images/2018/05/coursera-intuition.png)

## Week 2
本周，分为2大部分：
* Linear Regression with Multiple Variables
是对第一周的推广，需要偏导数的知识。==另外引入了几个重要的概念：Feature Scaling、Learning Rate、Normalize，可以说是机器学习算法数据预处理的基本步骤。==
* Octave/Matlab Tutorial
介绍MATLAB基本语法。

另外，本周开始出现编程作业。

## Week 3
本周开始讲解Logistic Regression以及如何处理overfitting问题
* Logistic Regression
分类问题。==针对分类问题提出了新的Hypothesis function和Cost Function。主要引入了sigmoid function解决。==
另外，同时扩展到Multiclass Classification，使用的是One-vs-all的思想。
* Regularization
==引入了一对重要的问题和概念：Overfitting和Regularization==。Overfitting的问题通过简单的引入一个平方和就解决了！没有证明，但感性上应该能接受。后面的算法在处理overfitting问题时，做法基本一致。

## Week 4
这周介绍了传说中的**神经网络Neural Networks**，多年来听的都烦了，看的也多少遍了（一排排圆圈连接的上密密麻麻的箭头），在这一课基本讲清楚了。可能是考虑到内容复杂，这一周只是讲解了Neural Network的Model Representation（即Hypothesis Function）,但如何求解则是下周的问题。
同样，为了做好铺垫，这周也大量的介绍了Intuition。

## Week 5
==这周可能是Machine Learning课程中最难的一周==。我在此停留了半个多月。
==这周的核心是介绍Neural Network的Cost Function和Backpropagation算法==，尤其是后者，课程中并没有详细的推导过程，反而让人更难理解这个算法，最后我也是通过参考其他材料才搞定的，推荐一份介绍Backpropagation算法的材料：
[How the backpropagation algorithm works](http://http://neuralnetworksanddeeplearning.com/chap2.html)

## Week 6
在前几周学习了三个算法（Linear Regression、Logistics Regression和Neural Network）后，本周在此基础上，讨论了机器学习算法的评价方法、如何根据一些指标优化算法。接着通过一个垃圾邮件分类器的例子进一步说明。
重要的概念有：
* 将数据拆分成三个集合用于训练、验证和测试：Training sets/Validation sets/Test sets
* 进一步讨论欠拟合和过拟合问题：Bias vs Variance以及Learning Curves
* 介绍Skewed Data下单纯用错误率评价算法的局限，并引入评价标准：Precision/Reacall以及F1 Score

==这些概念很基础，也是机器学习的基础。==

## Week 7
本周介绍了大名鼎鼎的SVM支持向量机。内容比较浅，并且核心的算法通过调用库函数一笔带过，主要侧重于感性的认识。

## Week 8
从本周开始，正式进入Unsupervised Learning的部分。首先就是K-means聚类算法，应该是比较好理解的。
然后是PCA算法，用于对数据降维。但核心的算法实现（比如SVD分解）是通过库函数调用实现的。感性上介绍了PCA主要做的事情，但如果要进一步学习还是需要理解线代里的一些概念。

## Week 9
这周难度可以说是和Week 5有的一拼，内容分为两个比较独立的部分：Anomaly Detection和Recommender System。个人觉得应该分为两个week学习比较好。
* Anomaly Detection
相对容易，有基本的统计知识，学过高斯分布的话，基本是没有难度，主要是学习这种算法的思想
* Recommender System
通过用户对电影打分的方式，为用户推荐电影。难点在于Collaborative Filtering的算法实现。虽然还是通过Cost Function和Gradient Descent实现。但同时要对Parameters和Features做Gradient Descent，而且是针对多个用户。概念还不算难，==但课后编程作业，实现具体的矩阵运算有一定的难度。==

## Week 10
经过week 9的小高潮后，剩下的两周略显“水”了些，内容简单，并且连编程作业都没有了，可能是考虑到大家毕业心切，就放水了吧。
本周主要讲大规模数据的集群学习，现实中这可能是最重要的问题了。本周也只能讲一下皮毛：Map Reduce。可能考虑操作起来比较难，也就不安排代码了

## Week 11
最后一周，还是很激动的；Andrew Ng也就不难为大家了。本来以为这周要亲手实现以下Photo OCR，实际上只是简单了介绍了一下Machine Learning Pipline，并说明如何找出Pipline中的瓶颈。基本就是一片科普文章。
轻松毕业。

# 关于Andrew Ng的教学
* 课程使用了Matlab，可能现在不太合适了，如果用Python可能会更好。
* 课程中有不少错误，但都是在课后更正才提示。如果能直接在视频中更正最好。
* Andrew Ng太会鼓励人了，一会儿说学的都是state of arts，一会说你已经ahead of the curve，即便是硅谷的专家也只不过这样。最后结业还把你捧上天了：你现在已经是机器学习专家了。==鼓励是好的，但不必当真。==

最后晒一下我的证书

![coursera-certificate](/content/images/2018/05/coursera-certificate.jpeg)


