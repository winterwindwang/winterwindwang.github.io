---
layout: post
title: 【论文阅读】【ICLR2019】Imagenet-Trained Cnns Are Biased Towards Texture; Increasing Shape Bias Improves Accuracy And Robustness
category: 文献阅读
keywords: CNN property, shape bias improve acc and rubst
tags: imagenet上训练的CNN偏向纹理
---

关键词：ImaegNet上训练的CNN学到更多的局部纹理信息；使用Stylied-ImageNet微调CNN网络可以增加网络的泛化性和鲁棒性。

---

## 摘要

CNN常被认为通过增加对目标形状的复杂表示学习来识别物体。最近的研究表明图像纹理可能是更重要的因素。我们在这里通过在具有纹理形状线索冲突的图像上评估 CNN 和人类观察者来对这些相互矛盾的假设进行定量测试。结果显示，在ImageNet上训练的CNN**强烈地偏向识别纹理**而不是**形状**，这与人类的行为具重大差异，并因此揭露了分类策略上重要的差异。随后，作者在相同的网络结构上使用ImageNet训练的预训练模型在Stylied-ImageNet上进行微调，可以使网络学习到更多形状信息。这在我们控制良好的心理物理实验室环境中为人类行为表现提供了更好的拟合（九个实验，总共 97 个观察者进行了 48,560 次心理物理试验），并带来了许多意想不到的好处，例如提高了物体检测性能和以前未见的鲁棒性。 广泛的图像失真，突出了基于形状的表示的优势。

![image-20211107182344584](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211107182344584.png)

## 结果

#### 人类和CNN 在  偏向纹理 vs 偏向形状

1.  由于灰度图仍然保存纹理和形状信息，因此可以被识别得很好。
2. 当目标是以黑色轮廓的形式呈现时，CNN的准确率远低于人类。这在目标边缘图像（canny结果）时更加明显，这表明人类可以很好的处理缺少纹理信息时的情况。
3. 可以看出CNN在处理领域偏移时的效果很差，由自然图像（训练过）到结构图像（没训练过），图像的统计信息发生巨大变化时。

#### 克服CNN的纹理偏差

1. 在ImageNet（IN）上训练的CNN在Stylied-ImageNet(SIN)测试，效果很差。反之，效果要好很多
2. 增加ResNet50的感知域无助于提升上述结果。
3. SIN训练的ResNet50显示出更强的形状偏向。

![image-20211107170520175](C:\Users\wdh\AppData\Roaming\Typora\typora-user-images\image-20211107170520175.png)

![image-20211107171143973](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211107171143973.png)

#### 形状偏置表示的鲁棒性和准确率

提升形状偏置，从而来偏移表示，会影响CNN的性能或鲁棒性吗？为此，考虑两种训练模式：

+ 在SIN和IN上联合训练
+ 在IN训练的模型上，联合fine-tuning SIN和IN。Shape-ResNet

实验结果如下图所示

![image-20211107171132450](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211107171132450.png)

+ SIN可以是IN很好的数据增强数据集，可以在不改变模型结构的情况下，提升模型的性能。
+ 迁移学习，将上述两个数据集上训练得到的网络作为Faster RCNN的backbone，在VOC上进行训练。结果表示基于形状的表示更有益于网络目标检测任务。

鲁棒性失真

![image-20211107171532465](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211107171532465.png)

SIN训练的ResNet50达到了人类的标准，尽管在训练时未见过该样本，也能表现得很好。融合SIN训练网络，可以提高模型得泛化性。

## 讨论

+ ImageNet上训练的CNN倾向于根据局部纹理而不是全局形状来进行分类。与纹理相关的颜色和物体的大小对CNN的检测性能影响不大，这表明了如纹理这样的局部线索在CNN识别时的重要性。
+ 在没有纹理信息的数据上，IN上训练的CNN表现得很差。
+ 作者得发现解释了为什么在合成纹理图像上训练的分割数据集可以迁移到自然图像或视频。
+ 引入了SIN数据集，通过风格迁移移除局部线索，迫使网络学习纹理以外的信息。
+ 使用基于形状偏置的CNN预训练模型作为backbone， 目标检测任务可以表现得更好。
+ 在SIN微调的CNN（SIN 预训练网络），具有很好的鲁棒性，可以抵抗各种噪声。

