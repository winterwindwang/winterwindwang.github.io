---
layout: post
title: python快速找到目录下所有特定后缀的文件
category: 技术
keywords: python, glob
tags: technical details
---

# python快速找到目录下所有特定后缀的文件

```
--Dataset
	--sub_folder1
		-- image01.png
		-- image02.png
		-- ...
	--sub_folder1
		-- image01.png
		-- image02.png
		-- ...
```



假设你需要读取以上目录结构中所有的文件，一般做法如下

```python
import os
path = r'Dataset'
image_list = []
for sub_folder in os.listdir(path):
    image_list.extend(os.listdir(os.path.join(path, sub_folder)))
   
```

可以看到，上面的代码毕竟冗长，用`glob`库可以用一行代码接近

```python
from glob import glob
file_list = glob(os.path.join(path, "*/*.JPEG"))
```



