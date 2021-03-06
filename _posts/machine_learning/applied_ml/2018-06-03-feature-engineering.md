---
layout: post
title: 特征工程
author: "Bin Li"
tags: [Machine Learning]
category: ""
comments: true
published: true
---

特征工程（Feature Engineering）是利用领域知识和现有数据，创造出新的特征，用在机器学习算法上的过程。

![](/img/media/15427027069088.jpg)

Features:
1. numeric
2. categorical
3. ordinal
4. datetime
5. coordinates

层次化特征，微观特征，宏观特征。

## 特征工程-创造特征

### 1)  好的特征
好的特征以及数据样本决定我们模型优化的上限，所以找到好的特征非常重要。好的特征来源于对业务的深入理解。首先自己要深入理解业务的运作方式，了解影响模型 label 目标的主要业务因素；其次多和业务的专家沟通，获取到从他们角度认为重要的因素；拉入更多人员进行头脑风暴，找到尽可能多的影响因素。

不同特征当前可用性也不一样。 初期我们要更多关注那些已有数据、线上易获取的特征；然后对于一些我们排序出来重要因素，如果当前没有数据以及线上无法获取，我们要尽快准备；

具体特征都是业务相关的，宏观上讲一些可能的方向供参考，例如用户使用上下文中可以感知的因素的属性、用户历史的业务数据因素、时间因素、地域因素、用户的个性化因素（年龄、爱好等）、用户使用场景中各种历史沉淀的评分因素（好评、差评等数量）、场景各种对象的属性特征（例如长度、颜色、形状等）。

### 2)  特征可视化
将特征数据通过散点图、分布图等方式观察下特征数据的特点，一方面可以观察特征对于分类等数据区分度，更重要的是可以根据数据分布，确认特征是否存在异常情况，例如由于线上 bug 导致部分数据是错误的。这一块建议重视关注，可能比较费时，但是可以避免后面模型优化或 bad case 排查的工作量。

### 3)  统计特征
有了原始的特征因素后，可以让这个特征具备更强的表达性。统计化是一个常用的方式，主要有最大值、最小值、平均值、标准差、方差、中位数、分布区间统计数等。例如周一的平均订单数、最大订单数等。可以查看下节中类别特征与数值特征的组合。

### 4)  特征组合
组合多个相关特征提取出其相关的规律，例如多个特征加和、求差、乘除、求斜率、变化比率、增长倍数等。


**数值特征的简单变换**

