---
layout: post
title: 图像黑盒对抗样本攻击，概述
category: 技术
tags: React
keywords: React
---

## 1、什么是对抗样本？
对抗样本在深度学习网络中，向正常样本添加人眼难以察觉的扰动后导致正常样本被分类网络错分的样本。

## 2、威胁模型
1）、攻击者的知识
- 白盒攻击，即攻击者能够获得目标网络的信息，包括模型、参数、训练集。攻击者可以通过目标网络的梯度信息来生成对抗样本。典型的方法如FGSM,PGD等。
- 黑盒攻击，即攻击者无法获得目标网络的信息，仅能获得目标网络的输出，根据输出的不同，黑盒攻击算法又可以分成以下几类：
	- 基于可转移性的攻击，该类攻击中，攻击者通过目标模型的替代模型，然后生成替代模型的对抗样本，使用该样本去攻击目标模型。如>[1],[2]
	- 基于得分的攻击，在该类攻击中，攻击者通过目标网络返回的置信度得分，构建相应的攻击算法。有如>[11],[12]
	- 基于决策的攻击，该类黑盒攻击是我认为黑盒攻击中最具有挑战性、也最有实际意义的攻击，因为攻击者仅能得到目标模型返回的标签类别，没有置信度得分，在实际应用场景中，攻击者通常只能获得图像的类别。因此，我认为该类黑盒攻击最具挑战。
	有如下的工作>[21],[22]
	
2)、攻击者指定
- 非目标攻击，即没有指定攻击的目标，对抗样本的只需不被分类成本类别即达到攻击效果
- 目标攻击，攻击者指定攻击目标，对抗样本被目标模型分类成目标类别才算攻击成功

## 3、性能指标
- 与基线攻击成功率的比较
- 扰动大小
- 查询次数

## 4、一些思考
在《深度学习中的图像分类与对抗技术》一书中，黑盒攻击算法中的PSO算法以及CMA算法，假定条件都是攻击者手里有目标图像，如在介绍PSO中，作者将目标图像设置为粒子的初始值，这样可以保证初值被目标模型识别为目标类别。
通过不断地迭代，最终使目标图像逐渐变化成原始图像。该思想同样存在于文献[23]中。我认为这种假设或许不太合理吧。

## 参考文献
[1]Y. Dong, F. Liao, T. Pang, H. Su, J. Zhu, X. Hu, and J. Li. Boosting adversarial attacks with momentum. In CVPR, 2018. 
[2]Y. Liu, X. Chen, C. Liu, and D. Song. Delving into transferable adversarial examples and black-box attacks. In ICLR, 2017.

[11]P.-Y. Chen, H. Zhang, Y. Sharma, J. Yi, and C.-J. Hsieh. Zoo: Zeroth order optimization based black-box attacks to deep neural networks without training substitute models. In Proceedings of the 10th ACM Workshop on Artificial Intelligence and Security, pages 15–26. ACM, 2017. 
[12]A. Ilyas, L. Engstrom, A. Athalye, and J. Lin. Black-box adversarial attacks with limited queries and information. In ICML, 2018. 

[21]W. Brendel, J. Rauber, and M. Bethge. Decision-based adversarial attacks: Reliable attacks against black-box machine learning models. In ICLR, 2018.
[22]M. Cheng, T. Le, P.-Y. Chen, J. Yi, H. Zhang, and C.-J. Hsieh. Query-efficient hard-label black-box attack: An optimization-based approach. arXiv preprint arXiv:1807.04457, 2018.
[23]