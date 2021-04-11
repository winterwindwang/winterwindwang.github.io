---
layout: post
title: jupyter notebook中调用当前文件夹下文件的方法（持续更新）
category: 技术
tags: jupyter notebook, python, torch
keywords: 
---



> 导语：在使用jupyter notebook时，碰到.ipynb文件无法调用文件夹下模块，找了一些方法解决了一些问题，故记录下来

假设有如下目录结构

```
--tools
	-- modules
		-- __init__.py
		-- a.py
		-- b.py
	-- __init__.py
	-- image_process.py
	-- utils.py
main.ipynb
dataloader.py
```

在`mian.ipynb`中分别想调用各个文件的方法

1. 首先，最简单的调用`dataloader.py`中的方法，那么只需在`main.ipynb`中加入如下一句：

```
import dataloader
# 或者
from dataloader import *
```

2. 然后，调用`tools`子目录下的`image_tools.py`文件中的方法

```
首先需要在tools子目录下新建一个__init__.py文件，并添加如下代码
from tools import image_tools.py
注意：格式为 from 包名 import 模块名  （这样就可以被文件夹外的main.ipynb检索到？）
```

​	进一步地，如果`image_tools.py`文件调用了相同目录下的`utils.py`，那么在`image_tools.py`文件中引入

```
from . import utils
# 或者
from utils import xxx(具体方法)
```

​	最后，如果`image_tools.py `调用了`modules`文件夹下的`a.py`中的文件，那么

```
首先,在modules文件下，新建__init__.py文件，并在其中添加
from .a import xxx(foo)
from .b import xxx
# 或者
from . import a
from . import b

然后，在image_tools.py文件中写上
from .modules.a import xxx(foo)
from .modules.b import xxx
# 或者
from .modules import * (待验证)

```

 	如果目录`modules`文件夹下的`a.py`或`b.py`中的方法想要调用`utils.py`，我还没想到办法，待验证....

