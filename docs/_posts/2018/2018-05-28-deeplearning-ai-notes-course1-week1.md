---
title: deeplearning.ai深度学习笔记（Course1 Week1）：Introduction to deep learnring
date: '2018-05-28 13:22:09'
tags:
- deep-learning
- coursera
- ai
- deeplearning-ai-notes
---

==提示：**为方便阅读，《deeplearning.ai深度学习笔记》系列博文已统一整理到 [http://dl-notes.imshuai.com](http://dl-notes.imshuai.com)**==

Andrew Ng去年离开百度后，再次投身到了人工智能领域的大众教育上，并推出了[deeplearning.ai](http://www.deeplearning.ai)网站，旨在建立深度学习教育品牌。不过目前课程主要还是托管在Coursera上，deeplearning.ai本身只不过就是一个 Landing page。

学完了机器学习（[Coursera Machine Learning机器学习课程总结](/coursera-machine-learning-review/)），我要快马加鞭把深度学习也搞一遍。

之前Machine Learning的笔记都是记在自己的Evernote里面；既然要记录，不然直接放到博客，互相交流岂不更好。

目前在Coursera上以Specialization的形式出现，一共包含5个Coursera：
* Course 1 Neural Networks and Deep Learning
* Course 2 Improving Deep Neural Networks 
* Course 3 Structured Machine Learning Projects 
* Course 4 Convolutional Neural Networks
* Course 5 Sequence Models 

课程以订阅的形式提供，订阅的价格是$49/月，因此：**学的越快，越省钱**。极限情况下可以在7天试用期学完则一分钱不要。不过上班族很难吧，我计划1-2个月完成。



Course1，一共有4个week的课程，题目是：Neural Networks and Deep Learning

下面是Course1 Week1的笔记。

# Welcome to Deeplearning Specialization

## welcome（5min）

深度学习的光明前景：

> AI is the new Electricity!

5个Course的简介，以及Course1的目标：Recognize Cat

几个如雷贯耳的名词（先记下，后面慢慢学））：
* CNN: Convolutional Neural Networks 卷积神经网络
* RNN: Recurrent Neural Networks 循环神经网络
* LSTM: Long Short-Term Memory 长短期记忆网络

# Introdcution to Deep Learning
## what'is a neural network? (7min)

Deep Learning = training (very large) neural network

What's a neural network?
* a very **simple** neural network: House Price-size Linear regression（就像一个Lego brick）
![simple-neural-network](https://cdn.imshuai.com/images/2018/05/simple-neural-network.png)
* a **larger** neural network: stacking together many of these Lego bricks。
![manual-larger-neural-network](https://cdn.imshuai.com/images/2018/05/manual-larger-neural-network.png)

注意，上图只是一个手工演示的larger neural network，与实际的区别：
1. hidden layer并不是手工设置的
2. 每一层都是上一层所有输入的函数

实际的neural network：
![real-neural-network](https://cdn.imshuai.com/images/2018/05/real-neural-network.png)

Neural Network的两个特点；
1. Neural networks are remarkably good at **figuring out functions that accurately map from x to y**. 
2. Most powerful in supervised learning.

一个常见的激活函数 ReLU ：
ReLU function: **Re**ctified **Li**near **U**nits (rectified means: taking a min of zero，原文是taking a *max* of zero，我觉得可能讲反了吧)
![relu](https://cdn.imshuai.com/images/2018/05/relu.png)

## Supervised Learning with Neural Network (8min)

截止到目前，**Neural Network的成功应用基本都在Supervised Learning**。比如：Ad，Images vision, Audio to Text, Machine translation, Autonomous Driving![supervised-learning-exmples](https://cdn.imshuai.com/images/2018/05/supervised-learning-exmples.png)


Neural Network examples:
![](https://cdn.imshuai.com/images/2018/07/NeuralNetworkExamples.jpg)

Structured Data vs Unstructured Data

## Why is Deeplearning taking off? (8min)

**Scale** drives deep learning progress
![Screen-Shot-2018-05-28-at-20.42.54](https://cdn.imshuai.com/images/2018/05/Screen-Shot-2018-05-28-at-20.42.54.jpg)
Scale means: Bigger network and More (labeled) data.

在数据匮乏的时代，算法的性能更依赖于技巧和手工设置的feature (the algorithms is actually not very well defined so if you don't have a lot of training data is often up to your skill at hand engineering features )
大数据时代，大数据处于支配地位。(big data regime very large training sets very large M regime in the right that we more consistently see largely Ronettes dominating the other approaches)

三大原因：
* Data
* Computation
比如：GPU
* Algorithm
比如：sigmoid function→ReLU function
![Screen-Shot-2018-05-28-at-20.53.10](https://cdn.imshuai.com/images/2018/05/Screen-Shot-2018-05-28-at-20.53.10.jpg)

算法必须快，形成一个正循环，快速验证：
![Screen-Shot-2018-05-28-at-20.54.59](https://cdn.imshuai.com/images/2018/05/Screen-Shot-2018-05-28-at-20.54.59.jpg)


# Hero of Deep Learning
As part of this course by deeplearning.ai, hope to not just teach you the technical ideas in deep learning, but also **introduce you to some of the people, some of the heroes in deep learning**.（大概看了一下，后面还会介绍很多大神，这是比之前的Machine Learning增加的特色）

Geoffrey Hinton interview (40min)
![Geoffrey Hinton interview](https://cdn.imshuai.com/images/2018/05/Screen-Shot-2018-05-28-at-20.59.23.jpg)

* God father of deep learning
* 传奇辗转的求学经历，为研究how does the brain store memories.：
    * Hologram made me interested in how does the **brain store memories**. 
    * Studying physiology and physics in Cambridege. 
    * Gave up and tried to do philosophy
    * Switched to psychology. 
    * Took some time off and became a carpenter（瞎折腾这么久，也迷茫了😄）
    * Then I decided that I'd try AI, and went of to Edinburgh
    * Then get a PhD in AI.
* 前景惨淡
    * 找不到工作
    * in Britain, neural nets was regarded as kind of silly
* 看到曙光
    * in California, Don Norman and David Rumelhart were very open to ideas about neural nets. **It was the first time I'd been somewhere where thinking about how the brain works**（不忘初心啊）, and thinking about how that might relate to psychology, was seen as a very positive thing. 
    * 1982与Rumelhart写了backpropagation论文, in UCSD。而实际上这又是一个重复发明了很多次的算法。但直到他们的工作，backpropagation才真正被人们重视。
* 最得意的算法发明：So I think the most beautiful one is the work I do with Terry Sejnowski on **Boltzmann machines**。
    *  **Simple** algorithm, beautiful.
    *  And it looked like the kind of thing you should be able to get in a brain because each synapse only needed to know about the behavior of the two neurons it was directly connected to. 
*  ReLU
*  Relationship between backpropagation and brains
    *  I guess my main thought is this. If it turns out the back prop is a really good algorithm for doing learning. Then for sure evolution could've figured out how to prevent(字幕可能用错词了吧） it. 
    *  And I think the brain probably has something that may not be exactly be backpropagation, but it's quite close to it
* Multiple time skills in deep learning
    * Fast weights
* capsules
    * subsets as a capsule
* your understanding of AI changed over these years? 
    * most humain learning is unsupervised learning. What's worked over the last ten years or so is supervised learning, **in the long run, I think unsupervised learning is going to be absolutely crucial**.  And things will work incredibly much better than they do now when we get that working properly, but we haven't yet. 
    * I think generative adversarial nets are one of the sort of biggest ideas in deep learning that's really new. 
* a sort of basic principle about how you model anything. 
    * You take your measurements, and **you're applying nonlinear transformations to your measurements until you get to a representation as a state vector in which the action is linear**. So you don't just pretend it's linear like you do with common filters. But you actually find a transformation from the observables to the underlying variables where linear operations, like matrix multipliers on the underlying variables, will do the work. 
* Advice for breaking through AI and deeplearning
    * My advice is sort of read the literature, but don't read too much of it.Read a little bit of the literature. And notice something that you think everybody is doing wrong, I'm contrary in that sense. You look at it and it just doesn't feel right. And then figure out how to do it right. 
    * Develop and trust your intuitions. When you have what you think is a good idea and other people think is complete rubbish, that's the sign of a really good idea. 
    * **Never stop programming**.
    ![never-stop-programming](https://cdn.imshuai.com/images/2018/05/never-stop-programming.jpg)
* advice for new grad students
One good piece of advice for new grad students is, see if you can find an advisor who has beliefs similar to yours. Because if you work on stuff that your advisor feels deeply about, you'll get a lot of good advice and time from your advisor. （这显然就是自己的亲身经历）
*  I kind of agree with you, **that it's not quite a second industrial revolution, but it's something on nearly that scale**. And there's a huge sea change going on, basically because **our relationship to computers has changed. Instead of programming them, we now show them, and they figure it out**.
*  symbolic AI
And so I think thoughts are just these great big vectors, and that big vectors have causal powers. They cause other big vectors, and that's utterly unlike the standard AI view that thoughts are symbolic expressions.  
