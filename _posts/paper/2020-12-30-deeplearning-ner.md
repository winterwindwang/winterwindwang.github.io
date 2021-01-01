---
layout: post

title: 深度神经网络实体词识别综述

category: 文献阅读

tags: Paper reading

keywords：deep learning, NER
---

## A Survey on Deep Learning for Named Entity Recognition  (2020, IEEE TKDE)
**摘要** Named entity recognition(NER)是一个任务，用于从预定义预定义语义类型（例如人、位置、组织、组织等）的文本中识别提及的刚性指示符。NER作为很多自然语言应用的基础，如问答、文本总结、机器翻译等。**早期纯人工在设计领域相关特征与规则**的NER系统取得了巨大的成功。最近，将通过非线性处理的连续实值表示与语义组合的深度学习引入NER系统，取得了SOTA的性能。在本篇文章中，作者全面回顾了基于深度学习的NER技术。通过首先回顾NER资源，包括NER标签文集与代表性的NER工具。然后，基于以下三个维度系统地对现在的工作进行分类：输入分布表示、内容编码、标签解码。接着，调查了在新的NER问题设置与应用中的深度学习技巧的大多数代表性工作。最后，提出了NER系统面临的挑战与指出了该领域的未来方向。

**Introduction** "Named Entity"(NE)一词首次出现在MUC-6（第六届信息理解大会）[10][]，并作为在文本中识别组织名、人名、地理位置名，目前也用于识别时间与百分比表达。从MUC-6发布以后，研究者对NER的研究兴趣逐渐增加，并发布了不同的数据集。尽管如此，Petasis等人[15][]将NE的定义限制在：“命名实体应该是一个专有名词，用于代表某些事物或某些人”。语料库中存在大量的专有名词可以证明这一限制。而Nadeai和Sekine等人[1][]认为“命名”一词将任务限制为仅一个或多个刚性指示符代表所指对象的实体。刚性指示符在文献[16][]中定义，包括专有名词以及如生物物种或物质的自然种类。尽管对NE定义存在差异，但研究者对可识别的NE类型达成了共识。NE的通常可以分成两种：通用NEs（人类或地点）与特定领域NEs（蛋白质，基因等）。该文主要调查了英语中的通用NEs。

NER中的技术可以分为以下四种：

+ Rule-based（基于规则）方法，不需要标准数据，仅依赖人工设计的规则
+ Unsupervised learning（无监督学习）方法，不需要人工标记训练集，依赖于无监督学习算法
+ Feature-based supervised learning（基于特征的监督学习）方法，依赖精心设计的特征工程，并使用监督学习算法
+ Deep learning based(基于深度学习)方法，以端到端的方式自动地从原始输入中发现用于分类或检测地表示。

**Background**

##### NER

一个命名实体是一个单词或短语，能从拥有一组具有类型属性的其他术语中清晰地识别出一术语[28][]。NER是在文本中定位或分类命名实体到预定义实体类别中的过程。

正式地，给定一个令牌序列$s=<w_1,w_2,...,w_3>$,NER输出一个元组集合$<I_s,I_e,t>$,其中每个都是一个在$s$中的命名实体。$I_s\in[1,N]$与$I_e\in[1,N]$分别是一个命名实体开始和终止索引;$t$是预定义类别集合中的实体种类。具体如下图所示

