---
layout: post
title: Matplotlib绘图系列一；基础图形绘制
category: 技术
keywords: python, torch
tags: technical details
---

>本文旨在介绍matplotlib中简单图形的绘制，主要涉及：折线图、柱状图、散点图、多子图绘制及相关注意事项

环境：

```
Python==3.9.0
matplotlib==3.6
seaborn==0.12.2
```

# 前言

首先，导入所需的Python库

```python
import matplotlib
import matplotlib.pyplot as plt
import numpy as np
import seaborn as sn
matplotlib.use('TkAgg')
```

其次，指定需要绘制图的风格，字体等信息

```python
plt.style.use('ggplot') # 该命令指定以ggplot风格绘制图形。
# 使用以下命令可以查看matplotlib支持的所有风格
print(plt.style.library)  # 共有28种风格，其中有一些存在重叠。每种风格定义了其背景样式（颜色，底纹），以及绘制图案时的颜色集合）

font_plot = {
    'family': 'Times New Roman',  # 字体类型
    # 'color': 'darkred',		# 字体颜色
    'weight': 'medium',			# 不关键，可不写
    'style': 'italic',			 # 斜体
    'variant': 'small-caps',     # 不关键，可不写
    'stretch': 'extra-condensed', # 不关键，可不写
    'size': 12  # 关键，决定字体大小
}
# 一般用法如下
font14 = {'family': 'Times New Roman',
          'size': 14}
```

接着，定义简单的数据集作为绘制数据

```python
datas = [
    [100.0, 90.4, 86.7, 92.0, 52.4, 37.3, 38.5, 33.5],
    [100.0, 91.5, 88.3, 92.6, 52.1, 38.5, 36.3, 34.1],
    [100.0, 91.5, 88.4, 94.5, 51.8, 37.1, 37.5, 30.9],
    [100.0, 92.2, 88.1, 93.9, 52.9, 37.6, 38.3, 32.1],
    [100.0, 91.9, 88.5, 94.5, 53.4, 37.2, 38.8, 30.7],
    [100.0, 92.2, 88.7, 94.4, 53.0, 37.0, 38.6, 32.0],
    [100.0, 91.8, 89.1, 94.9, 53.8, 37.6, 40.0, 32.7],
    [100.0, 92.0, 88.2, 94.2, 54.2, 37.0, 39.9, 30.9],
    [100.0, 92.3, 89.3, 94.1, 53.2, 34.8, 38.8, 31.2],
    [100.0, 92.2, 88.9, 94.5, 54.3, 35.1, 39.7, 31.3],
] # 数据维度是 10 * 8，每一列表示不同模型，每一行表示不同实验设置

column_names = ["M1", "M2", "M3", "M4", "M5", "M6", "M7", "M8"]
row_names = ["Exp1", "Exp2", "Exp3", "Exp4", "Exp5", "Exp6", "Exp7", "Exp8"]
```

最后定义绘制图形的marker，以及颜色

```python
markers = [".", "o", "v", "^", "<", ">", "*", "x", "D", "d", "2", "p", "+"]
colors = ['#E24A33', '#348ABD', '#988ED5', '#777777', '#FBC15E', '#8EBA42', '#FFB5B8', '#bcbd22']
```

# 一、折线图

### 1.1 单个折线图

```python
fig = plt.figure()
x_axis = list(range(len(steps)))  # length:  10
data = np.asarray(resnet50_data_conv_different_steps).T  # shape: [8, 10]
for i, row in enumerate(data):
    # row length: 10
    plt.plot(x_axis, row, color=colors[i % len(colors)], marker=markers[i], label=legend_label[i])
plt.xticks(x_axis, steps, font=font14)
plt.ylabel("Y-axis-label", font=font14)
plt.xlabel("X-axis-label", font=font14)
plt.legend()
plt.show()
```

### 1.2 多子图绘制

