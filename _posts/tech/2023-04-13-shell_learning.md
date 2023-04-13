---
layout: post
title: ubuntu shell 批处理程序学习
category: 技术
keywords: ubuntu, shell
tags: technical details
---

# ubuntu shell 批处理程序学习

近期有大量使用命令行执行命令的需求，为了方便遂学习如何使用shell来进行批处理。任务是使用同一命令对同一文件夹下不同视频进行检测，因此每次执行都需要更换视频的名称。当整条命令很长时，更换起来很繁琐。因此，拟采用批处理程序批量地对视频进行处理。具体流程如下：

1. 指定路径（以后替换）
2. 获取指定路径下所有的文件名
3. 使用循环依次处理所有文件名

```bash
#!/bin/bash
DATA_ROOT="xxx/xxx/recorded_data"  # 指定文件夹
echo $DATA_ROOT
FILE_NAMES=$(ls $DATA_ROOT)  # 获取指定文件夹下所有文件名
FOR $filename in $DATA_ROOT; do
	python ../detect.py \
	--source $DATA_ROOT/$filename
done
```



# 参考资料

1. https://juejin.cn/post/7131625896825323550