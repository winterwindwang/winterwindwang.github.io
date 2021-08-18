---
layout: post
title: neural_rendered的成功安装记录
category: 技术
tags: neural renderer, build, ninja
keywords: 神经渲染器安装，环境编译
---

> 导语：复现论文时，需要使用神经渲染器，在我的环境下安装时遇到一些问题，先将不成功的解决方案记录下来。主要是在RTX 3070显卡（算力参数,sm_86），CUDA11.0编译并安装torch版本的neural_renderer

### 1 系统环境

+ Python 3.6
+ VS2017
+ RTX 3070
+ CUDA11.0 

### 2 版本的选择和安装 

在github上有两个版本的neural_renderer的版本，分别是作者提供的chainer和cupy版本和其他作者改进的纯torch版本：

+ chainer版本：[https://github.com/hiroharu-kato/neural_renderer](https://github.com/hiroharu-kato/neural_renderer)

+ torch版本： [https://github.com/daniilidis-group/neural_renderer](https://github.com/daniilidis-group/neural_renderer)
+ 另一个torch版本：[https://github.com/akanazawa/cmr](https://github.com/akanazawa/cmr)

### 3 Chainer版本的安装和可能遇到的问题以及解决方案

首先拉取chainer版本的github仓库；

然后安装chainer和cupy库，安装cupy库时要根据自己安装的cuda版本选择安装不同的cupy库。

#### 3.1 第一种使用方式--安装neural_renderer库

然后运行`python setup.py install`

如果cupy版本没有安装好，使用上述命令估计会出错。因此为了快速使用，可以使用下面的方式。

#### 3.2 第二种使用方式--直接在代码中通过绝对路径来使用

假如我们的文件目录如下：

```
-data
-src
	- neural_renderer
		- renderer
		- __init__.py
		- ...
	- main.py
	- ...
```



如果我们想要在`main.py`中使用`neural_renderer`，那么可以使用以下代码将作为其作为库调用

```python
import sys
sys.path.append('../neural_renderer/')
import neural_renderer
```

#### 效果



### 4 纯torch版本的安装和遇到的问题以及解决方案

与Chainer版本类似，首先拉取github仓库；

然后在安装了pytorch的conda环境下，运行以下命令`python setup.py install`进行安装。

> 重要：若要使用的数据格式是[物体的位置信息，相机位置信息（相对于物体的偏移）]，需要修改neural_renderer中renderer.py文件中所有nr.look()调用参数，具体的修改参照如下的方式：
>
> nr.look(vertices, self.eye, self.camera_direction)
>
> 修改成下面的形式:
>
> nr.look(vertices, self.eye, self.camera_direction, self.up)
>
> 一共需要修改4处

但是会遇到很多问题。遇到的问题和解决方案如下：

#### 问题和解决方案

1. 第一个问题是由于torch版本的问题会报“AT_CHECK”错误，这是因为“AT_CHECK”在torch的1.5版本就被丢弃了，使用TORCH_CHECK将文件中所有“AT_CHECK”都替换掉。

2. 第二个问题，报了一个无法找到`cl.exe`文件的错误，解决这个错误只需要使用`x64 Native Tool command`这个工具，这个工具在`Win+Q`搜索到。

3. 修改`torch/utils/cpp_extension.py`中第1631行的`['ninja', '-v']`改成`['ninja', '--version']`

4. 第三个问题，报了一个无法加载`load_texture.obj`的问题

   > 解决方案：使用python setup.py install 命令运行，等到报错的时候就去`build/temp.win-amd64-3.6/Release`下寻找build.ninja。
   >
   > 首先，下载ninja并将其编译，然后C盘下新建一个文件夹Local并在其下新建Ninja文件夹，把编译好的ninja.exe放到里面，再把路径名配置到环境变量里面。回过头看nerual renderer文件夹下有个build文件夹，里面有个cuda文件夹，里面有个build.ninja文件，如果报算力不匹配(如sm_86不匹配，就将其改成sm_80)，改完后然后在命令行里面使用ninja -f build.ninja编译.obj文件。后续的无.obj报错的解决方案类似。

5. 第四个问题，会报以下错误

   ` error: function "atomicAdd(double *, double)" has already been defined`

   >解决方案：使用以下代码进行替换
   >
   >```cpp
   >
   >
   >
   > #if !defined(__CUDA_ARCH__) || __CUDA_ARCH__ >= 600
   >
   >  #else
   >  static __inline__ __device__ double atomicAdd(double *address, double val) {
   >    unsigned long long int* address_as_ull = (unsigned long long int*)address;
   >    unsigned long long int old = *address_as_ull, assumed;
   >    if (val==0.0)
   >      return __longlong_as_double(old);
   >    do {
   >      assumed = old;
   >      old = atomicCAS(address_as_ull, assumed, __double_as_longlong(val +__longlong_as_double(assumed)));
   >    } while (assumed != old);
   >    return __longlong_as_double(old);
   >  }
   >
   >#endif
   >```

其中，针对每个.cpp和.cu文件都要重复修改算力参数。

解决上述问题以后，就应该可以成功的安装neural_renderer。

#### 效果

