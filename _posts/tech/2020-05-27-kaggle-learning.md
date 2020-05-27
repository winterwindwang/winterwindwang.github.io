---
layout: post
title: Kaggle 比赛教程
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
