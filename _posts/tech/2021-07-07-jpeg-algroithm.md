---
layout: post
title: JPEG算法详解
category: 技术
tags: algorithm, jpeg, compression
---

> 导语： 这是一个印度人写的关于JPEG压缩算法的博客，解释得很清楚，并且用简单容易理解的python进行了解释
>
> 博客地址：[https://yasoob.me/posts/understanding-and-writing-jpeg-decoder-in-python/](https://yasoob.me/posts/understanding-and-writing-jpeg-decoder-in-python/)

#### 1 JPEG算法的不同部分

首先观察如下图片，它列出了JPEG文件所有不同的部分。

![](https://d33wubrfki0l68.cloudfront.net/bdc1363abbd5744200ec5283d4154e55143df86c/8c624/images/decoding_jpeg/jpegrgb_dissected.png)

其中，FF D8是文件头，表示这个文件是一个JPEG文件；FFD9是文件尾部，标志着图像文件的结束。除FFD0到FFD9和FF01之间所有的标记，后面都跟着长度说明符，它将为您提供标该标记段的长度。至于文件的开始和结束标记，它们通常由两个字节构成。

JPEG中数据的存储格式是big-endian格式。仅有EXIF数据可以用Little-endian格式进行存储。JPEG中的标记是4个十六进制数组成：`ffd8`。一个十六进制数字等于 4 位（1/2字节），因此4个十六进制等于2个字节，一个short也等于2个字节。

Start of Scan后面紧跟着Image scan，因此Image scan数据没有特定的长度，它直到“end of file”标记的出现。每当我们看到SOC标记时，我们都会手动寻找EOF标记。

到目前为止我们已经了解了基本的网络框架，接下来看看剩余的图像数据都包括哪些。我们将首先浏览必要的理论，并对其进行编码实现。

### 2 Encoding a JPEG

首先将介绍JPEG的相关基础概念和编码技术，然后将介绍解码部分。直接理解解码部分有难度。

尽管下面的图片现在对你来说意义不大，但它会在我们完成整个编码/解码过程时为你提供一些锚点。 它显示了 JPEG 编码过程中涉及的步骤：

![](https://d33wubrfki0l68.cloudfront.net/7237dd0093b6a8070b2c927673fd73bc797561d2/33b0a/images/decoding_jpeg/encoding.png)

### JPEG的颜色空间

根据JPEG色谱（ISO/IEC 10918-6:2013(E), section 6.1）：

+ 仅使用一个组件对图像进行编码，被编码成灰度图，范围是黑色0，白色255。
+ 使用三个组件对图像进行编码，将RGB数据编码成YCbCr，除非图像包含了在6.5.3中指定的APP14标记物，在这种情况下，根据APP14标记物分段，颜色编码被认为是RGB或YCbCr中的一种。RGB和YCbCr之间的关系定义在ITU-T T.871 | ISO/IEC 10918-5
+ 使用四个组件进行编码，被编码成CMYK。

大部分JPEG算法的实现均使用了亮度和色度（YUV编码），而不是RGB。这在JPEG中非常有用，这是因为人眼对小区域高频亮度的变化不敏感，因此我们本质上可减少大量的频域，且人眼无法告知这些差异。高质量的压缩图像在质量上几乎不会有可见的降低。

正如RGB颜色空间中的每个像素均由3个字节组成，YUV颜色空间中的像素也使用3个字节表示，但是每个字节表示稍有不同。