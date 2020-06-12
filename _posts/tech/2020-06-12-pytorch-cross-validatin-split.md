---
layout: post
title: pytorch中数据集的分割，KFold验证
category: 技术
tags: React
keywords: React
---

## pytorch的Dataset, DataLoader, SubsetRandomSampler + Sklearn的KFolder
```
from torch.utils.data import DataLoader, Subset, SubsetRandomSampler
from sklearn.model_selection import KFold
train_dataest = CustomerDataset(...)
# define a KFold with cross_validation 5
FK_split = KFold(n_split = 5, shuffle=True, random_state=42)
for train_idx, valid_idx in FK_split.spilt(train_dataest):
	# First solution: use sampler
	# sampler to get indices for cross validation
	train_sampler = SubsetRandomSampler(train_idx)
	valid_sampler = SubsetRandomSampler(valid_idx)
	train_dataloder = DataLoader(train_dataest, batch_size=batch_size, sampler=train_idx, shuffle=False)
	valid_dataloder = DataLoader(train_dataest, batch_size=batch_size, sampler=valid_sampler, shuffle=False)
	
	# Second solution: use Subset(not evaluation)
	train_data = Subset(train_dataest, train_idx)
	valid_data = Subset(train_dataest, valid_idx)
	train_dataloder = DataLoader(train_data, batch_size=batch_size, shuffle=True)
	valid_dataloder = DataLoader(valid_data, batch_size=batch_size, shuffle=True)
	
```
在不修改Dataset的情况下，提供了两种分割数据集以及KFold验证的方法。主要思路是使用sklearn中的SKold分割出训练和验证的indices，
然后使用torch的SubsetRandomSampler或Subset两种方式构建Dataloader，第二种方法没有验证。