```python
fig, axs = plt.subplots(nrows=2, ncols=5, figsize=[13, 10], sharex=True, sharey=True,  constrained_layout=True)  # default is [6.4, 4.8]  constrained_layout=False
for idx, ax in enumerate(axs.flat):
    x_axis = list(range(len(steps)))  # length:  10
    data = np.asarray(resnet50_data_conv_different_steps).T  # shape: [8, 10]
    for i, row in enumerate(data):
        # row length: 10
        ax.plot(x_axis, row, color=colors[i % len(colors)], marker=markers[i], label=legend_label[i])
    ax.set_xticks(x_axis, steps, font=font12, rotation=-30)
    ax.set_title(title_list[idx])


lines_labels = [ax.get_legend_handles_labels() for ax in fig.axes]
lines, labels_leg = [sum(lol, []) for lol in zip(*lines_labels)]

fig.legend(lines[:8], labels_leg[:8], ncol=8, bbox_to_anchor=(0.85, -0.01))
fig.text(0, 0.5, 'Y-axis-label', va='center', rotation='vertical', font=font12)
fig.text(0.45, 0, 'X-axis-label', va='center', rotation='horizontal', font=font12)
plt.savefig("analysis_example_subplot.png", bbox_inches='tight',
                    pad_inches=0, dpi=300)
plt.show()
```

# 二、柱状图

### 2.1 单个柱状图绘制

```python
title_list = [f"data_{i}" for i in range(10)]

def plot_three_bar_figure(data_lsit, xticks, title_name, bar_width=0.2, save_fig=False):
    # 创建示例数据
    method_names = ['dropout', 'uniform', 'normal']
    data_dropout = data_lsit[0]  # length: 8
    data_uniform = data_lsit[1]
    data_normal = data_lsit[2]

    data = np.stack([data_dropout, data_uniform, data_normal], axis=-1)   # shape: [8, 3]
    # 创建X轴刻度位置
    x = np.arange(len(data_normal))  # length: 8
    # 绘制柱状图
    for i in range(len(method_names)):  # length: 3
        plt.bar(x + i * bar_width, data[:, i], bar_width, label=method_names[i], color=colors[i])
    # 设置X轴标签
    plt.xlabel('Models', font=font14)
    plt.xticks(x + bar_width, xticks, font=font14, rotation=-15)
    # 设置Y轴标签
    plt.ylabel('Y-axis-label', font=font14)
    # 添加图例
    plt.legend(loc='upper right')
    # # 设置标题
    plt.title(title_name)
    # 调整子图之间的间距
    plt.tight_layout()
    if save_fig:
        plt.savefig(f"analysis_example_bar.pdf", bbox_inches='tight',
                    pad_inches=0, dpi=300)
        plt.savefig(f"analysis_example_bar.png", bbox_inches='tight',
                    pad_inches=0, dpi=300)
    # 显示图形
    plt.show()
# 注意，这里使用了新得数据集
data = [
    [69.1, 73.7, 75.5, 68.5, 100, 68.7, 44.2, 62.9],
    [76.3, 79.9, 84, 73.8, 99.9, 69.7, 45.7, 63.3],
    [75.9, 80.4, 83.5, 74.1, 100, 69.9, 47.3, 64.5],
]

plot_three_bar_figure(data, legend_label, title_name=title_list[0], save_fig=False)
```

### 2.2 多子图绘制

