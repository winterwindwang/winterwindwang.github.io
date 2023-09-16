---
layout: post
title: ubuntu shell/window bat 批处理程序学习
category: 技术
keywords: ubuntu, shell, window, bat
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

# window bat 批处理程序学习
新建.bat文件以后，需要改变其文件格式以及编码格式，如参考资料2所示

```bat
@echo off
set model_names=faster_rcnn mask_rcnn retina_net
set eval_images_num=20 30 40 50
set block_type=0 1 2 3 4 5

FOR %%A IN (%model_names%) DO (
	FOR %%B IN (%eval_images_num%) DO (
		FOR %%C IN (%block_type%) DO (
			E:\software\Anaconda3\envs\ml_pytorch3d\python.exe TDE_location_natural_images_open3d_simplify.py --model_name %%A --eval_images %%B --block_idx %%C
			Echo ************** %%A %%B %%C Done **************
		)
	)
)
pause
Rem Echo test #

Rem 注意几点:1、Rem类似于python中的#,作用是注释；2、所有关键词前后都必须加空隔，如果在DO后面不加空格，那么程序将会报"处理时出错 DO(。"。
暂时只有这个使用的需求，后续需求再补充
```
# 参考资料

1. https://juejin.cn/post/7131625896825323550
2. https://blog.csdn.net/qq_33468857/article/details/88963725