1. 单独特征列乘以一个常数（constant multiplication）或者加减一个常数：对于创造新的有用特征毫无用处；只能作为对已有特征的处理，也就是说数据预处理中特征归一化等操作是不能产生新的特征的。
2. 任何针对单独特征列的单调变换（如对数）：不适用于决策树类算法。对于决策树而言，$X$、$X^3$、$X^5$ 之间没有差异， $|X|$、 $X^2$、 $X^4$ 之间没有差异，除非发生了舍入误差。
3. **线性组合**（linear combination）：**仅适用于决策树**以及基于决策树的ensemble（如gradient boosting, random forest），因为常见的axis-aligned split function不擅长捕获不同特征之间的相关性；**不适用于SVM、线性回归、神经网络等**。
4. 多项式特征（polynomial feature）：[sklearn.preprocessing.PolynomialFeatures](http://link.zhihu.com/?target=http%3A//scikit-learn.org/stable/modules/generated/sklearn.preprocessing.PolynomialFeatures.html)。
5. 比例特征（ratio feature）：$X_1 / X_2$。
6. 绝对值（absolute value）：$|X_1 - X_2|$
7. 其他一些操作的特征：
$max(X_1, X_2)$，$min(X_1, X_2)$，$X_1 xor X_2$。

**类别特征与数值特征的组合**

用 N1 和 N2 表示数值特征，用 C1 和 C2 表示类别特征，利用 Pandas 的 groupby 操作，可以创造出以下几种有意义的新特征（其中，C2 还可以是离散化了的 N1）：
```
median(N1)_by(C1)  \\ 中位数
mean(N1)_by(C1)  \\ 算术平均数
mode(N1)_by(C1)  \\ 众数
min(N1)_by(C1)  \\ 最小值
max(N1)_by(C1)  \\ 最大值
std(N1)_by(C1)  \\ 标准差
var(N1)_by(C1)  \\ 方差
freq(C2)_by(C1)  \\ 频数

freq(C1) \\这个不需要groupby也有意义
```

仅仅将已有的类别和数值特征进行以上的有效组合，就能够大量增加优秀的可用特征。

将这种方法和线性组合等基础特征工程方法结合（仅用于决策树），可以得到更多有意义的特征，如：
```
N1 - median(N1)_by(C1)
N1 - mean(N1)_by(C1)
```


将多个维度特征相互交叉，产生更多具体场景化的特征，例如和不同时段段、和不同的地理位置范围组合。


### 5)  特征拆解
将一个特征拆为多个**更易理解**的特征。 例如日期，可以拆为年、月、日、小时、分、秒、星期几、是否为周末。

### 6)  数字型特征重构
通过调整数字单位等方式，可以调整数字大小。 例如6500 克 可以表达6.5千克； 也可以进一步拆解表达为6千克、0.5千克等。这不是没啥道理嘛……

### 7)  One-hot encoding
将类型特征映射多个是否特征，例如颜色可映射是否为为红色、是否为绿色、是否为蓝色。

### 8) 统计性特征映射为解释型特征
将一个数字型或统计性特征，映射为多个范围区间，然后为每个区间为一个类别，接着借助于 onehot encoding 就变为一系列是否的解释型特征。例如历史月订单 0~5 为低频、6~15 为中频、 大于16为高频， 订单量10数字就可以变为 [0,1,0] 这三维特征。

### 用基因编程创造新特征
Welcome to gplearn’s documentation!

基于genetic programming的symbolic regression，具体的原理和实现参见文档。目前，python环境下最好用的基因编程库为gplearn。基因编程的两大用法：

* 转换（transformation）：把已有的特征进行组合转换，组合的方式（一元、二元、多元算子）可以由用户自行定义，也可以使用库中自带的函数（如加减乘除、min、max、三角函数、指数、对数）。组合的目的，是创造出和目标y值最“相关”的新特征。这种相关程度可以用spearman或者pearson的相关系数进行测量。spearman多用于决策树（免疫单特征单调变换），pearson多用于线性回归等其他算法。
* 回归（regression）：原理同上，只不过直接用于回归而已。

### 用决策树创造新特征
在决策树系列的算法中（单棵决策树、gbdt、随机森林），每一个样本都会被映射到决策树的一片叶子上。因此，我们可以把样本经过每一棵决策树映射后的index（自然数）或one-hot-vector（哑编码得到的稀疏矢量）作为一项新的特征，加入到模型中。

具体实现：apply()以及decision_path()方法，在scikit-learn和xgboost里都可以用。



决策树、基于决策树的ensemble
spearman correlation coefficient


线性模型、SVM、神经网络
对数（log）
pearson correlation coefficient



### 9) 挖掘特征
对于有些特征我们的数据没有明确的标注，但是我们认为也很重要。这是可以用机器学习以及部分样本标注或人工标注的方式挖掘一些特征。 例如假设大部分用户的性别我们不知道，但是部分用户可以通过各种其他途径知道。那可以基于这个样本训练出一个性别分类预测的模型，然后预测出所有用户的性别，将这个预测结果做为特征。

### 10) 特征自学习
在深度学习中，在构造出得到好的原始特征和适用于特征的网络结构后，特征组合和抽象会交给深度学习自行学习。 典型可以参考 CNN 算法，通过网络的卷积、池化等操作将原始图片特征，通过网络层学习抽象出了高层次的边缘、轮廓等特征。

### 11) 特征筛选
当有很多特征时，有部分特征是强相关的，属于冗余特征；有部分特征可能贡献小甚至负面贡献。 这时候需要做一些特征筛选。例如特征两两组合确认特征之间的相关性系数，对于相关性非常高的特征只保留一个；**特征和 Label 标注做相关性判断，去掉一些相关性差的特征；**当然也可以通过控制特征增长的过程，从基础特征集合开始，逐渐加入新特征或新特征集实验，如果新特征效果不好，则丢弃。

有些模型自带特征筛选的能力，例如gbdt（ xgboost）、回归中正则化等。通过这些模型一定程度也能达到特征筛选的目标。不过如果特征量特别多，建议在上线前，去掉无效特征，这样既可以避免特征维护的工作量，同时也能提高线上性能。

---

Feature engineering is a vital component of modelling process, and it is the toughest to automate. It takes domain expertise and a lot of exploratory analysis on the data to engineer features

1. single variable Basic transformations: x, x^2 ,sqrt x ,log x, scaling

2. If variable's distribution has a long tail, apply Box-Cox transformation (taking log() is a quick & dirty way).

3. One could also perform analysis of residuals or log-odds (for linear model) to check for strong nonlinearities.

4. Create a feature which captures the frequency of the occurrence of each level of the categorical variable. For high cardinality, this helps a lot. One might use ratio/percentage of a particular level to all the levels present.

5. For every possible value of the variable, estimate the mean of the target variable; use the result as an engineered feature.

6. Encode a variable with the ratio of the target variable.

7. Take the two most important variables and throw in second order interactions between them and the rest of the variables - compare the resulting model to the original linear one

8. if you feel your solutions should be smooth, you can apply a radial basis function kernel .  This is like applying a smoothing transform.  

9. If you feel you need covariates , you can apply a polynomial kernel, or add the covariates explicitly

10. High cardinality features : convert to numeric by preprocessing: out-of-fold average two variable combinations

11. Additive transformation

12. difference relative to baseline

13. Multiplicative transformation : interactive effects

14. divisive : scaling/normalisation

15. thresholding numerical features to get boolean values

16. Cartesian Product Transformation

17. Feature crosses: cross product of all features -- Consider a feature A, with two possible values {A1, A2}. Let B be a feature with possibilities {B1, B2}. Then, a feature-cross between A & B (let’s call it AB) would take one of the following values: {(A1, B1), (A1, B2), (A2, B1), (A2, B2)}. You can basically give these ‘combinations’ any names you like. Just remember that every combination denotes a synergy between the information contained by the corresponding values of A and B.

18. Normalization Transformation: -- One of the implicit assumptions often made in machine learning algorithms (and somewhat explicitly in Naive Bayes) is that the the features follow a normal distribution. However, sometimes we may find that the features are not following a normal distribution but a log normal distribution instead. One of the common things to do in this situation is to take the log of the feature values (that exhibit log normal distribution) so that it exhibits a normal distribution.If the algorithm being used is making the implicit/explicit assumption of the features being normally distributed, then such a transformation of a log-normally distributed feature to a normally distributed feature can help improve the performance of that algorithm.

19. Quantile Binning Transformation

20. whitening the data

21. Windowing -- If points are distributed in time axis, previous points in the same window are often very informative

22. Min-max normalization : does not necessarily preserve order

23. sigmoid / tanh / log transformations

24. Handling zeros distinctly – potentially important for Count based features

25. Decorrelate / transform variables

26. Reframe Numerical Quantities

27. Map infrequent categorical variables to a new/separate category.

28.Sequentially apply a list of transforms.

29. One Hot Encoding

30. Target rate encoding

Hash Trick Multivariate:

31. PCA

32. MODEL STACKING

33. compressed sensing

34..guess the average” or “guess the average segmented by variable X”

Projection : new basis

35. Hack projection:

Perform clustering and use distance between points to the cluster center as a feature
PCA/SVD -- Useful technique to analyze the interrelationships between variables and perform dimensionality reduction with minimum loss of information (find the axis through the data with highest variance / repeat with the next orthogonal axis and so on , until you run out of data or dimensions; Each axis acts a new feature)
36.Sparse coding -- choose basis : evaluate the basis  based on how well  you can use it to reconstruct the input and how sparse it is take some sort of gradient step to improve that evaluation

efficient sparse coding algorithms
deep auto encoders
37 :Random forest: train bunch of decision trees :use each leaf as a feature

## References
1. [机器学习之 特征工程](https://juejin.im/post/5b569edff265da0f7b2f6c65)
2. [The Comprehensive Guide for Feature Engineering](https://adataanalyst.com/machine-learning/comprehensive-guide-feature-engineering/)
3. [【持续更新】机器学习特征工程实用技巧大全](https://zhuanlan.zhihu.com/p/26444240)
4. ✳️ [Machine Learning Kaggle Competition Part Two: Improving Feature engineering, feature selection, and model evaluation](https://towardsdatascience.com/machine-learning-kaggle-competition-part-two-improving-e5b4d61ab4b8)
5. [Feature Engineering 特徵工程中常見的方法](https://vinta.ws/code/feature-engineering.html)
6. [机器学习之step by step实战及知识积累笔记](https://www.cnblogs.com/kidsitcn/p/9176602.html)
