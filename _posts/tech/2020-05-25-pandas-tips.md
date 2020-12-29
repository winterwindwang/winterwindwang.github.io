---
layout: post
title: Pandas小技巧
category: 技术
tags: React
keywords: React
---
## 按天统计不同类别商品销售占比
![avatar](https://winterwindwang.github.io/assets/img/2020-05-25-data1.jpg)

工作中，这种问题还是比较多的。通过分析比较每天不同商品的销售额占比，再结合利润率情况，
就可以知道公司下一步应该主推什么才能盈利，哪些商品的占比应该提高或者降低。
或者也可以反映出哪些种类的商品更赚钱，对公司盈利层面更有帮助。

```
# Python coding
>>> df = pd.read_csv('./sales.csv')
>>> df.set_index('日期',inplace = True)

>>> (100*df['销售额']/df['销售额'].sum(level = '日期')).round(2).map('{}%'.format)
 
日期
2020-05-01    20.26%
2020-05-01    26.35%
2020-05-01    25.74%
2020-05-01    27.65%
2020-05-02    11.92%
2020-05-02    44.09%
2020-05-02    25.76%
2020-05-02    18.22%
Name: 销售额, dtype: object

>>> df['占比'] = (100*df['销售额']/df['销售额'].sum(level = '日期')).round(2).map('{}%'.format)
>>> df.head(8)
```

## pandas 实现group_concat功能
![avatar](https://winterwindwang.github.io/assets/img/2020-05-25-pandas_need.jpg)

[image](pandas_need.jpg)
一般情况下，销售表的生成方式都是产生记录后，一条一条插入的。我们看到的表是一行一行的。
但是，总有一些奇葩的需求，想把表横过来看看。所谓的换个角度看世界，我依然很穷！

```
>>> df.groupby('顾客').apply(lambda x:'-'.join(x['商品']))

>>> df.groupby('顾客').apply(lambda x:'-'.join(x['商品'])).reset_index().rename({0:'商品'},axis=1)
>>> df_gb = df.groupby('顾客').apply(lambda x:'-'.join(x['商品'])).reset_index().rename({0:'商品'},axis=1)
>>> df_gb

>>> df_gb['商品'].str.split('-',expand = True）
>>> df_gb[['商品1','商品2','商品3']] = df_gb['商品'].str.split('-',expand = True)
>>> df_gb
>>> df_gb.drop('商品',axis = 1,inplace = True)
>>> df_gb
```