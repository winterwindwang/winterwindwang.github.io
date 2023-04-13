---
layout: post
title: neural_rendered的成功安装记录
category: 技术
tags: neural renderer, build, ninja
keywords: 神经渲染器安装，环境编译
---

> 导语：复现论文时，需要使用神经渲染器，在我的环境下安装时遇到一些问题，先将不成功的解决方案记录下来。主要是在RTX 3070显卡（算力参数,sm_86），CUDA11.0编译并安装torch版本的neural_renderer
>
> 更新：V100了，GPU上上安装neural_renderer

# Window系统

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

#### 更新：ninja安装

1. window

```git
git clone https://github.com/ninja-build/ninja && cdd ninja
python configure.py --bootstrap
```



1. linux(待续)



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
   > 首先，下载ninja并将其编译，然后C盘下新建一个文件夹Local并在其下新建Ninja文件夹，把编译好的ninja.exe放到里面，再把路径名配置到环境变量里面。
   > 
   > 然后，cmd定位到build文件夹下的\temp.win-amd64-3.6\Release\nerual renderer，可以看到有个build.ninja文件，先使用命令`ninja -f build.ninja`编译。
   > 
   > 如果输出算力不匹配错误(如sm_86、computer_86不匹配，就将build.ninja中所有以_86为后缀的字符改成_80)。改完后在命令行里面使用ninja -f build.ninja重新编译。后续的发生无.obj错误以类似的方法解决。
   > 
   > 记住编译完build.ninja后，cmd命令行要定位回neural_renderer根目录，然后再执行`python setup.py install`

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

6. 如果遇到类似于如下的问题

```python
LINK : fatal error LNK1181: 无法打开输入文件
```

则打开`x64 Native Tool command`这个cmd终端，应该就能解决问题。

# Linux 系统

### 1  系统环境

为防止安装cuda后，系统原有驱动器加载不了的情况，建议先删除系统原有的cuda。然后在安装cuda的时候，勾选安装驱动器即可。

```
sudo apt-get remove --purge '^nvidia-.*'
sudo apt-get remove --purge '^libnvidia-.*'
sudo apt-get remove --purge '^cuda-.*'
```

+ ubuntu 18.04

  ```
  >>uname -r
  4.15.0-167-generic
  >> uname -a
  Linux i-t3wxq38x 4.15.0-167-generic #175-Ubuntu SMP Wed Jan 5 01:56:07 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
  >> lsb_release -a
  No LSB modules are available.
  Distributor ID:	Ubuntu
  Description:	Ubuntu 18.04.4 LTS
  Release:	18.04
  Codename:	bionic
  >> dpkg --print-architecture
  amd64 (要根据这个下载cudnn)
  ```

+ Cuda 11.0 `CUDA Toolkit 11.0.3 (August 2020), Versioned Online Documentation`

  ```
  选择：Linux=>x86_64=>Ubuntu=>18.04=>runfile[local]
  注意！在删掉原始显卡驱动的情况下，选择安装显卡驱动（默认不用改选项）。
  ```

+ cudnn `Download cuDNN v8.4.0 (April 1st, 2022), for CUDA 11.x下面的Local Installer for Ubuntu18.04 x86_64 (Deb)`

+ 安装`sudo apt-get install zlib1g`（不知是否必要，参考的英伟达官网）
+ ninja安装`sudo apt install ninja-build`（注意安装完成后，按照window系统的做法，修改torch中的cpp_extension.py文件）

```
/home/ubuntu/anaconda3/lib/python3.9/site-packages/torch/utils/cpp_extension.py
注意位置发生了变化，修改的位置变成了1682行
```

+ 在neural_renderer文件夹下重复以下过程

```
1、python setup.py install
2、ninja -f  build/temp.linux-x86_64-3.9/build.ninja
1与2步重复3次，最后在运行一次第一步即可
```

+ 最后为防止驱动自动更新照成不必要的麻烦，建议禁止自动更新

```
1、打开sudo vim /etc/apt/apt.conf.d/10periodic
2、将文件中的1改成0
```

## 2 V100, cuda 10.2, Pytorch 1.10.2

1. 遇到问题`error: [Errno 2] No such file or directory: ':/usr/local/cuda-10.2/bin/nvcc'`，解决方案`export CUDA_HOME=/usr/local/cuda`[参考](https://github.com/NVIDIA/apex/issues/368)。

2. 遇到问题`he detected CUDA version (10.2) mismatches the version that was used to compile PyTorch (11.3). Please make sure to use the same CUDA versions`，原因：自己装的cuda与镜像自带的cuda toolkit版本不一致，导致在base环境下安装失败。解决方案：创建新环境`conda create -n ml python==3.6`。然后再执行`python setup install`

## 参考资料

1. https://blog.csdn.net/sinat_36458870/article/details/104522845

2. https://zhuanlan.zhihu.com/p/387156204
3. https://forums.developer.nvidia.com/t/nvidia-smi-has-failed-because-it-couldnt-communicate-with-the-nvidia-driver-make-sure-that-the-latest-nvidia-driver-is-installed-and-running/197141/6?u=soft.wdh.craftman

