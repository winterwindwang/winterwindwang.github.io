---
layout: post
title: Python移动文件等操作
category: 技术
keywords: python
tags: technical details
---

# Python移动文件

```python
import os
import numpy as np
import shutil

ratio = 0.8

source_data_root = r'F:\DataSource\comprehensive_cars\images_filtered'
dest_data_root = r'F:\DataSource\comprehensive_cars\New'

total_data = os.listdir(source_data_root)
val_len = int(len(total_data) * (1-ratio))
train_len = len(total_data)- val_len
np.random.shuffle(total_data)

print(f"Train len: {train_len}, Val len: {val_len}")

train_file = total_data[:train_len]
val_file = total_data[train_len:]


def move_src_file2dest(file_list, folder_name, source_root, dest_root):
    """
    move the file from a folder to anther folder of dest_root
    :param file_list:
    :param folder_name:
    :param source_root:
    :param dest_root:
    :return:
    """
    for file in file_list:
        src_path = os.path.join(source_root, file)
        dest_path = os.path.join(dest_root, folder_name, file)
        shutil.copy(src_path, dest_path)

# move_src_file2dest(val_file, "val", source_root=source_data_root, dest_root=dest_data_root)
# move_src_file2dest(train_file, "train", source_root=source_data_root, dest_root=dest_data_root)


def process_label_file(source_label_file, file_list):
    file_str = ""
    another_str = ""

    with open(source_label_file, 'r') as fr:
        lines = fr.readlines()
        for line in lines:
            line_split = line.split(',')
            if line_split[0] in file_list:
                file_str += line
            else:
                another_str += line
    return file_str, another_str


print("Statistic Result: ")
source_label_file = r'F:\DataSource\comprehensive_cars\file_label.txt'
# train_str, val_str = process_label_file(source_label_file, train_file)
#
# with open(os.path.join(dest_data_root, "train_lab.txt"), 'w') as fw:
#     fw.writelines(train_str)
#
# with open(os.path.join(dest_data_root, "val_lab.txt"), 'w') as fw:
#     fw.writelines(val_str)


def filter_file_by_label(label_file):
    """复制文件"""
    src_root = r'F:\DataSource\comprehensive_cars\images'
    dest_root = r'F:\DataSource\comprehensive_cars\images_filtered'
    with open(label_file, 'r') as fr:
        lines = fr.readlines()
        for line in lines:
            line_split = line.split(',')
            src_path = os.path.join(src_root, line_split[0])
            target_path = os.path.join(dest_root, line_split[0])
            try:
                shutil.copy(src_path, target_path)
            except:
                pass

# filter_file_by_label(source_label_file)

from collections import Counter
def static_label_class(label_file):
    class_set = []
    with open(label_file, 'r') as fr:
        lines = fr.readlines()
        for line in lines:
            line_split = line.split(',')
            class_set.append(int(line_split[1]))

    return Counter(class_set).most_common()

print("Train set: ", static_label_class(r'F:\DataSource\comprehensive_cars\New\train_lab.txt'))
print("Val set: ", static_label_class(r'F:\DataSource\comprehensive_cars\New\val_lab.txt'))
```

