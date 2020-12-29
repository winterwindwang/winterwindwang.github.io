---
layout: post
title: Kaggle 比赛教程（三）
category: 技术
tags: React
keywords: React
---

## 数字信息处理 Numerai Competition
每周的数据可能是相同的，即上一周的数据添加噪声后变成这一周的数据。因此通过特征的相减然后再sort或许会有新的发现

## 验证及过拟合 Validation and overfitting

1.We will understand the concept of validation and overfitting
2.We will identify the number of splits that should be done to establish stable validation
3.We will break down most frequent ways to repeat train test splits
4.We will discuss most often validation problems

### Conclusion
1.Validation helps us evaluate a quality of the model
2.Validation helps us select the model which will perform best on the unseen data
3.Underfitting refers to not capturing enough patterns in the data
4.Generally, overfitting refers to 
  a.capturing noise
  b.capturing patterns which do not generalize to test data
5.In competitions, overfitting refers to 
  a.low model's quality on the test data, which was unexpected due to validation scores
   
## 验证集策略 Validation Strategies
1.Holdout
ngrougps = 1
sklearn.model_selection.ShuffleSplit
2.K-fold  
sklearn.model_selection.Kfold   every sample validate once
3.Leave-one-out
ngroups = len(train)
sklearn.model_selection.LeaveOneOut

Stratification is useful for:
a.Small dataset
b.Unbalanced dataset
c.Multiclass classification
Stratification preserve the same target distribution over different folds


## 数据分割策略 Data splitting strategies
问题：如果要预测下一个月得销售额，那么数据集该如何分割呢？
方案一：找某个时间点，在该时间点的数据设为训练集，该时间点后的称为验证集，这可能会导致该模型仅在临近值时有效，预测未来可能无效
方案二：验证和测试交替进行， 该模型可能对邻近值无效，对于预测下一个时间点有效

1.Previous and next target values
2.Time-based trend

### Important outcome
Different splitting strategies can differ significantly
1.in generated features
2.in a way the model will rely on that features
3.in some kind of target leak

#### Splittign data into train and validation
1.Random, rowwise (most command way)
2.Timewise   (Moving window)
3.By id  (对全新用户推荐音乐，利用用户得历史数据，如有多少歌曲的用户上周听了某些音乐。
4.Combined
5.Logic of feature generation depends on the data splitting strategy
6.Set up your validation to mimic the train/test split of the competition


## 验证时出现的一些问题 Problems Occuring During validation
Validation problems
a. Validation stage
Cause of different scores and optimal parameters
1.Too little data
2.Too diverse and inconsistent data
We should do extensive validation
1.Average scores from different KFold splits (5 is enough)
2.Tune model on one split, evaluate score on the other
comptition:
1).Liberty Mutual Group
Property Inspection Prediction
2).Santander Customer Satisfaction

b. Submission stage
1)Problems
LB score is consistently higher/lower that validation score
LB score is not correlated with validation score at all
0.We may already have quite different scores in Kflod other reasons for problems;
1.too little data in public leaderboard
2.train and test data are from different distribution (Quora Question Pairs)
	Mean for train: Calculate from the train data
	Leaderboard probing  
	当验证机与测试集的正负样本数量恰好相反时，验证集的正负样本数据应与测试集一致
	(competitions: Data Science Game 2017 Qualification phase: Music recommendataion; CTR prediction task from EDA)
Cause of the validation problems:
	too little data in public leaderboard
	incorrect train/test split
	different distributions in train and test

c. Except LB shuffle beacuse of 
1).Randomness 
competition:two sigma, liberty mutual
2).Little amount of data
competition: tab food investments
3).Different public/private distribution
competition: rossmann

### Conclusion
1.If we have big dispersion of scores on validation stage, we should do extensive validation
a).Average scores from different KFold splits
b).Tune model on one split, evaluate score on the other
2.If submission's score do not match local validation score, we should 
a). Check if we have too little data in public LB
b). Check if we overfitted 
c). Check if we chose correct splitting strategy
d). Check if train/test have different distributions
3.Except LB shuffle beacuse of
a).Randomness
b).Little amount of data
c). Different public.private distributions

### Summary of Validation topic
1.Defined validation and its connection to overfitting
2.Described common validation strategies
3.Demonstrated major data spltting strategies
4.Analysed and learn how to tackle main validation problems



















