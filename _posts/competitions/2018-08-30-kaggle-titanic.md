---
layout: post
title: Kaggle 入门一步一步记录
subtitle: 
author: Bin Li
tags: [Machine Learning, Competitions]
image: 
comments: true
published: true
---

为了不让手生，纯复现机器学习算法也蛮无聊的，想抽空也看看 Kaggle 上的比赛，当然，刚开始还是拿已经结束的比较经典的比赛来试试看吧！

## 特征选择
我们采用 ANOVA 方差分析的 F 值来对各个特征变量打分，打分的意义是：各个特征变量对目标变量的影响权重。代码如下：

```python
from sklearn.feature_selection import SelectKBest, f_classif,chi2

target = data_train["Survived"].values
features= ['Survived', 'Pclass', 'Sex', 'Age', 'Fare', 'Embarked', 'Family',
       'Name_length', 'Has_Cabin', 'Title']

train = data_train.copy()
test = data_train.copy()

selector = SelectKBest(f_classif, k=len(features))
selector.fit(train[features], target)
scores = -np.log10(selector.pvalues_)
indices = np.argsort(scores)[::-1]
print("Features importance :")
for f in range(len(scores)):
    print("%0.2f %s" % (scores[indices[f]],features[indices[f]]))
```


## Titanic: Machine Learning from Disaster
先通过具体[比赛介绍](https://www.kaggle.com/c/titanic)了解一下题意，我们是要通过给定的数据预测一个人是否最后会在灾难中生存下来。

了解了题目之后，接下来就是要看数据了。

这里新列出一般的比较经典的流程：

1. Data exploration and visualization
    * Explore dataset
    * Choose important features and visualize them according to survival/not-survival
2. Data cleaning, Feature selection and Feature engineering
    * Null values
    * Encode categorical data
    * Transform features
3. Test different classifiers
    * Logistic regression
    * KNN
    * Support Vector Machines (SVM)
    * Naive Bayes
    * Radom Forest


## 数据预处理
首先要认识数据，可以通过 pandas 方便的 `isnull` 来查看空值和异常值，通过 `describe` 来查看数据集的整体情况。
```python
print('Train columns with null values: \n')
print( data1.isnull().sum())
print('-'*10)

print('Test/Validation columns with null values: \n')
print(data_val.isnull().sum())
print('-'*10)

data_raw.describe(include='all')
```

### The 4C (Correcting, Completing, Creating and Converting)
#### Correcting aberrant values and outliers


#### Completing
对于数据填充来说，定性数据（qualitative data）或称之为类别数据的，一般采用众数；而定量数据（quantitative data）或者称之为连续数据的，一般采用平均数，中位数，或者平均数加上随机标准差（randomized standard deviation）。

填充并舍弃一些没有必要的数据后，就准备转换数据类型以符合模型的需要！

#### Converting
将处理空值和异常值搞定的数据中标称数据需要转化成 dummy variables 数值类型的数据，以下是常见的几种转换方式：
* pandas.get_dummies
* sklearn.preprocessing.LabelEncoder
* sklearn.preprocessing.OneHotEncoder

IMPORTANT: When it comes to data modeling, the beginner’s question is always, "what is the best machine learning algorithm?" To this the beginner must learn, the No Free Lunch Theorem (NFLT) of Machine Learning. In short, NFLT states, there is no super algorithm, that works best in all situations, for all datasets. So the best approach is to try multiple MLAs, tune them, and compare them for your specific scenario.


### Python programming
`pivot table` vs `crosstab`



### plt.subplot(231) 中数字的区别
表示要绘制出来的图表一共 $2*3$ 的格子


### plt.barplot
绘制出来的图像上面会有一个竖线，那个是误差棒。


---

首先要认识类型的数据，每种数据应该用什么方式整理，数值型看最大最小值，众数，平均数等，标签型的看一共有多少类，分布是如何的，还有就是variable transformation, normalization and etc.

我们在处理数据的时候，最好将训练数据集和测试数据集放到一起来进行，然后再分开来训练和测试模型。

| Variable<span class="Apple-tab-span" style="white-space:pre"></span> | Definition | Key | Type |
| :-: | :-: | :-: | :-: |
| survival | Survival | 0 = No, 1 = Yes | L |
| pclass | Ticket class | 1 = 1st, 2 = 2nd, 3 = 3rd | L |
| sex | Sex | male, female | L |
| Age | Age  | in years | N |
| sibsp | # of siblings / spouses aboard the Titanic |  | N |
| parch | # of parents / children aboard the Titanic |  | N |
| ticket | Ticket number |  | L |
| fare | Passenger fare |  | N |
| cabin | Cabin number |  | L |
| embarked | Port of Embarkation | C = Cherbourg, Q = Queenstown, S = Southampton | L |
| PassengerId | PassengerId |  | L |
| name |  |  | L |


对于固定类别的属性我们用onehot编码，对于连续数值型的我们就要对其进行标准化。

训练数据集和测试数据集到底是一起操作还是分开操作这得具体看情况了。

分类的问题，分类的结果不能是浮点型，要用整型！

#### 截取数据
Name属性是类似的形式'Braund, Mr. Owen Harris'、'Futrelle, Mrs. Jacques Heath (Lily May Peel)'和'Heikkinen, Miss. Laina'，现在想在Name属性中截取出称呼，可以用如下的方式：
```python
combi['Title'] = combi['Name'].str.extract('([A-Za-z]+)\.', expand=True)
```
`expand=True`使得得到的结果是 Dataframe，否则为 Series。结果就变成了Mr, Mrs和Miss。

### Feature Engineering
Feature engineering is the process of creating new features from existing data.

如果出现多个csv文件的数据的情况，首先我们要把所有数据集中到一起来，才好做特征工程。那么在整合的时候，对于连续数值型数据，一般会对`mean`, `max`, `min`, `sum`做一个group的统计。

How do we know if any of these features are helpful? One method is to calculate the Pearson correlation coefficient between the variables and the TARGET. 

除了皮尔森系数还可以用决策树跑一下特征，然后参考特征的权重值。

因为特征工程而生成数量庞大的新的特征时，注意避免会有 multiple comparisons problem 的问题。

### Auto Feature Engineering
通过这篇[notebook](https://www.kaggle.com/liananapalkova/automated-feature-engineering-for-titanic-dataset)的方式，如果数据只有一个 table 的话，我们可以利用`normalize_entity`函数来创造 dummy tables，我们用featuretools的方式求到一个准确率只有 $72%$ 的结果。

### Manual/Handcrafted Feature Engineering
#### 处理称呼生成新的特征
首先用前面介绍的方式截取出类似 Mr, Mrs 和 Miss 等的称呼，然后将一些不太常用的称呼统一归类到常用的称呼上：
```python
#replacing the rare title with more common one.
mapping = {'Mlle': 'Miss', 'Major': 'Mr', 'Col': 'Mr', 'Sir': 'Mr', 'Don': 'Mr', 'Mme': 'Miss',
          'Jonkheer': 'Mr', 'Lady': 'Mrs', 'Capt': 'Mr', 'Countess': 'Mrs', 'Ms': 'Miss', 'Dona': 'Mrs'}
data_df.replace({'Title': mapping}, inplace=True)
```

### Feature Selection
1. Removing collinear variables
2. Removing variables with many missing values
3. Using feature importances to keep only “important” variables


### Programming
drop data
```python
X = dataset.drop(['PassengerId','Cabin','Ticket','Fare', 'Parch', 'SibSp'], axis=1)
```

**Name**

针对名字来说，具体姓氏名谁好像没有太大的利用之处，我们可以从称呼（salutations）中看到区别，于是我们多加一列，将称呼补充进去。

像Sex和Embarked是字符串类型的类标型数据，那么我们需要对其进行Encoding。

After this, it is then possible to use the get_dummies and get three new columns (Embarked_C, Embarked_Q, Embarked_S) which are called dummy variables (they assign ‘0’ and ‘1’ to indicate membership in a category). The previous "Embarked" can be dropped from X as it will not be needed anymore and we can now concatenate the X dataframe with the new "Embarked" which has the three dummy variables. Finally, as the number of dummy variables necessary to represent a single feature is equal to the number of categories in that feature minus one, we can remove one of the dummies created, lets say Embarked_S, for example. This will not remove any information because by having the values from Embarked_C and Embarked_Q the algorithm can easily understand the values from the remaining dummy variable (when Embarked_C and Embarked_Q are '0' Embarked_S will be '1', otherwise it will be '0').


**pandas.map()怎么用？**

pandas.map() is used to map values from two series having one column same. For mapping two series, the last column of the first series should be same as index column of the second series, also the values should be unique.

### Feature Importance
```python
f,ax=plt.subplots(2,2,figsize=(15,12))
model=RandomForestClassifier(n_estimators=500,random_state=0)
model.fit(X,Y)
pd.Series(model.feature_importances_,X.columns).sort_values(ascending=True).plot.barh(width=0.8,ax=ax[0,0])
ax[0,0].set_title('Feature Importance in Random Forests')
model=AdaBoostClassifier(n_estimators=200,learning_rate=0.05,random_state=0)
model.fit(X,Y)
pd.Series(model.feature_importances_,X.columns).sort_values(ascending=True).plot.barh(width=0.8,ax=ax[0,1],color='#ddff11')
ax[0,1].set_title('Feature Importance in AdaBoost')
model=GradientBoostingClassifier(n_estimators=500,learning_rate=0.1,random_state=0)
model.fit(X,Y)
pd.Series(model.feature_importances_,X.columns).sort_values(ascending=True).plot.barh(width=0.8,ax=ax[1,0],cmap='RdYlGn_r')
ax[1,0].set_title('Feature Importance in Gradient Boosting')
model=xg.XGBClassifier(n_estimators=900,learning_rate=0.1)
model.fit(X,Y)
pd.Series(model.feature_importances_,X.columns).sort_values(ascending=True).plot.barh(width=0.8,ax=ax[1,1],color='#FD0F00')
ax[1,1].set_title('Feature Importance in XgBoost')
plt.show()
```
![](/img/media/15496327661144.jpg)


## 问题总结
* 如果文件太大不能用pd.read_csv来装咋办？
* 对于NaN的数据，我们要怎么办？
    * 通过平均数来填充
    * 众数填充
* heatmap 是起什么作用的？
* t-SNE 是个啥？？
* [bad case 分析](https://blog.csdn.net/login_sonata/article/details/54315273)。

## References
1. ✅ [如何在 Kaggle 首战中进入前 10%](https://dnc1994.com/2016/04/rank-10-percent-in-first-kaggle-competition/)
2. [使用sklearn做单机特征工程](https://www.cnblogs.com/jasonfreak/p/5448385.html)
3. [分分钟带你杀入Kaggle Top 1%](https://zhuanlan.zhihu.com/p/27424282)
4. [Learn Machine Learning](https://www.kaggle.com/dansbecker/learn-machine-learning)
5. [Titanic Competition from Kaggle](https://www.kaggle.com/rochellesilva/simple-tutorial-for-beginners)
6. [Suggest an easy level/start level competition](https://www.kaggle.com/getting-started/12606)
7. [Introduction to Ensembling/Stacking in Python](https://www.kaggle.com/arthurtok/introduction-to-ensembling-stacking-in-python)
8. [All You Need is PCA (LB: 0.11421, top 4%)](https://www.kaggle.com/massquantity/all-you-need-is-pca-lb-0-11421-top-4)
9. [Tutorial: Accessing Data with Pandas](https://www.kaggle.com/sohier/tutorial-accessing-data-with-pandas)
10. [Data Preparation & Exploration](https://www.kaggle.com/bertcarremans/data-preparation-exploration)
11. [Data preparation for building models](https://www.kaggle.com/sirpunch/flatten-de-serialize-credits-data)
12. [Data Preparation for Sberbank](https://www.kaggle.com/optidatascience/data-preparation-for-sberbank)
13. ✅ [Titanic: score over 80% - part 1 data preparation](https://www.kaggle.com/ymlai87416/titanic-score-over-80-part-1-data-preparation)
14. [Titanic: score over 80% - part 2 model selection](https://www.kaggle.com/ymlai87416/titanic-score-over-80-part-2-model-selection)
15. [Best Titanic Survival Prediction for Beginners.](https://www.kaggle.com/vin1234/best-titanic-survival-prediction-for-beginners)
16. [Titanic, a step-by-step intro to Machine Learning](https://www.kaggle.com/ydalat/titanic-a-step-by-step-intro-to-machine-learning)
17. [Introduction to Ensembling/Stacking in Python](https://www.kaggle.com/arthurtok/introduction-to-ensembling-stacking-in-python)
18. [Exploring Survival on the Titanic](https://www.kaggle.com/mrisdal/exploring-survival-on-the-titanic)
19. [Machine Learning Kaggle Competition Part One: Getting Started](https://towardsdatascience.com/machine-learning-kaggle-competition-part-one-getting-started-32fb9ff47426)
20. [Machine Learning Kaggle Competition Part One: Getting Started](https://towardsdatascience.com/machine-learning-kaggle-competition-part-one-getting-started-32fb9ff47426)
21. ✅ [Automated Feature Engineering in Python](https://towardsdatascience.com/automated-feature-engineering-in-python-99baf11cc219)
22. [Introduction to Manual Feature Engineering](https://www.kaggle.com/willkoehrsen/introduction-to-manual-feature-engineering)
23. [Introduction to Feature Selection](https://www.kaggle.com/willkoehrsen/introduction-to-feature-selection)
24. [chrisalbon blog](https://chrisalbon.com/)
25. [Open Machine Learning Course. Topic 1. Exploratory Data Analysis with Pandas](https://medium.com/open-machine-learning-course/open-machine-learning-course-topic-1-exploratory-data-analysis-with-pandas-de57880f1a68)
26. [Understand Your Problem and Get Better Results Using Exploratory Data Analysis](https://machinelearningmastery.com/understand-problem-get-better-results-using-exploratory-data-analysis/)
27. [Kaggle Tutorial: EDA & Machine Learning](https://www.datacamp.com/community/tutorials/kaggle-machine-learning-eda)
28. [Exploratory data analysis - Coursera](https://www.coursera.org/lecture/competitive-data-science/exploratory-data-analysis-zvOJc?authMode=login)
29. ✅ [Feature Engineering](https://jakevdp.github.io/PythonDataScienceHandbook/05.04-feature-engineering.html)
30. [Discover Feature Engineering, How to Engineer Features and How to Get Good at It](https://machinelearningmastery.com/discover-feature-engineering-how-to-engineer-features-and-how-to-get-good-at-it/)
31. [Why Automated Feature Engineering Will Change the Way You Do Machine Learning](https://towardsdatascience.com/why-automated-feature-engineering-will-change-the-way-you-do-machine-learning-5c15bf188b96)
32. [A Hands-On Guide to Automated Feature Engineering using Featuretools in Python](https://www.analyticsvidhya.com/blog/2018/08/guide-automated-feature-engineering-featuretools-python/)
33. ❇️ [Start Here: A Gentle Introduction](https://www.kaggle.com/willkoehrsen/start-here-a-gentle-introduction)
34. [Introduction to Manual Feature Engineering](https://www.kaggle.com/willkoehrsen/introduction-to-manual-feature-engineering/notebook)
35. [当你在应用机器学习时你应该想什么](https://zhuanlan.zhihu.com/p/27345831)
36. [最具价值的50个机器学习应用 (2017年)](https://zhuanlan.zhihu.com/p/33674059)
37. [scikit-learn Machine Learning in Python](http://scikit-learn.org/stable/)
38. [TOP AND BEST BLOG ABOUT ARTIFICIAL INTELLIGENCE MACHINE/DEEP LEARNING](https://www.favouriteblog.com/)
39. [A Tour of Machine Learning Algorithms](https://machinelearningmastery.com/a-tour-of-machine-learning-algorithms/)
40. ✅ [Titanic Data Science Solutions](https://www.kaggle.com/startupsci/titanic-data-science-solutions)

* [Kaggle案例泰坦尼克号问题](https://www.cnblogs.com/nxf-rabbit75/p/9680878.html)
* [Kaggle赛题解析：逻辑回归预测模型实现-bad case](https://cloud.tencent.com/developer/article/1060891)
* [機器學習專案 Kaggle競賽-鐵達尼號生存預測(Top 3%)](https://medium.com/@yulongtsai/https-medium-com-yulongtsai-titanic-top3-8e64741cc11f)
* [Titanic Model with 90% accuracy](https://www.kaggle.com/vinothan/titanic-model-with-90-accuracy)
* [Kaggle项目Titanic挑战最高分，特征工程](http://www.mashangxue123.com/TensorFlow/2743066386.html)


--- 
1. [EDA To Prediction (DieTanic)](https://www.kaggle.com/ash316/eda-to-prediction-dietanic)
2. ✳️ 对于调参和选择模型可以重点参考：[A Data Science Framework: To Achieve 99% Accuracy](https://www.kaggle.com/ldfreeman3/a-data-science-framework-to-achieve-99-accuracy)
3. [A Comprehensive ML Workflow with Python](https://www.kaggle.com/mjbahmani/a-comprehensive-ml-workflow-with-python)
4. [Kaggle泰坦尼克号船难--逻辑回归预测生存率](https://www.jianshu.com/p/bbfbdedc3c1c)
5. [参考文献4的代码](https://www.kaggle.com/beleev/titanic)
6. ✳️ [Machine Learning Kaggle Competition Part Two: Improving Feature engineering, feature selection, and model evaluation](https://towardsdatascience.com/machine-learning-kaggle-competition-part-two-improving-e5b4d61ab4b8)