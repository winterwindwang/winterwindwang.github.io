---

layout: post

titile: 小技巧积累

category: 技术

tag: React

keyword: React

----

## 小技巧积累

1. 参数的显存占用。只有有参数的层，才会有显存占用。这部分的显存占用和**输出无关**，模型加载完成之后就会占用。

**有参数的层主要包括：**

+ 卷积
+ 全连接层
+ BatchNorm
+ Embedding层

**无参数的层：**

+ 多数的激活层（Sigmoid/ReLU)
+ 池化层
+ Dropout
+ ... ...

**梯度与动量的显存占用** 模型与**输入无关的显存占用**：

+ 参数**W**
+ 梯度**dW**（一般与参数一样）
+ 优化器的**动量**（普通SGD没有动量，momentum-SGD动量与梯度一样，Adam优化器动量的数据量是梯度的两倍）

**深度学习中神经网络的显存占用，有如下公式：**

```
显存占用=模型显存占用 + batch_size x 每个样本的显存占用
```

2. 实时查看nvidia显卡是使用情况：`nvidia-smi -l 1` 即每隔1秒刷新一次
3. PyTorch官方推荐：具有学习参数的（例如，conv2d, linear, batch_norm)采用`nn.XXX`的方式，没有学习参数的（例如，maxpool, loss func, activate func)等根据个人选择使用`nn.functional.xxx`或者`nn.XXX`方式。但是关于`dropout`，推荐使用`nn.Xxx`方式，因为一般情况下只有训练阶段才进行dropout，在eval阶段都不会进行dropout。使用`nn.Xxx`方式定义dropout，在调用`model.eval()`后，model中所有的dropout layer都会关闭，但是以`nn.functional.dropout`方式定义dropout，在调用`model.eval()`后并不能关闭dropout。[参考地址](https://www.zhihu.com/question/66782101)
4. 