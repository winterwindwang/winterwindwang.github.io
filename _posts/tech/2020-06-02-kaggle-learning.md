---
layout: post
title: Kaggle 比赛技巧
category: 技术
tags: React
keywords: React
---

## 基本数据泄漏 Basic Data Leaks
### Data leak
1.How bad it is ?
2.Whats the public opinions?
3.To exploit or not to exploit

### Contents 
1.Leakage types and examples
2.Competition specific. Leaderboard problem
3.Concrete walkthroughs


### Leaks in time series
1.Split should be done on time
	a).In real life we don't have information from future
	b).In competitions first thing to look: train/public/private
	split, is it on time?
2.Even when split by time, features may contain information about future.
	a).User history in CTR tasks
	b).Weather

### Unexpected information
1.Meta data
2.Information in IDS
3.Row order  
competitions:Dato, TalkingData, CAT
	
## 排行榜探测与不易察觉的数据泄漏 Leaderboard probing and Examples of Real Data Leaks
1.Types of LB probing
2.Categories tightly connected with 'id' are vulnerable to LB probing
	a).Company of user in RedHat competition
	b).Year, Month, Week in WestNile competition	
C:Truly Native
1.Predict whether the content in an HTML file is sponsored or not
2.Data leak in archive dates. But is it all?
	a).Data collection
	b).Date proxies
C:Expedia
1.Predict hotel group a user is going to book
2.Data leak in distance feature
3.Reverse engineering true coordinates
C:Pairwise tasks
1.Data leakage in item frequencies
2.Similarities from connectivity matrix

## Expedia 比赛例子 Expedia chanllenge	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	