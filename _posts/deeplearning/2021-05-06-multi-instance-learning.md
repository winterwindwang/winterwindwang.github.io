---
layout: post
title: 多示例学习
category: deeplearning
tags: 多示例学习
keywords: 机器学习，多示例学习 
---

> 在机器学习中，多示例学习（MIL）是由监督式学习演变而来的。相较于输入一系列被单独标注的示例，在多示例学习中，输入的是一系列被标注为“包”，每个“包”都包括许多示例。

举一个二元分类的简单的例子，当包中的所有示例都是负例时，这个包会被标注为负包。另一方面，当包中至少含有一个正例时，这个包会被标注为正包。当收到一系列被标注的包时，机器试着去：（1）归纳出一个类别概念以便正确标注个别示例。（2）在归纳之外学习怎样去标注一个包。

就图像分类举一个例子：给出一个图像，我们想要根据图像的画面内容来确定它的目标类别。比如，当图像同时包括了“沙子”和“水”时，图像的目标类别可能是“海滩”。在多示例学习中，图像被描述称一个包：$X={X_1,...,X_N}$，其中每一个$X_i$均是从图像中相应第i个区域中提取出来的特征向量（我们称之为示例），N是图像被分割出的区域（示例）个数。当图像包同时包含“沙子”区域示例和“水”区域示例时，这个包会被标注成正例（“海滩”）。

**起源**：多示例学习这一名称最初是由Dietterich（1997）年提出的，但是类似更早的研究，由Keeler（1990）的手写数字识别。最近关于多示例学习的回顾文献包括Amores(2013)，对于不同的范式，它提供了一个广泛的回顾和比较研究。还有Foulds（2010），对于文献中不同的范式所提出的不同假设，它提供了一个全面的回顾。

运用多示例学习的几例子：

+ 分子活性
+ 钙调素结合蛋白结构位点的预测
+ 对于选择性剪接异构体的预测作用
+ 图像分类
+ 文本或文档分类

数不清的研究都在作者促使传统分类技术，诸如**支持向量机**或是**提升方法**，适用于多示例学习环境的工作。

### 定义

MIL的数据集的数据的单位是Bag，以二分类为例，一个bag中包含多个instace，如果**所有**的instance都被标记为negative，那么这个包就是negative，反之这个包为positive。

设Y为包X的label，$X={X_1,...,X_N}$，每个示例$X_i$对应一个标签$Y_i$，则包的标签可以表示为：

$$Y=\begin{cases} +1 & if ~\exist y_i:y_i=+1; \\ -1 & if ~  \forall y_i:y_i=-1. \end{cases}$$

**tasks:**

1. Classification
2. Regression
3. Ranking
4. Clustering 

以上学习目标可以分为bag维度的和instance维度的。

> 通俗解释：设想有若干人，每个人手上有一个钥匙串（bag），串有若干个钥匙（instance）。已知某个钥匙串能否打开特定的一扇门（training set）。我们的任务是要学习到那一串钥匙串能打开这扇门，以及哪个钥匙能打开这扇门。

**MIL主要有四个特点**：

+ **task**
  + instance-level
  + bag-level
+ Bag composition
  + Witness rate
  + Relation between instances
+ Data distribution
  + multi-concept
  + non-representative negative distribution
+ Label ambiguity
  + Noise
  + Different label spaces

### CV里的应用

使用MIL，我们可以利用爬虫技术所爬到的图片自带的tags进行训练，而不是针对每个部分进行标注的图片。

**MIL为什么有用**：包的类别不能由单独某个Instance学习到，但是可以通过多个instance相互作用学习到。如要检测沙滩，必须要同时检测到沙和水。

#### Computer Aided Diagnosis and Detection

弱标签（比如说，对某个全切片病理学图像，而不是具体哪一个区域是疾病区域）在医学诊断中更常见。一个X光或WSI图像中，所有区域都是正常的，那么该bag就是健康的，只要有一个区域被显示出有疾病，那么该bag就是不健康的。非常符合MIL框架的定义。（Bag相当于一个WSI图像，instance相当于WSI图像中各个块）。

在进行包分类的医学图像处理任务中，MIL分类器利用到了co-occurrence和instance-structure的信息，比一般的简单学习表现得要好一些。

示例级的任务在医学图像处理中不太常见，因为示例级的标签代价很高。这也是比较锚段的一点，使用MIL框架的motivation本来就是因为缺少示例级的标签。但是这里也能作为日后的研究方向，这其实也是整个医学图像处理中比较坑的一点，有能力去做标注的人本来就很少。

总之CAD方向的挑战还是很多，真正实际应用时受到很多方面的影响，比如病人的年龄、性别、体重等。而且疾病一般来说是分为不同阶段的，但是有些算法[2]把某种疾病的全部阶段都作为同一个positive class

知乎作者认为MIL这种框架还是比较好编故事的，比较符合现实世界的情况（weak labels）。这几年MIL也算是一个研究热点，还是有很多研(guan)究(shui)方向可以挖掘的。

#### 参考资料

[1]:https://zh.wikipedia.org/wiki/%E5%A4%9A%E7%A4%BA%E4%BE%8B%E5%AD%A6%E4%B9%A0

[2]: Carbonneau M A, Cheplygina V, Granger E, et al. Multiple instance learning: A survey of problem characteristics and applications[J]. Pattern Recognition, 2018, 77: 329-353.

[3]: https://zhuanlan.zhihu.com/p/299819082