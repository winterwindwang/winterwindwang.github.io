---
layout: post
title: Python打包、上传
category: 技术
keywords:
---

## 文件目录

文件目录如下图所示，

```python
proj/
  src/
    founctionA/
      __init__.py
      utils/
         __init__.py
      abc.py
  setup.py
```

## setup.py文件

find_package如果没指定包所在的位置时，那么生成的包里面什么都没有。

加入文件目录结构如上图所示：参考[这里](https://stackoverflow.com/questions/14417236/setup-py-renaming-src-package-to-project-name)，无论使用哪种package方法，都没法找到`founctionA`目录下所有的包含`__init__.py`的包，所以打包出来的文件都存在错误。看到博客提到`packages`包含的目录应该是包，也就是说这个目录下必须有`__init__.py`，所以，当上述的文件目录没有包含包标志文件的话，压根不会去查找src目录下的文件。（或许可以参考`find_packages`方法，编写自定义的找包函数），如果在src目录下添加`__init__.py`文件，可以解决问题，但是好像安装完成，既可以使用`import src`，也能使用`import simplelayout`，故这种方法的可用性存疑。

又找到一种方法，在不加`__init__.py`的前提下，使用如下代码（[参考](https://www.coder.work/article/7631747)），可以只生成`simplelayout.py`文件。

```python
packages=find_packages(where='src'),
package_dir={'': 'src'},
```

**注意**：看是否打包成功，可以看`build`目录下是否生成包含模块的`lib`文件

打包package（[参考](http://wsfdl.com/python/2015/09/06/Python%E5%BA%94%E7%94%A8%E7%9A%84%E6%89%93%E5%8C%85%E5%92%8C%E5%8F%91%E5%B8%83%E4%B8%8A.html)）

`python setup.py bdist_wheel `

## 上传到pypi前，使用twine检查生成的dist文件

首先需要安装twine包，`pip install twine`

然后使用命令`twine check dist/*`，如果显示如下输出，那说明你的文件没有问题，可以进行后续的上传

![image-20211120011818939](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211120011818939.png)

## 上传准备：[PyPi](https://pypi.org/)以及[PyPi-test](https://test.pypi.org/)账号的创建

这两个账号的信息可以完全一样，包括账号名，使用的邮箱。

注意：需要将account settings中的public email设置成某个验证的邮箱（如果没有添加其他邮箱，就直接使用下拉栏中默认的邮箱），否则无法创建API TOKEN。

使用以下命令上传包到pypi

```python
twine upload dist/*
```

**注意**: 这两个账号都需要注册。如果只是用了一个账号的app-token配置**.pypirc**文件，那么会出现如下错误：

![image-20211120011007250](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211120011007250.png)

分别使用两个账号的token配置**.pypirc**文件以后，上传成功了

![image-20211120011137531](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211120011137531.png)

**注意**：如果目录结构/代码发生了变化，那么要修改版本号，否则会出现以下错误

![image-20211120102332051](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211120102332051.png)

## Sphinx创建项目文档

注意：不要在vscode里面使用终端来允许sphinx-quickstart，因为这样会报错误；使用cmd命令行就没问题了。（可能是没有为vscode配置conda虚拟环境所导致的？）

错误：在使用命令`shpinx-apidoc -o source src`命令和`make html`命令（错误是后者导致的）,会出现如下错误

![image-20211120013358060](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211120013358060.png)

修改两个地方，问题就可以解决，虽然还是由一个警告，但是网页可以用。

打开`source/config.py`文件，

+ 第一处修改（[参考](https://blog.csdn.net/qq_42689684/article/details/103088152)）

```python
# import os
# import sys
# sys.path.insert(0, os.path.abspath('.'))
# 其中将上面一行代码修改成源代码所在的目录（猜测）

# 修改后的代码
import os
import sys
sys.path.insert(0, os.path.abspath('../src'))
```

+ 第二处修改（[参考](https://stackoverflow.com/questions/13516404/sphinx-error-unknown-directive-type-automodule-or-autoclass)）

```python
extensions = [] 

上述的错误可能是因为extensions没有参数造成的。可能因为有中文注释的缘故，不用最后一项"autodoc"，警告还是存在

extensions = [
    'sphinx.ext.todo',
    'sphinx.ext.viewcode',
    'sphinx.ext.autodoc'
]
```

完成后，需要将下项目推送到`github`，然后配置`Read the Docs`

## 推送步骤

1. 关联github（一定要是自己的github以及当作的项目）
2. 在github初始化一个项目，该项目中包含sphinx生成的source文件夹，然后将文件推到github上。
3. 然后build项目（好像会自动关联github中的webhook？）
4. 若没有自动关联，那么可以先在Read the Docs的项目设置中找到集成，然后点击创建会生成一个链接一样的**字符串**。记住该字符串，然后在github项目的设置中，找到webhook并新建hook，将`https://`加载字符串的前面，放到url里面。Select **Let me select individual events**, and mark **Branch or tag creation**, **Branch or tag deletion**, **Pull Requests** and **Pushes** events
