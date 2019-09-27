---
layout: post
title: Machine Learning Yearning Notes
subtitle: 看来不是瞎拍脑袋调
date: 2018-10-17
categories: Note
tags: [ML]
catalog: true
---

### Machine Learning Yearning 笔记

上班的时候train模型总是要跑上片刻，有时候开很无聊的会，就把吴恩达的新书Mchine Learning Yearning读完了。作为平时就在瞎随缘调（不是）模型的菜鸟，这本小册子带在算法和数学之外简单明了的告诉我一些很常见的问题以及应对方法，具有很高的实践性。

嗯，上面这段话就像是初高中评论教材／教师时自己写的评语。这么多年没有长进。（笑

[这本书的链接](<https://www.deeplearning.ai/machine-learning-yearning/>) 上输入邮箱就可以得到最新手稿啦～ 

感谢大佬免费提供教育资源( ´▽` )ﾉ

英文的部分都是原文了

### Train/Test/Dev Sets

* Training set — Which you run your learning algorithm on
* Dev (development) set — Which you use to tune parameters, select features, and make other decisions regarding the learning algorithm. Sometimes also called the hold-out cross validation set 
* Test set — Which you use to evaluate the performance of the algorithm, but not to make any decisions regarding what learning algorithm or parameters to use

关于我一直都用train set当dev的做法....应该问题不大吧\_(:з」∠)\_ 就是好慢

关于dev／test的一些基本原则：

* Reflect data you expect to get in the future and want to do well on
  * Data mismatch: generalizes well to new data drawn from the same distribution as the training set(training dev set), but not to data drawn from the dev/test set distribution(dev/test set)
* Dev/test come from same distribution (but not necessary same distribution as training set)
  * 如果dev表现好而test表现不好的话… 原因会是overfit on dev呢，test set更难呢，还是只是因为test和dev不一样呢？
* Dev set should be large enough to reflect different algorithms, test set should be large enough to give high confidence in overall performance of the system
* Single number metrics

### Basic error Analysis

Error Analysis refers to the process of examining dev set examples that your algorithm misclassified, so that you can understand the underlying causes of the errors.

在development set上面进行误差分析，包括错误的原因和比例，这样就可以得知是什么原因对模型performance造成了影响，进而明确提升的方向。如果dev set很大的话可以将它们分成Eyeball dev set和Blackbox dev set，防止在Eyeball dev set上面overfit。 模型的搭建也应该由浅入深，从最基本的结构开始。

### Bias and Variance

* Bias: the algorithm’s error rate on the training set
* Variance: how much worse the algorithm does on the dev (or test) set than the training set

Bias and variance are two big source of error. 

所以根据模型的bias和variance，一个模型可能会overfit(high variance), underfit(high bias low variance). 或者向前几天我的那个，both overfit and underfit(high bias and variance，暴风哭泣)

将自己模型的bias和variance与optimal error rate相比，有这么几个概念需要明确：

* Optimal error rate (“unavoidable bias”): “unavoidable” part of a learning algorithm ’ s bias 
  * Bias = Optimal error rate (“unavoidable bias”) + Avoidable bias
* Avoidable bias: the difference between the training error and the optimal error rate
  * If this number is negative, you are doing better on the training set than the optimal error rate. This means you are overfitting on the training set, and the algorithm has over-memorized the training set. Should focus on reducing variance instead of bias. 
* Variance: difference between the dev error and the training error 
  * All variance is “avoidable” 

所以调试模型就是要尽可能减少avoidable bias和variance. 具体而言：

* High avoidable bias: increase the size of your model (天呀怎么这么慢呀QAQ)
  * Increasing the model size generally reduces bias, but it might also increase variance and the risk of overfitting.
  * Should add well-designed regularization method, like L2 regularization or dropout
* High variance: add data to training set （诶呀数据就这么多了QAQ）
  * Can also tune regularization method
  * If you think you have data that has no benefit,you should just leave out that data for computational reasons

~~日常瞎调，提升模型复杂度的同时加入dropout和regularizatoin应该是对的方向~~

#### Techniques for reducing avoidable bias 

* Increase the model size 
* Modify input features based on insights from error analysis 
* Reduce or eliminate regularization -> increase variance
* Modify model architecture 
* Add more training data -> usually has no significant on bias but help variance problems

#### Techniques for reducing variance 

* Add more training data 
* Add regularization (L2 regularization, L1 regularization, dropout) -> increase bias
* Add early stopping -> also increase bias
* Feature selection to decrease number/type of input features
* Decrease the model size: *Use with caution*
  * The advantage of reducing the model size is reducingyour computational cost and thus speeding up how quickly you can train models
* Modify input features based on insights from error analysis 
* Modify model architecture 

### Learning curves

这里介绍了我从没画过的随着training set size而导致dev error变化的图。一个理想中的曲线是dev error随着training size的增长而逐渐向desired performance收敛。如果dev error提前收敛的话，至少可以知道增加更多的数据是没有用的。加上training error之后，training error会随着training size增长而增长。根据desired performance与training error之间的gap可以判断出bias情况，根据training error和dev error的gap可以判断处variance情况。

### Debugging and Pipeling 

最后几章简单介绍了下end to end learning system，强化学习，pipeling的东西。比较有用的经验就是简化模型，通过手动输入perfect output每次mask掉模型的一部分来进行调整，然后和desired performance进行比较，分别试一试调整variance和bias的方法。



看完啦。其实是一本只有118页的干货手册。

嗯，要是每周能减掉1磅就满足了，好饿啊。