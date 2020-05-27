---
layout: post
title: Kaggle 课程
category: 技术
tags: React
keywords: React
---

##  探索性数据分析 EDA exploratory data analysis

1. Exploratory Data Analysis (EDA): What and why?
visualization ----  idea        idea ------- visualization
2. Things to explore
3. Exploration and visualization tools
4. (A bit of) dataset cleaning 
5. Kaggle competition EDA

## 建立数据的直觉 building intuition about data
1. Getting domain knowledge
了解数据，知道数据是关于什么的
--it helps to deeper understand the problem
2.Checking if the data ais intitive
错误的值进行手动调整，如出现年龄336， 应该变成33或者36
--And agrees with domain knowledge
3.Understanding how the data was generated
It is crucial to understand the generation process to set up a proper validation scheme
-- As it is crucial to set up a proper validation


## 处理隐藏数据信息 Exploring Anonymize data
1. What is anonymized data ?
哈希值的简化
2. What can we do with it?

a. Explore individual features
--- Guess the meaning of the columns
 Guess  the true meaning of the feature
---  Guess the types of the column
 Each type needs its own preprocessing
df.dtypes
df.info()
x.value_counts()
x.isnull()

b. Explore feature relations
--- Find relations between pairs
--- Find feature groups

## 可视化 Visualizations
1.Visualizations tools to ...
-- Explore individual features
  Historgrams 不适用于所有的值都是唯一的或者很多重复的值，应该用不同的图来表示，验证是否合理
              峰值精确地位于该特征的平均值  plt.hist(x)
  Plots  横坐标 row index 纵坐标 ：feature value  plt.plot(x, '.')  plt.scatter(range(len(x)), x, c=y) (index versus value)
  Statistic    df.describe()   x.mean(), x.var(), x.value_counts(), x.isnull()
--  Explore feature relations
  Scatter plots    plt.scatter(x1, x2)  两种特征下， 类别的分布
				若出现x1+x2=1这样的特征，如果使用基于树的模型，则可以用特征之间的差值或者比值作为一个新的特征
				pd.scatter_matrix(df)
				df.corr(), plt.matshow(...)  若画出的corr图很乱，则可以使用K-mean 先聚类，在画图
				df.mean().plot(style='.')
				df.mean().sort_values().plot(style='.')
  Correlations plots
  Plot(Index vs feature statistics)
	a.Pairs
	  -- Scatter plot, scatter matrix
	  -- Corrplot   对于理解特征相似性具有重要的作用
	b. Groups
	  -- Corrplot + clustering
	  -- Plot(index vs feature statistic)


## 数据处理与审查
1. Dataset cleaning
a.constant features
b.Duplicated features
	traintest.nunique(axis=1) == 1
    如果某列特征在训练集和测试集上的都相同， 则不要那列数据
	如果某列特征在训练集上相同，在测试集上不同， 此时考虑是否需要重建该特征
	若有两个特征的值完全一致，则可以只用其中的某列数据
	traintest.T.drop_duplicates()
	for f in categorical_feats:
	    traintest[f] = raintest[f].factorize()

2.Other things to check
a.Duplicated rows
 -- check if same rows have same label
 -- Find duplicated rows, understand why they are duplicated.
b.Check if dataset is shuffled
  plot rowindex and mean, and after shuffling rolling_mean()
  
  
## Springleaf 真实竞赛数据处理