```python
title_list = [f"data_{i}" for i in range(10)]

def plot_three_bar_figure(axes, data_lsit, x_axis, title_name, bar_width=0.2):
    # 创建示例数据
    method_names = ['dropout', 'uniform', 'normal']
    data_dropout = data_lsit[0]  # length: 8
    data_uniform = data_lsit[1]
    data_normal = data_lsit[2]

    data = np.stack([data_dropout, data_uniform, data_normal], axis=-1)   # shape: [8, 3]
    # 创建X轴刻度位置
    # 绘制柱状图
    for i in range(len(method_names)):  # length: 3
        fill_plot = ['/', 'x', '\\']
        axes.bar(x_axis + i * bar_width, data[:, i], bar_width, hatch=fill_plot[i],
                 label=method_names[i], color=colors[i], edgecolor='white')
    
    # # 设置标题
    axes.set_title(title_name)
    # 调整子图之间的间距

data = [
    [69.1, 73.7, 75.5, 68.5, 100, 68.7, 44.2, 62.9],
    [76.3, 79.9, 84, 73.8, 99.9, 69.7, 45.7, 63.3],
    [75.9, 80.4, 83.5, 74.1, 100, 69.9, 47.3, 64.5],
]

bar_width = 0.2
fig, axs = plt.subplots(nrows=2, ncols=5, figsize=[13, 10], sharex=True, sharey=True,  constrained_layout=True)  # default is [6.4, 4.8]  constrained_layout=False
for idx, ax in enumerate(axs.flat):
    x_axis = np.arange(len(data[0]))  # length: 8
    for i, row in enumerate(data):
        plot_three_bar_figure(ax, data, x_axis, title_name=title_list[i])
    ax.set_xticks(x_axis + bar_width, legend_label, font=font12, rotation=90)
    ax.set_title(title_list[idx])

lines_labels = [ax.get_legend_handles_labels() for ax in fig.axes]
lines, labels_leg = [sum(lol, []) for lol in zip(*lines_labels)]
fig.legend(lines[:3], labels_leg[:3], ncol=3, bbox_to_anchor=(0.65, -0.01))

fig.text(0, 0.5, 'Y-axis-label', va='center', rotation='vertical', font=font12)
fig.text(0.45, 0, 'Models', va='center', rotation='horizontal', font=font12)

# plt.savefig(f"analysis_example_bar.pdf", bbox_inches='tight', pad_inches=0, dpi=300)
plt.savefig(f"analysis_example_bar_subpolt.png", bbox_inches='tight', pad_inches=0, dpi=300)
# plt.show()
```

# 3. 散点图

散点图无多子样例

```python
label1 = ["A1", "A2", "A3", "A4", "A5"]
label2 = ["A1-S", "A2-S", "A3-S", "A4-S", "A5-S"]

markers = [".", "o","v", "^","<",">","*", "x", "D","d", "2", "p", "+"]
colors = ['#1f77b4','#ff7f0e','#2ca02c','#d62728','#9467bd','#8c564b','#e377c2', '#7f7f7f', '#bcbd22', '#17becf']

markers1 = ["o","o","o","o","o"]
markers2 = ["v","v","v","v","v"]
colors1 = ['#1f77b4','#ff7f0e','#2ca02c','#d62728','#9467bd']



data_x = {
    "Ori": [55.57, 66.86, 71.21, 66.93, 72.26],
    "S":  [61.64, 70.71, 72.01, 68.29, 73.93]
}

data_y = {
    "Ori": [3.709545851, 3.538772821, 3.847475052, 3.89070797, 3.351880789],
    "S": [3.642693996, 3.538482189, 3.550876141, 3.903666258, 4.207882881]
}


markers3 = ['.',',', 'o','v','^','<','>','8','s','p','*','+','D','d','x','|','_']

fig = plt.figure()
def scatter_plot(data1, data2, markers, type='Ori'):
    for i, (p1, p2, m) in enumerate(zip(data1[type], data2[type], markers)):
        if type == "Ori":
            plt.scatter(p1, p2, marker=m, label=label1[i], color=colors[i], s=60)
        else:
            plt.scatter(p1, p2, marker=m, label=label1[i]+"-S", color=colors[i], s=60)

scatter_plot(data_x, data_y, markers=markers3[:5], type='Ori')
scatter_plot(data_x, data_y, markers=markers3[6:], type='S')
plt.ylabel("data y-axis")
plt.xlabel("data xaxis")

plt.legend(ncol=2)
plt.savefig(f"analysis_example_scatter.png", bbox_inches='tight', pad_inches=0, dpi=300)
plt.show()
```

