---
layout: post
title: 读论文，记笔记
category: 生活
tags: Essay
keywords: 生活,2020
description: 
---


## 论文英语句子笔记
### Article: Cooling-Shrinking Attack Blinding the Tracker with Imperceptible Noises
1. <font color=#008000>Although several works have focused on attacking</font> image classifiers and object detectors,
<font color=#008000>an effective and efficient method for attacking</font> single object trackers of any
target **in a model-free way** remains lacking.

2. Research on adversarial attack is beneficial to understand deep neural networks and design robust models.

3. The latter method is faster than the former method because only one-time forward propagation is needed for each attack after training. 

4. In the recent year, adversarial attack has become a popular topic and has extended from image classification to more challenging tasks,
such as object detection [35, 31] and semantic segmentation [35].

5. The latter type is generally faster than the former type because the operation for transforming an image to an adversarial one does not need to be
repeated.

6.  AdvGAN [34] is the first work to generate adversarial examples with GAN, which can run efficiently during
the inference phase.

7. UEA achieves comparative performance but is much faster than DAG, because only one-time forward propagation is needed during the inference phase
after training.(基于GAN的对抗样本生成技术优于其他方法的解释)

8. Inheriting similar ideas （继承自该思想） 与 Inspired by 。。。一致

9. 将自己方法时，首先用一句话指出我们的工作，或提出的方法；然后再指出提出该方法的目标；最后提出为了实现该目标，做了的工作。
In this work, we propose an adversarial perturbationgenerator for deceiving the SiamRPN++ tracker. The goal of our method is to make the target invisible to trackers,thereby leading to tracking drift. To accomplish this goal,
we train the generator with a carefully designed and novel cooling-shrinking loss. Considering that SiamRPN-based
trackers [16, 40, 15, 39, 30] locate the target in a local search region based on the template given in the initial frame, we
design two versions of perturbation-generators to attack the search regions and the template respectively.

### Threat of Adversarial Attacks on Deep Learning in Computer Vision: A Survey
1. Whereas deep learning **performs** a wide variety of Computer Vision tasks with **remarkable** accuracies

2.  Notice that the perturbations are hardly perceptible for human vision system, however their effects on the deep learning models are catastrophic.

3. The remaining article also follows the same definitions of the terms. 表述某个符号或者定义在下文不发生变化

4. laboratory setting, in the digital world.

### Matching Networks for One Shot Learning

1. 规则的单词：criterion, regime, strategy
2. more precisely, more specifically
3. improve .... on dataset xx% to xx% and from xx% to xx% dataset compared to competing approaches.
4. The novelty of our work is twoflod:
5. we draw inspiration from models such as sequence to sequence with attention.
6. Unless otherwise specified, training is always on, and test in .. 除非另有说明，
7. as reported in their paper 引用他人文章数据用语
8. An obvious drawback of our model is the fact that, 表述方法的缺陷

### One-shot Learning with Memory-Augmented Neural Networks

1. adequately incorporate 充分纳入  
2. catastrophic interference 灾难性推断
3. obviate the downside of 消除...的不利因素
4. The network is tasked to.../The goal of the network is ...网络的任务是/网络的目标是
5. akin to 类似于
6. As such 因此
7. prior to... 先于
8. in accordance with 依据（后面接公式等）

## New High-speed Frame Detection Method: Side Trace Algorithm (STA) for i-appli on Cellular Phones to Detect Watermarks  
1. the response delay caused by the transmission time presented a problem for the user interface. 由传输时间引起的相应延迟给用户交互引出了一个难题。
   句式：名词（现象）+ 原因状语 + 谓语 + 宾语 （个人己见）
2. The drawback to this method is that  这个方法的缺点是
3. the former , 二者中的前者
4. A feature point search must be able to *withstand* the effects of this noise 特征点搜索必须能够经受得了这种噪声。
5. quadrilateral 四边形
6. bisect 二等分
7. traversal 遍历
8. 

## Screen-Shoting Resilient Watermarking

1. traversal 遍历
2. The reason can be analyzed as follows. 原因解释如下
3. intensity 强度
4. As explained previously , 正如之前的解释
5. The second requirement is motivated by the observation that .... 第二个要求是由以下观察得出：
6. Similar conclusion can be drawn as that for Dataset8. 可以得出与数据集8相似的实验结论
7. It is observed that,   可以观察到

