---
layout: post
title: matplotlib 绘图总结
category: 技术
tags： 双y轴坐标
---

> 记录各种使用matplotlib画图技巧，持续更新
>
> 1. 同x轴两个y轴坐标
> 2. 折线图的画法

## 1 同x轴两个y轴坐标

```python
import numpy as np
import matplotlib.pyplot as plt
# data
y1 = np.arange(10, 20)
y2 = np.arange(0, 10)
x = np.arange(0, 10)
# plot the data
figure = plt.figure()
# line 1
ax1 = fig.add_subplot(111)
line1 = ax1.plot(x, y1, marker='o', label='y1 legend')
ax1.set_ylabel("Y1 label")
# ax1.set_ylim((10,20)) 刻度，是list或者tuple的格式的数据，(start, end)

ax2 = ax1.twinx()
line2 = ax2.plot(x, y2, 'r', marker='o', label='y2 legend') # 'r'指示红色线
ax2.set_ylabel('Y2 label')

# set the legend
lines = line1 + line2
labels = [l.get_label() in lines]
ax1.legend(lines, labels, loc=7)  # loc

# set the grid 
plt.grid(axis='y') # 如果没有指定axis='y'，那么就是横纵的网格，如果指定'y’，那么只有横坐标有grid line
plt.savefig("figure1.png", dpi=300)
plt.show()
```

### tips

+ legend中放置的位置

| Location String | Location Code |
| --------------- | ------------- |
| 'best'          | 0             |
| 'upper right'   | 1             |
| 'upper left'    | 2             |
| 'lower left'    | 3             |
| 'lower right'   | 4             |
| 'right'         | 5             |
| 'center left'   | 6             |
| 'center right'  | 7             |
| 'lower center'  | 8             |
| 'upper center'  | 9             |
| 'center'        | 10            |

+ 在图中现实数学符号

  公式与latex的语法一样，使用`$$`将公式包起来就行了

## 2 折线图的画法

```python
# data
y_data = [40, 50， 60， 70， 80]
x_data = [1, 2, 3, 4, 5]
# 只有使用ax才能给图片加横纵坐标的名称
fig, ax = plt.subplots()
ax.plot(x_data, y_data, marker='o')
ax.set_xlim(0,5)
ax.set_ylim(0,100)
ax.set_xlabel("X label")
ax.set_ylabel("Y label")
plt.grid(axis='y')
plt.show()
```

