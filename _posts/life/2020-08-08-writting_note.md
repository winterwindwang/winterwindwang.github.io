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

2.  Notice that the perturbations are hardly perceptible for human vision system, however their effects on the deep learning models are **catastrophic**.

3. The remaining article also follows the same definitions of the terms. 表述某个符号或者定义在下文不发生变化

4. laboratory setting, in the digital world.

### Matching Networks for One Shot Learning

1. 规则的单词：criterion, regime, strategy
2. more precisely, more specifically