![image](https://winterwindwang.github.io/assets/img/2020-12-30-dlner_reviews_01.jpg)

NER是一个非常重要的预处理步骤，可以用于各种不同下游应用。

##### NER资源：数据集与工具

高质量标注是模型学习与评估的关键。下图列举了一些广泛使用的数据集。2005年以前的数据集主要由少量实体的标注新闻文章构成，适合粗粒度的NER任务。在那之后的任务就更加广泛了。标记的类别更加大（HYENA能够达到505个）。

![image](https://winterwindwang.github.io/assets/img/2020-12-30-dlner_reviews_02.jpg)

现有的NER工具如下图

![image](https://winterwindwang.github.io/assets/img/2020-12-30-dlner_reviews_03.jpg)

##### NER评估指标

NER系统一般通过比较系统输出与人类的标记进行评估。可以通过完全匹配或宽松匹配来量化比较。NER从本质上可以分成两个子任务：边界检测与类别识别。在“确切匹配评估”中，正确地识别样例需要同时正确地识别其边界和类型。更具体地，FP,FN,TP被用于计算Precision, Recall, F-score。

+ False Positive(FP): 实体是由NER系统返回，但没有出现真实实体（ground truth）集中。
+ False Negative(FN): 实体不是由NER系统返回，而是出现在真实实体中。
+ True Positive(TP): 实体由NER系统返回，同时出现在真实实体中。

Percision表示系统结果被正确识别的比例。Recall表示系统正确识别的总实体数比例。F-score衡量了Percision与Recall二者。
$$
Precision = \cfrac{TP}{(TP+FP)} ~~~~~~  Recall = \frac{TP}{TP+FN}~~~~ F-score=2*\frac{Precision x Recall}{Precision + Recall}
$$
此外，在多实体类型中macro-averaged F-score和micro-averaged F-score同样会被作为衡量性能

+ Maro-averaged F-score独立地在不同实体类型上计算F-score，最后进行平均
+ Mirco-averaged F-score在所有实体类型中所有独立地对TP,FP,FN进行求和，最后计算F-score

MUC-6定义了一个宽松匹配评价：如果为实体分配了正确的类型而不管其边界如何，只要正确的类型与基本真理边界重叠，就可以认为该类型正确； 无论实体的类型分配如何，正确的边界都将归功于此。最近的研究中复杂的评估方法使用的不广泛。

##### NER传统的方法

###### 基于规则的方法

生物医学领域，Hanisch等人[46][]提出了ProMiner, 利用预处理代名词字典来识别生物本文中的蛋白质提及与蛋白质基因。Quimbaya等人[47][]提出基于字典的NER方法，用于识别电子健康病历。实验结果表明能够达到很好的Recall，并保证对precision的影响也很小。基于规则的系统在词法很详尽的时候，可以工作的很好。但是由于特定领域规则与不完整字典，高精度与低召回的现象通常出现在这些系统中，并且使得这些系统不能够迁移到其他系统。

###### 无监督学习

典型的无监督学习就是聚类[1][]。基于聚类的NER系统从基于上下文相似的聚类组中提取命名实体。Collins等人[54][]观察到使用无标签数据减少了监督需求，并提出了两种无监督算法对命名实体进行分类。KNOWITALL等人[9][]等人利用一个预测命名集合作为输入，从一小套通用提取模式中引导其识别过程。zhang和Elhadad[44][]提出一个无监督方法以提取生物文本中的实体。不同于监督，他们建模重新排列了术语，文集统计（如逆文档频率与上下文向量）和浅层语法知识（如名词短语分块）。

###### 基于特征的监督学习

给定标注数据样本，仔细设计特征以表示每个训练样本。然后使用机器学习算法来识别未见数据中类似模式。在监督NER系统中，特征工程非常关键。特征向量表示是在文本上的抽象，即一个单词是由一个或多个布尔类型、数值类型或者名义值表示。基于这些特征，有很多机器学习算法如，隐藏马尔可夫模型[69][]，决策树[70][]，最大交叉熵模型[71][]，支持向量机[72][]，条件随机场[73][]。Bikel等人[74][],[75][]提出了首个HMM-based NER模型，为IdentiFinder，以识别或分类名称、数据、时间表达、或者数值量。此外，Szarvas等人[76][]开发了一个基于C4.5决策树和AdaBoostM1学习算法的多语言NER模型。主要的贡献在于提供了一个机会在不同特征子集上训练多数独立决策数分类器，然后使用多数投票的方式得到最后的决策。Borthwick等人[77][]通过应用最大熵理论，提出“最大熵命名实体”(MENE)。该方法能够利用额外的李赛范围知识源来进行决策标注。相似的最大熵方法有[78][]-[80][]。SVM在预测一个实体类别时，无法考虑“邻居”词。CRFs考虑了上下文。McCallum和Li[82][]等人提出了一个基于CRF特征感应的NER方法。实验结果表明，在CoNLL03的英语数据集上，能达到84.04%的F-score。Krishnam和Manning等人[67][]提出一个基于耦合CRF分类器的两阶段方法。第二个阶段的CRF利用了第一个CRF输出的潜在表示。CRF在NER中广泛的使用，如生成物文本[58,83][],推特[84,85][],化学文本[86][]。

##### 使用深度学习技术的NER

深度学习的关键优势在于其由向量表示和神经元处理驱动的表示学习与语义组合能力。这使机器可以获取原始数据，并自动发现分类或检测所需的潜在表示和处理。

作者将从以下几个方面介绍

**句式**

1. We do not claim this article to be exhaustive or representative of all NER works on all language. 我们不敢自称本文对所有语言中的NER都进行了详尽的调差或表示。



**词汇**

1. significant percentage of 大量的
2. natural kind term 自然种类
3. domain-specific 特定领域
4. off-the-shelf 现成的
5. enlighten 开导
6. coarse-grained 粗粒度
7. relaxed-match evalution 宽松匹配评估
8. syntactic-lexical   句法词汇
9. be cast to 被投射到  e.g. NER is cast to a multi-class classification