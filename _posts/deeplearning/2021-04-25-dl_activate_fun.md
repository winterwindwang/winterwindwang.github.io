---
layout: post
title: 深度学习中的激活函数
category: deeplearning
tags: deep learning, activation function
keywords: deep learning, activation function
---

> 导语：深度学习中，激活函数的作用非常重要。在此记录目前大多数激活函数。激活函数一律使用f(x)表示，无论其公式表示如何

### 1. Sigmoid

sigmoid是常用的激活函数，一般用于神经网络输出层之后，与logistic, soft step是同种类别（同名？）。在分类任务中，使用sigmoid后，得到的数值在（0，1）之间预测概率，预测概率和取决于类别数和预测概率，值可大于1。这与softmax不同，因为softmax得到的是量化后的预测结果，概率和为1，所以不需要使用阈值。激活函数的数学表示，其范围为（0，1）。



其导数为



其图像为



### 2. Softmax

softmax是sigmoid更一般的形式。它用于多分类问题。与sigmoid相似，它产生的值在0-1范围内，因此它作用与分类模型中的最后一层。其数学表达式如下，其范围在（0，1）之间：



其导数为



### 3. ReLU(rectified linear unit)

中文名为整流线性单元，其常用于深度神经网络中。其公式如下

#### 4. Tanh

### 5. Leaky ReLU (leaky rectified linear unit)

### 6. PReLU(parametric rectified linear unit)

### 7. ReLU6

### 8. Swish

### 9. Hard-Swish or Swish

### 10. Linear

### 11. GELU(gaussian error linear unit)

### 12. ELU(exponential linear unit)

### 13. Binary step

### 14. Softplus

### 15. Maxout