## The Unreasonable Effectiveness of Deep Features as a Perceptial Metric

1. *The ability to* compare data items *is perhaps the most fundamental* operation underlying all of computing.

   句意：比较数据项的能力也许能所有计算的基础的最基本操作

2. the goal is for the compressed image to be indistinguishable from the original by a human observer， irrespective of the fact that their pixel representations might be very different.
   句意：压缩图像的目标是尽可能让一个人类观察者感知不到与原始图像的差别，尽管实际上它们像素表示上有一些差别。

3. A well-know example is that blurring causes large perceptual but small $\ell_2$ change.
   句意：一个显而易见的例子是感知上造成较大模糊的情况下，二范数损失的该变量却很小。

4. Our results are consistent with the hypothesis that perceptual similarity is not a special function all of its own, but rather a *consequence* of visual representations tuned to be predictive about important structure in the world.
   句意：我们的结果与假设一致：感知相似性并不是其本身的特殊功能，而是视觉表示的结果，这些视觉表示被调整为可预测现实世界的重要结果。

5. We create a set of "traditional" distortions consisting of common operations performed on the input pathches, listed in Table 1. **In general**, we use photometric distortions, random noise, blurring, spatial shifts and corruptions, and compression artifacts. 
   句意：我们创建了一个”传统“失真集合，包括在输入patches上常用的一些操作。一般而言，我们使用。。。。
6. In addition, we collect judgements on a large number of 64 x 64 patches rather than a small number of images. **There are three reasons for this**. First,...
   句意：此外，我们在64x64尺寸的patches集合中收集判断，而不是小数据集。原因是。。。
7. facto standard   事实标志

## Text-image Watermarking based on Integer Wavelet Transform(IWT) and Discrete Cosine Transform(DCT)

1. Integer Wavelet Transform(IWT) is much faster beacuse it deals only with integers. The image can be reconstructed without any loss using IWT and can be stored without rounding off the error [1]([1])
   句意：IWT更快，因为它仅处理整数。使用IWT重建图像而不会造成任何损失，并且可以存储图像出现错误？
2. In this section, watermarking methods which performed on text-image will be discussed and compared. Text-image watermarking methods in the spatial domain are presented first, then watermarking methods which are performed in the transform domain.
   句意：在本节，将会讨论数字水印用于文本图片的方法。首先介绍空域方法，然后介绍变换域方法。

## Image Demoireing with Learnable Bandpass Filters

1. Through an ablation study, we demonstrate the effectiveness of the different components of Net
   句意：通过对比实研究，我们证明了网络的不同组件的有效性。
2. **In this section, we make a breif introduction of **several CNN-based methods in related tasks, where deep learning has made significant impact.
   句意：在本节，我们简短地介绍了基于CNN方法在相关任务中的工作，这些工作中深度学习方法取得了重大的进展。
3. L1 loss is more effective than L2 loss for image restoration tasks.
   句意：在图像重建任务中，L1损失的比L2损失更加有效
4. Please note: the validation dataset is completely independent and not used in training.
   句意：请注意：验证集完全独立并且没有在训练中使用
   *这个可以视情况使用，比如说实验结果都在什么数据集上的*
5. To verify the effectiveness of each component in our model, we conduct extensive ablation study, including evaluation of MTRB vs. GTMB and LTMB, learnable bandpass filter, and loss function.
   这段是指出消融实验都包括哪些内容，可以在消融实验标题下的第一段使用。
6. + The result is mainly caused by two reasons. First, .... ;(用.)Second,...
     句意：解释某项结果的原因的句式。
   + There are two reasons for this observation. First, .... ;(用.)Second,...
     句意：这个观察的原因有两个。
7. The CNN has **certain** denoising and local smoothing capabilities.
   句意：CNN**有一定的**去噪和局部平滑能力。
8. We denote the networks constructed without LP and DDT as MBCNN-nLP and MBCNN-nDDT, respectively.
   用法：在对网络进行修改的对比实验设置时，可以使用该句法。
   句意：我们将不带LP或DDT的网络结构分别称为。。。。
9. We tested  the performance of these three models on validation set of *LCDMoire*.
   句意：我们在验证机上测试了三个模型的性能。
10. 











[1] R. Harshitha, S.S., Vidya, Robust and High Limit Watermarking using DWT-IWT. International Journal of Advanced Scientific Research and Management, 2 (4), pp.18-21, 2017.  