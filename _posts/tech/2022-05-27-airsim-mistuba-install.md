---
layout: post
title: Mitsuba+AirSim 安装
category: 技术
keywords: 渲染器、仿真平台
tags: technical details
---

# Mitsuba安装

环境配置：

```
1. python 3.6
2. VS2019
3. Scons 3.0
4. Mitsuba 1(0.6)
```

以下是安装步骤

## Step 1 拉取Mitsuba

拉取 mitsuba分支，并选择mitsuba下scons-python3子分支

```
1、git clone https://github.com/mitsuba-renderer/mitsuba.git
2、git checkout scons-python3
```

## Step 2  安装Scons

不要直接使用`pip install scons`，因为这个命令会安装最新的scons，可能会发生编译错误，这里我参考issues中一位前辈的成功案例[这里](https://github.com/mitsuba-renderer/mitsuba/issues/150)，选择安装 scons 3.0版本

```
activate conda_env  #激活你的虚拟环境
pip install scons=3.0 -i https://pypi.tuna.tsinghua.edu.cn/simple   # 使用源可以加快安装速度
```

## Step 3 编译

编译时，为避免出现各种错误，使用`x64  native tools command prompt for vs 2019`命令行工具，该工具在windows系统左下角可以输入 x64等关键词找到。（**建议在安装scons这一步就可以使用上述命令行**）

打开 命令行工具后，定位到mistuba文件夹，然后激活conda虚拟环境。接着直接使用命令即可完成编译

```
scons
```

## Step 4 

编译完成后，会生成一个dist文件夹，里面有`mitsuba.exe`与`misgui.exe`，但是后者还运行不了，这时需要安装QT5，具体详情可以参考[这里](https://banbao991.github.io/2021/04/26/CG/mitsuba/mitsuba-0-6-installation/)

在查看issues时，发现有人把编译好的dist文件共享出来，下载下来后发现还能用，参考[这里](https://github.com/mitsuba-renderer/mitsuba/issues/52#issuecomment-357189129)，下载地址是[这里](https://drive.google.com/file/d/1Ggt1KzxaGT737qReroIwt9iDM3dFrD__/view)

## 参考资料

[1]:https://zhuanlan.zhihu.com/p/359008593
[2]:https://github.com/mitsuba-renderer/mitsuba/issues/52
[3]:https://banbao991.github.io/2021/04/26/CG/mitsuba/mitsuba-0-6-installation/

# Mitsuba 2的安装

参考：https://zhuanlan.zhihu.com/p/359008593

遇到最大的问题就是`git submodule update --init --recursive`，在开启代理的情况下，使用`git clone https:`拉取代码会出错。关闭代理就不会出现这个问题，但是容易超时。为此，我将mitsuba文件夹中`.gitmodules`的`https:`修改成`git@github.com`（即ssh形式），然后就可以拉取成功了。如果后续还会出现错误，可以尝试关闭代理，然后再执行命令`git submodule update --init --recursive`

在python中使用mitsuba2时候，要改变mitsuba中的`variants`,即打开`mitsuba.conf`在其xx行添加以下几行(注意不要超过5个，否则编译过程会非常慢)：

```
packet_rgb,
packet_spectral, // python使用以上两个
gpu_rgb,
gpu_autodiff_rgb,
gpu_autodiff_spectral, // 如果用gpu渲染，那么就只用上面几个
```

添加完后直接使用命令编译

```
cmake -G "Visual Studio 16 2019" -A x64
```

会报以下错误

```
CMake Error at :
  No CUDA toolset found.
```

为解决该问题，查询这里[资料](https://stackoverflow.com/a/68120870)可知。但是如果要加入gpu可微分的约束，那么就需要VS2019支持cuda编译，具体操作如下，将目录 `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.0\extras\visual_studio_integration\MSBuildExtensions`中的中的四个文件复制到`xxx\Microsoft Visual ，Studio\2019\MSBuild\Microsoft\VC\v160\BuildCustomizations`，这样cmake就能使用cuda编译。（注意VS2017则不存在这个问题，而且`BuildCustomizations`的位置也与VS2019也不一样）

注意！！！！：不要自己拉ext里面的文件，否则会出现版本对不上的问题从而导致cmake出错。我遇到的就是libpng编译没通过的问题，问题大概是

````
set_prop(TARGET， png16) png16报错
````

一番查找过后才发现，是因为libpng目录中的cmake文件没有png16，而通过`recursive`方式下载的文件里面有png16。

针对这个问题，有另一种解决思路：利用git回滚

比如说针对`libpng @ 7f4528d`，在利用git clone 拉取libpng以后，使用以下命令，可完成版本的更换

```
>> cd libpng
>> git reset --hard 7f4528d
```

最终就可以生成mitsuba.sln文件了（由于我用的RTX3070，cuda11.1，所以没法完成编译，因此先暂时不用gpu的配置了）

接着，别用右键用vs2019打开。要先打开vs2019，然后`打开项目或解决方案`

#### 最新！！！完成安装

系统参数：

+ RTX 3070
+ CUDA 11.0
+ PYTHON 3.7
+ CMake 3.21.0
+ win10
+ optix 7.0
+ compiled variants
  + scalar_rgb
  + gpu_autodiff_rgb
  + gpu_rgb

1、先按照参考资料1拉取github仓库

2、如果要使用gpu，好像需要下载optix，去[官网](https://developer.nvidia.com/designworks/optix/downloads/legacy)下载就好了。下载完后，安装好。

3、在目录`mitsuba2/resource/ptx/`编译Makefile，这里面需要指定刚刚下载好的optix目录地址

```
mts_include_folder = ../../include
mts_shape_folder   = ../../src/shapes/optix
mts_optix_main     = ../../src/librender/optix/optix_rt.cu

all: optix_rt.ptx

optix_rt.ptx: $(mts_optix_main) $(mts_include_folder)/* $(mts_shape_folder)/*
	nvcc $(mts_optix_main) \
		 -I $(mts_include_folder) -I $(mts_shape_folder) -I /opt/optix-7.0.0/include/ \
		 -O3 -gencode arch=compute_61,code=compute_61 --ptx

clean:
	rm -f optix_rt.ptx
	
============================
将-I /opt/optix-7.0.0/include/ 修改成-I "C:/ProgramData/NVIDIA Corporation/OptiX SDK 7.0.0/include"
然后将-O3 -gencode arch=compute_61,code=compute_61 --ptx 中的结构改成（根据使用的显卡改，RTX3070本来是86，但是86会报错，改80不会报这个错）
-O3 -gencode arch=compute_80,code=compute_80 --ptx
============================ 修改后版本================
mts_include_folder = ../../include
mts_shape_folder   = ../../src/shapes/optix
mts_optix_main     = ../../src/librender/optix/optix_rt.cu

all: optix_rt.ptx

optix_rt.ptx: $(mts_optix_main) $(mts_include_folder)/* $(mts_shape_folder)/*
	nvcc $(mts_optix_main) \
		 -I $(mts_include_folder) -I $(mts_shape_folder) -I "C:/ProgramData/NVIDIA Corporation/OptiX SDK 7.0.0/include" \
		 -O3 -gencode arch=compute_80,code=compute_80 --ptx

clean:
	rm -f optix_rt.ptx
```

修改完后，可能还会遇到`cl.exe`不存在的问题，如果用VS2019自带的x64命令行终端应该不会出现这个问题，我是将对应的`cl.exe`配置到环境变量中

```
E:\software\VS2017\Microsoft Visual Studio\2019\VC\Tools\MSVC\14.29.30133\bin\Hostx64\x64
```

配置完后，在powershell或其他终端（没尝试过其他终端）运行以下命令

```
bash Makefile
```

4、修改CMake文件，这里我选择的是CMake 3.21.0, 因为CMake 3.18.3不存在以下选项。在文件`xxx\CMake\3.21.0\share\cmake-3.21\Modules\Compiler\NVIDIA-CUDA.cmake`的91行左右有

```
  if (NOT CMAKE_CUDA_COMPILER_VERSION VERSION_LESS 9.0)
    if(CMAKE_CUDA_SIMULATE_VERSION VERSION_GREATER_EQUAL 19.10.25017)
      set(CMAKE_CUDA14_STANDARD_COMPILE_OPTION "-std=c++14")
      set(CMAKE_CUDA14_EXTENSION_COMPILE_OPTION "-std=c++14")
    else()
      set(CMAKE_CUDA14_STANDARD_COMPILE_OPTION "")
      set(CMAKE_CUDA14_EXTENSION_COMPILE_OPTION "")
    endif()
  endif()
============================修改成
  if (NOT CMAKE_CUDA_COMPILER_VERSION VERSION_LESS 9.0)
    if(CMAKE_CUDA_SIMULATE_VERSION VERSION_GREATER_EQUAL 19.10.25017)
      # set(CMAKE_CUDA14_STANDARD_COMPILE_OPTION "-std=c++14")
      # set(CMAKE_CUDA14_EXTENSION_COMPILE_OPTION "-std=c++14")
    else()
      set(CMAKE_CUDA14_STANDARD_COMPILE_OPTION "")
      set(CMAKE_CUDA14_EXTENSION_COMPILE_OPTION "")
    endif()
  endif()
```

5、完成以上步骤以后，（若使用conda虚拟环境，则激活虚拟环境，这一步很关键，因为好像只能为特定Python编译mitsuba2）使用以下命令编译库

```
cmake -G "Visual Studio 16 2019" -A x64
```

6、然后，可以选择打开VS2019，然后打开项目或解决方案的方式打开mitsuba.sln，将debug改成release模型进行运行，但是这种方式好像没法控制使用python的版本，会使用默认环境下的python，无法用特定虚拟环境中的python编译。因此，我采用了另外一种思路，即在mitsuba2目录下，使用以下命令运行

```
cmake --build ..(如果错误，就用一个点)
```

这样运行就不会出现错误了。

7、设置path，运行setpath

```
setpath
```

完成后，键入mitsuba，如果不报错就安装成功了。同时可以在同一个命令行下进入python终端，导入mitsuba，如果没报错就算是安装成功了。最后！！！如果要在python中使用，还需要将目录配置到环境变量中。

8、配置环境变量

```
向系统环境变量（Path）添加
F:\PythonPro\mitsuba2_gpu\dist;F:\PythonPro\mitsuba2_gpu\build\dist;
新建PYTHONPATH，然后添加
F:\PythonPro\mitsuba2_gpu\dist\python
F:\PythonPro\mitsuba2_gpu\build\dist\python
```

注意此时虽然在命令行里面能访问mitsuba，但是在pycharm里面还是访问不到mitsuba。

以上问题，重启电脑就解决了。

但是仍然存在问题：

mitsuba与其他库比如torch无法同时导入到项目里面，故而还是用不了，学习mitsuba3去了，放弃mitsuba2了（心累）。

参考：

```
1、https://www.cnblogs.com/FromATP/p/14920877.html
2、https://github.com/mitsuba-renderer/enoki/issues/70
3、https://github.com/mitsuba-renderer/mitsuba2/issues/569
```

注意：每次修改cmake文件以后，都需要将之前生成的文件删掉，以防修改不起作用。

#### 续：Linux安装

系统配置：

```
1、Linux 18.04
2、CMAKE 3.14.7
3、ninja 1.8.2
4、driver 510.39.01
5、Tesla T4
7、cuda 11.1
8、python 3.9
9、GCC 8.4.0
```

1、跟着官方教程走

2、CMAKE版本太低的问题：重新安装CMAKE，参考[这里](https://codeyarns.com/tech/2019-03-20-how-to-install-cmake.html#gsc.tab=0)。ninja可以使用默认安装版本。

3、在使用`git clone --recursive https://github.com/mitsuba-renderer/mitsuba2`拉取项目以后，多次使用以下命令拉取子模块

```
git submodule update --init --recursive
```

4、将resource中的mitsuba.conf.template复制到mitsuba2根目录下，并修改其名字为mitsuba.conf。 然后更改mitsuba.conf中enabled的设置，使其允许可微分渲染

```
   "enabled": [
        # The "scalar_rgb" variant *must* be included at the moment.
        "scalar_rgb",
        "scalar_spectral"，
        "gpu_rgb",
        "gpu_autodiff_rgb"
    ],
```

5、使用以下步骤安装（依次运行）

```
mkdir build
cd build/
cmake -GNinja ..
ninja

# 上述命令如果没报错就直接下一步
source setpath.sh
python
import mitsuba as mi
如果能正确导入，则说明安装成功
```

在上述几个步骤中可能会遇到一些问题，比如说系统的lib文件与anconda中的lib文件冲突，网上建议是删掉anconda中对应的文件。

6、路径设置（p.s， 网上都说设置环境变量，却没说怎么设置，如果按setpath.sh输出的那种设置，系统还是找不到对应的mitsuba），以下是系统建议的路径

```
export PYTHONPATH="$MITSUBA_DIR/dist/python:$MITSUBA_DIR/$BUILD_DIR/dist/python:$PYTHONPATH"
export PATH="$MITSUBA_DIR/dist:$MITSUBA_DIR/$BUILD_DIR/dist:$PATH"
```

以上路径设置在`~/.bashrc`中以后，仍然没有效果。分析一通后发现，没有指定MITSUBA_DIR，系统不知道该位置在何处，因此在`~/.bashrc`中需要加上该地址。最终在`~/.bashrc`中添加以下几行

```
export MITSUBA_DIR="/xx/mitsuba2/build" # 其中xx为mitsuba2在你系统中的地址
export PYTHONPATH="$MITSUBA_DIR/dist/python:$MITSUBA_DIR/$BUILD_DIR/dist/python:$PYTHONPATH"
export PATH="$MITSUBA_DIR/dist:$MITSUBA_DIR/$BUILD_DIR/dist:$PATH"
```

完后，`source ~/.bashrc`mitsuba就能被系统识别了。

# AirSim 安装

环境要求:

```
VS2019，SDK 安装默认与最新
Unreal Engine 4.27 
```

使用VS2019自带的`developr command prompt for vs 2019`进行编译

VS2019需要安装SDK, SDK4.8可用

参考：airsim详细教程(1)：win10配置airsim仿真环境(2021.8.12更新) - 宁子安的文章 - 知乎 https://zhuanlan.zhihu.com/p/267321662

## Tesla 系列（T4）显卡上安装AirSim遇到的一些问题

先说安装环境

```
VS2019
win10专业版
Epic（Unreal Engine 4.27）
需安装显卡驱动
最好安装.Net3.5和2.0框架
```

在使用T4显卡的win10系统上安装UE4.27会遇到以下问题：

```
运行引擎需要D3D11兼容GPU（功能级别11.0，着色器模型5.0）
```

参考这个[博客](https://www.pingxingyun.com/news/736177876327464960.html)，发现仅使用普通的英伟达显卡驱动无法启动UE仿真器。原因是Tesla原生驱动仅支持在TCC模式下工作，要使用UE仿真器（图形渲染器）需要WDDM模式。

为解决上述问题，需要安装vGPU驱动，可以使用以下的链接下载对应的vGPU驱动

```
https://cloud.google.com/compute/docs/gpus/grid-drivers-table?hl=zh-cn
```

我使用`463.15_grid_win10_server2016_server2019_64bit_international.exe`后，发现可以打开UE4。（注意我得系统是win10，不是server系统，但好像不影响使用）。

后续问题参考参考：airsim详细教程(1)：win10配置airsim仿真环境(2021.8.12更新) - 宁子安的文章 - 知乎 https://zhuanlan.zhihu.com/p/267321662
