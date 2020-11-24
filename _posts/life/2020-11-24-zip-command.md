---
layout: post
title: 使用7zip命令进行有条件压缩
category: 生活
tags: 条件压缩
keywords: 生活,2020
description: 
---

## 7zip压缩排除特定目录

在整理实验结果的时候，训练的模型和实验数据放在一起。想从服务器上提取这些模型的时候，发现如果数据和模型一起压缩那么整个压缩包就太大了，而且速度也会很慢。因此，想到如果有某种方法能够在压缩的时候排除某些文件或目录，那就太好了。

在网上搜索后，发现[这个方法][1]，为了方便查询，将其记录在自己博客里面。

右键压缩的方法没办法完成这个任务。因此，必须得使用命令行(使用命令行必须将7zip的安装目录配置到环境变量)。

例如当前目录下有foo这个目录，其结构是这样的

```
$ tree
.
`-- foo
    |-- bar
    |   `-- hello.txt
    |   `-- world.txt
    `-- hello.txt

2 directories, 3 files
```

排除bar目录，则

```
7z a foo.7z foo -x!foo\bar
```

排除foo/hello.txt文件，则

```
7z a foo.7z foo -x!foo\hello.txt
```

排除所有hello.txt文件，则

```
7z a foo.7z foo -xr!hello.txt
```

排除所有txt文件，则

```
7z a foo.7z foo -xr!*.txt
```

排除所有目录文件，则

```
7z a foo.7z foo -xr!*
```

这些命令的关键参数是递归 -r[2][2]

未完待续...

参考资料

[1]: https://blog.fooleap.org/7zip-exclude-directory.html
[2]:https://sevenzip.osdn.jp/chm/cmdline/switches/recurse.htm