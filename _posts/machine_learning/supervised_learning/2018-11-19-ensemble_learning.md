---
layout: post
title: Ensemble Learning
subtitle:
author: Bin Li
tags: [Machine Learning]
image: 
comments: true
published: false
---



理论上可以证明（周志华机器学习第八章），随着集成中个体分类器数目 $T$ 的增大，集成的错误率将指数级下降，最终趋向于零。值得注意的是，这里的前提是基学习器的误差相互独立！

目前的集成方法大致分成两类：
* 个体学习器之间存在强依赖关系、必须串行生成的序列化方法（Boosting）
* 个体学习器间不存在强依赖关系、可同时生成的并行化方法（Bagging 和 Random Forest）

思想：通过将多个弱学习器集成起来能够达到强学习器的效果。“三个臭皮匠顶个诸葛亮”。

问题：
* 如何得到这些弱分类器？
* 有了弱分类器，如何集成？即结合策略。

## Boosting
![](/img/media/15445148705893.jpg)


## Bagging
![-w359](/img/media/15445149854585.jpg)

![](/img/media/15445158241697.jpg)


从图中可以看出 Bagging 的弱学习器之间并没有什么联系，为了要尽可能使得集成的个体学习器能够尽可能相互独立，在实际中因为很难做到完全独立，所以 Bagging 采取随机采样的方式，从训练集中有放回的随机采样同等大小的数据集训练对应的弱学习器，进行 $T$ 次采样训练 $T$ 个弱学习器。通过采样得到训练数据不同，使得训练得出的弱学习器尽可能的有较大差异，以达到相对个体学习器相对独立的目的。最后我们将这 $T$ 个个体学习器集成起来。

### Bagging 算法流程
Bagging 算法流程比较简单，假设输入为样本集为

$$
D = \left\{ \left( x , y _ { 1 } \right) , \left( x _ { 2 } , y _ { 2 } \right) , \ldots \left( x _ { m } , y _ { m } \right) \right\}
$$

弱学习器记作 $G(x)$，迭代次数为 $T$：
1）对于 $t = 1,2 \ldots , T$：
a）对训练集进行第 $t$ 次随机采样，共采集 $m$ 次，得到包含 $m$ 个样本的采样集 $D_t$
b）用采样集 $D_t$ 训练第 $t$ 个弱学习器 $G_t(x)$
2) 如果是分类算法预测，则 $T$ 个弱学习器投出最多票数的类别或者类别之一为最终类别。如果是回归算法，$T$ 个弱学习器得到的回归结果进行算术平均得到的值为最终的模型输出。