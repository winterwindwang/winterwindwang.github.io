---
layout: post
title: 【对抗样本（十三）】【ICCV2021】On the Robustness of Vision Transformers to Adversarial Examples
category: 文献阅读
keywords: vision transformer robust to adversarial examples
tags: 视觉transformer天然地对对抗样本鲁棒
---

关键词: 研究了视觉transformer对对抗样本地鲁棒性; 白盒攻击对ViT的影响很大。

基于ViT和BiT方法集成的对防御方法仍然容易受到对抗样本的攻击。

---

## 摘要

虽然CNN中的对抗攻击已经得到了彻底的研究，但是Vision Transformer中的还没有。在本文，作者研究了Vision Transformer对对抗攻击的鲁棒性。transformer安全的分析分成了以下三个方面：

1. 首先，在白盒和黑盒攻击设置下测试了transformer
2. 其次，研究了transformer在CNN和transformer之间对抗样本的迁移性。作者表示对抗样本在CNN和transformer之间不存在迁移性。基于这个发现，作者分析了CNN和transformer简单的集成防御的安全性。

并创建了一种新的攻击，自注意力混合梯度攻击，作者表明集成防御在这种白盒攻击下无效。然而，在黑盒对抗攻击下，作者指出集成防御模型可以在不牺牲准确率的情况下，达到较好的鲁棒性。（黑盒攻击跟你模型鲁棒性什么关系？黑盒攻击无效？）作者的分析在6种白盒设置下，两种黑盒攻击设置下进行。对多个transformer网络（ViT， BiT和在cifar-10、CIFAR-100、ImageNet数据上的CNN结构）

## 相关结论

Vision Transformers和Big Transfer Models之间的迁移性非常低，但是相同模型族之间具有迁移性，比如说针对BiT生成的对抗样本，可以攻击不同的BiT结构，但是无法攻击ViT或resnet结构。模型族之间具有较高的相似性。

+ 模型集成中，大多数投票是很弱的评估对抗样本的方法，因为不是所有的分类器都必须被欺骗，随着分类器数量的上升，结果反而变差。

+ 替代方法之一绝对共识，如果所有的分类器不同意某个相同类别标签，那么该样本被标记为对抗样本。绝对共识移除了大多数投票中的效果递减现象。在该种策略中，大多数干净样本都会被标记为对抗样本。
+ 因此，作者采用在所有集成分类器结果中随机选择的方式。在随机选择中，单个模型被选择并且被用于评估输入。
