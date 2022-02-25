---
layout: post
title: t-SNE降维可视化方法
category: 技术
keywords: dimension reduction, visualization
tags: 可视化, 降维
---

# 安装

Github上有两个比较热门的TSNE包，分别是：

+ [MulticoreTSNE](https://github.com/DmitryUlyanov/Multicore-TSNE)
+ [TSNE-CUDA](https://github.com/CannyLab/tsne-cuda)

CSDN上也有一个手撸TSNE的，地址[在这](https://blog.csdn.net/leida_wt/article/details/84993848)，记录以便日后学习使用

由于TSNE-CUDA在Win10上装不上（可能是系统原因？），所以安装了MulticoreTSNE包。

使用github提供的`pip install MulticoreTSNE`会安装失败，看过ISSUS后，发现是依赖包的问题，在python3.6下，cmake的最新版本为3.22.x，但是requirements.txt中的约束是camke>=3.17.0。如果直接pip install的话，系统会安装最新的cmake版本，这样就会导致安装失败。那么如何解决呢？

直接指定cmake版本`pip install camke==3.17.0`！！

但是很不幸，又发生以下错误了

![image-20211214092000290](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211214092000290.png)

还是回到ISSUS中，发现别人也运到[这个](https://github.com/DmitryUlyanov/Multicore-TSNE/issues/32)问题，有位coder提供了以下的解决方案

```markdown
The problem I am having is that the file tsne_multicore.dll has been compiled with i386 architecture (32-bit) so it will not work on 64-bit systems, thus the "error 0xc1". I am not sure if this is the correct answer for everyone, but I was able to add:

    if 0 != execute(['cmake',
		 '-GMinGW Makefiles',  <----------This
                     '-DCMAKE_BUILD_TYPE={}'.format(build_type),
                     '-DCMAKE_VERBOSE_MAKEFILE={}'.format(int(self.verbose)),
to the setup.py file to get it to compile with MinGW for WIndows which works and produces the right files for the architecture.
```

最后提供修改过程

```python
第一步：
修改setup.py中的第50行，即修改完成后如下所示
if 0 != execute(['cmake',
'-DCMAKE_GENERATOR_PLATFORM=x64', # 修改这里
'-DCMAKE_BUILD_TYPE={}'.format(build_type),
'-DCMAKE_VERBOSE_MAKEFILE={}'.format(int(self.verbose)),

第二步：
build----> python setup.py install
 
第三步：
install----> pip install .

这一步我用来不同的方法，我使用了
python setp.py bdist_wheel
然后使用离线安装
pip install 离线包.whel安装
```

你可能还会遇到刚刚的错误，这时去错误所在的目录`E:\software\Anaconda3\envs\ml\Lib\site-packages\MulticoreTSNE`删除`libtsnee_multicore.dll`

# 举个例子

官方例子

```python
from sklearn.datasets import load_digits
from MulticoreTSNE import MulticoreTSNE as TSNE
from matplotlib import pyplot as plt

digits = load_digits()
embeddings = TSNE(n_jobs=4).fit_transform(digits.data)
vis_x = embeddings[:, 0]
vis_y = embeddings[:, 1]
plt.scatter(vis_x, vis_y, c=digits.target, cmap=plt.cm.get_cmap("jet", 10), marker='.')
plt.colorbar(ticks=range(10))
plt.clim(-0.5, 9.5)
plt.show()
```

效果图

![image-20211214100801820](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211214100801820.png)



