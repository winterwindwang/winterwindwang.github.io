---
layout: post
titile: Python调用C++代码（原生调用CPython），VS2017
category: 技术
tags:  Python, C++, CPython
keywords: Python, C++, CPython
---
## 1.VS207配置
使用VS2017开发Python调用C++的环境，首先是安装相应的工具，在Visual Studio 2017中下载python开发

![avatar](https://winterwindwang.github.io/assets/img/2020-07-30-vs2017python.png)
[image](vs2017python.png)

然后在相同界面中的“单个组件”下，下载如下图所示工具：

![avatar](https://winterwindwang.github.io/assets/img/2020-07-30-vs2017config.png)
[image](vs2017config.png)

## 2.创建项目

在下载完上述工具后，打开VS2017->新建项目->C++, 应该有一个“Python扩展模块”的选项， 选择这个选项，如下图

![avatar](https://winterwindwang.github.io/assets/img/2020-07-30-cplusproject.png)
[image](cplusproject.png)

这里注意几个问题
 
 1、名称，这个名称将会决定你的Python包名，所以要慎重考虑
 
 2、解决方案名称影响不大，解决方案下可以有多个项目
 
 3、创建完的项目中，source file一般是以.c结尾的，这时候只需要文件重命名就行了，即将.c改成.cpp
 
 ## 3.配置项目
 
 创建完项目后，右击项目，选择项目属性，按如下设置配置项目环境
 
 |  项目   | 属性  |  设置值  |
|  ----  | ----  | ----   |
| 常规  | 常规->目标文件名 | 设置你想要在python端通过 ```from module import ...```中```moudel```的名称，如果你想保持与项目名一致，那么使用默认（所以如果项目名称取名时考虑这点，这时就不需要改，我尝试改了出了点小问题，这里推荐使用在创建项目时就考虑清楚）
| 常规  | 常规->目标文件扩展名 | 更改成```.pyd```
| 常规  | 项目默认值->配置类型 | 选择动态库(.dll) |
| C/C++ -> 常规  | 附加包含目录 | 如果你下载了python，那么选择python安装目录下的include的路径，如:```c:\Python36\include```。如果你使用的是Anconda，那么你应该将选择Anconda中的include路径，如:```C:\Anconda3\include``` |
| C/C++ -> 预处理器  | 预处理器定义 | 只在使用CPython方式调用C++时使用; 添加```Py_LIMITED_API``` |
| C/C++ -> 代码生成  | 运行库 | 选择“多线程DLL(/MD)” |
| 链接器 -> 常规  | 附加库目录 | 选择python中的libs的目录，如：```C:\Python36\libs```。 如果你使用的是Anconda3，那么你添加的目录应该是:```C:\Anconda3\libs``` |

项目配置完成，此时应该不会报错了。这时是直接按```ctrl+F5```（运行不调试），如果项目配置正确，则会出现一个类型控制台的python终端，如下图

![avatar](https://winterwindwang.github.io/assets/img/2020-07-30-pythonconsolo.png)
[image](pythonconsolo.png)

如果报了如下的错误：

```error : Python 3.6-32 is not installed. Please install Python 3.6-32 and try again.```
 
那么你只需要将Debug中的x86改成x64就行了，个人猜测python的版本是64位的，所以没办法使用32位的debugger。

![avatar](https://winterwindwang.github.io/assets/img/2020-07-30-debuggererror.png)
[image](debuggererror.png)
 
## 4.编写代码

如果完成了项目配置，并且弹出了类型控制一样的python终端，那么就可以着手代码的编写了。
这里涉及python向C++传递数组，我以Python调用C++实现的快速排序为例
这里首先贴上代码图

![avatar](https://winterwindwang.github.io/assets/img/2020-07-30-vs2017cpluspluscode.png)
[image](vs2017cpluspluscode.png)

初略的解释都在图中，现在就Python调用C++的函数进行解释

```
PyObject *Cplusplus_quicksort(PyObject *self, PyObject *args, PyObject *kwargs) {
	PyObject *listobj = NULL;   // 声明接受数组的PyObject对象
	int length = 0;            // 声明数组长度
	/* Parse positional and keyword arguments */
	static char* keywords[] = { "listobj", "length", NULL };  // 这里声明你需要接收几个参数，默认用法，最后一个NULL不用，默认添加的
	if (!PyArg_ParseTupleAndKeywords(args, kwargs, "Oi", keywords, &listobj, &length)) {
		return NULL;
	} // 这一步进行参数解析，将python传来的参数依次解析成 声明的参数， 根据声明的参数，设置“Oi”，我这里接受一个PyObject和int就写“Oi”，如果是三个整型数字那么就是"iii"
	int *data = new int[length];  // 根据传来的数组长度，声明数组
	// define a iterator to fetech the array data
	PyObject *iter = PyObject_GetIter(listobj);  // 定义一个PyObject的迭代器
	if (!iter) {
		PyErr_SetString(PyExc_TypeError, "The object is not iterable!");
		return NULL;
	}
	int i = 0;
	while (true) {
		PyObject *next = PyIter_Next(iter);  // 迭代取出数组的值
		if (!next) {
			break;
		}
		if (!PyLong_Check(next)) {
			PyErr_SetString(PyExc_TypeError, "Int or Long list is excepted!");
			return NULL;
		}
		long num = PyLong_AsLong(next);  // 将next解析成具体的数值
		data[i++] = num;
	}// data fetected ended!
	if (i != length) {
		PyErr_SetString(PyExc_TypeError, "Array length dismatch!");
		return NULL;
	}
	// quick sort  调用快速排序算法
	Parition(data, length, 0, length - 1);
	
	// construct result   构造返回排序好的数组
	PyObject * result = PyList_New(length);   // 声明一个List数组
	for (int i = 0; i < length; i++) {
		PyObject *item = Py_BuildValue("i", data[i]);  // 依次build每一个值
		PyList_SetItem(result, i, item);     // 添加到要返回的数组中
	}
	return result;
}
```

代码的运行结果如下图所示

![avatar](https://winterwindwang.github.io/assets/img/2020-07-30-pythonresult.png)
[image](pythonresult.png)

## 5.编译成python的包

完成上述的工作，说明代码能够成功地被python调用了，但是这时还无法直接在python环境中使用，现在将写好的这个代码打包成.whl文件

1.首先新建一个setup.py文件，用来编译package,代码如下，只需按提示修改
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-

from setuptools import Extension, setup

__version__ = '0.1.0'

cplusplus_src = ['src/Cplusplus.cpp']  # C++源文件
#cplusplus_dep = ['src/xxx.h']  C++ 头文件放置的地方，这里我没用到就不需要

cplusplus_ext = Extension('Cplusplus', cplusplus_src,#, depends=bchlib_dep,
                       extra_compile_args=['-std=c99'])

setup(name='cplusplus', version = __version__,
      ext_modules = [cplusplus_ext],
      description = 'A python wrapper module for the C++ library.',
      author = 'freeneuro',
      maintainer = 'freeneuro',
      license = 'GNU GPLv2',
      classifiers = [
        'Development Status :: 4 - Beta',
        'Intended Audience :: Developers',
        'License :: OSI Approved :: GNU General Public License v2 (GPLv2)',
        'Operating System :: POSIX :: Linux',
        'Programming Language :: C',
        'Programming Language :: Python :: 2.7',
        'Programming Language :: Python :: 3',
        'Topic :: Security :: Cryptography',
        'Topic :: Software Development :: Libraries :: Python Modules',
      ],
)
```
2.完成后，在setup.py的目录下，新建src目录，将VS2017项目中的.cpp放到该目录下

3.打开cmd，将目录更换到setup.py所在目录，使用如下命令进行打包，

```
python setup.py bdist_wheel
```
这里需要注意一点，如果只安装了anconda，那么需要激活anconda的base环境，再使用上面的命令。
还有一点，如果你环境中的python版本是3.6的，那么打出的包也是3.6的。那相要打包其他版本的怎么办呢？
很简单，你只需要在不同的python版本下使用上述命令，就能得到不同版本的package

4.package的安装

在setup.py同级目录会生成一个dist文件，进去后有个.whl文件，这时只要使用pip install 命令安装这个package就行了。

```
pip install cplusplus-0.1.0-cp36-cp36m-win_amd64.whl
```

![avatar](https://winterwindwang.github.io/assets/img/2020-07-30-finalresult.png)
[image](finalresult.png)

P.S. 在网上找了好多教程，最后发现还不如官网来的实在~~
## 参考

[https://docs.microsoft.com/en-us/visualstudio/python/working-with-c-cpp-python-in-visual-studio?view=vs-2017](https://docs.microsoft.com/en-us/visualstudio/python/working-with-c-cpp-python-in-visual-studio?view=vs-2017)


