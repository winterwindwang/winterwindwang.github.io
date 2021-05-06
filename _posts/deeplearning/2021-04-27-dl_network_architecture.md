---
layout: post
title: 深度神经网络中的各种网络结构
category: deeplearning
tag: graphic, networks overview
keywords: 神经网络结构
---

> 导语：记录神经网络的结构及其作用

### BP神经网络

> 摘自:https://wenku.baidu.com/view/ebea1f3570fe910ef12d2af90242a8956aecaa6b.html?fr=search-4-X-income1&fixfr=UEWnoIGmOkz5iXYEwnZKXQ%3D%3D

> 问题：画出BP神经网络的结构并描述其作用。

采用BP算法的神经网络模型称为BP神经网络，一般由输入层、隐层和输出层组成，隐层可以是一层或者多层，每个层又包含许多单个神经元，在BP神经网络中，层与层之间的神经元是全连接，层内部的神经元之间是无连接的。各隐层节点的激活函数使用Sigmoid函数，其输入层和输出层激励函数可以根据不同需要而异.因此,BP神经网络是前向反馈网络的一种,其网络结构可以采用简要的形式来描述,如图

![](https://winterwindwang.github.io/assets/img/BP_network.png)

#### BP算法

BP算法即误差反向传播（Back Progagation）算法，其主要思想是把学习过程分成两个阶段：

+ 第一阶段（正向传播过程），给出输入数据通过输入层经隐层处理并计算每个单元的实际输出值
+ 第二阶段（反向传播），若在输出层未能得到期望的输出值，则逐层递归地计算实际输出与期望输出之差值（即误差），以便根据此差值调节权重。



### 卷积神经网络



### 循环神经网络

#### RNN



#### LSTM



### 图神经网络



### Attention





