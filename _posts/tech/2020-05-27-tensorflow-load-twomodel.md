---
layout: post
title: Tensorflow 加载同一模型的两个不同模型参数
category: 技术
tags: React
keywords: React
---


## Tensorflow模型的保存

以下代码只是一个思想，并不保证能够运行
```
with tf.variable_scope('model1'):
	cnn1 = CNN()
	predict1 = cnn1(input)
with tf.variable_scope('model2'):
	cnn2 = CNN()
	predict2 = cnn2(input)

predict = (predict1 + predict2) / 2

t_var = tf.global_variable()
var_model1 = [var for var in t_var if var.name.startswith('model1')]
var_model2 = [var for var in t_var if var.name.startswith('model2')]

sess = tf.train.Session()
saver_model1 = tf.train.Saver(var_list=var_model1)
saver_model2 = tf.train.Saver(var_list=var_model2)

saver_model1.save(sess, 'checkpoint/model1')
saver_model2.save(sess, 'checkpoint/model2')
```
题外话，DeepSpeech模型参数的加载以及重新保存见[Black Box attack](https://github.com/rtaori/Black-Box-Audio/blob/master/run_audio_attack.py)

## Tensorflow模型加载

```
with tf.variable_scope('model1'):
	cnn1 = CNN()
	predict1 = cnn1(input)
with tf.variable_scope('model2'):
	cnn2 = CNN()
	predict2 = cnn2(input)

predict = (predict1 + predict2) / 2

t_var = tf.global_variable()
var_model1 = [var for var in t_var if var.name.startswith('model1')]
var_model2 = [var for var in t_var if var.name.startswith('model2')]

sess = tf.train.Session()

saver_model1 = tf.train.Saver(var_list=var_model1)
saver_model2 = tf.train.Saver(var_list=var_model2)

saver_model1.restore(sess, 'checkpoint/model1')
saver_model2.restore(sess, 'checkpoint/model2')

```


