---
layout: post
title: Runge-Kutta方法
category: 数值求解方法
keywords: numerical solvtion method
tags: technical details
---

> 在上课期间就没搞明白Runge-Kutta(龙格-库塔)的计算步骤以及使用场景，始终是一个心结。趁有空就认真学习了该方法，俗话说好记性不如烂笔头，记录下来以后也便于复习。也趁这个机会，开一个数值求解方法的学习记录。

# Runge-Kutta（龙格-库塔）方法

## 1. 基本思想

Runge-Kutta(龙格-库塔)方法是一类用于求解常微分方差数值解的迭代方法。这类方法特别适用于解决初值问题，即给定一个初值条件下的微分方程。**Runge-Kutta方法的核心实现是通过在每一步计算中使用多个中间点的斜率来改进欧拉方法的精度**。其中最著名的是四阶Runge-Kutta方法（RK4），它是一个平衡了效率与准确性的使用算法。

对于函数$y=y(x)$，设$y_n=y(x_n)$，将$y(x_{n+1})$在$x_n$处展开为Taylor级数：
$$
y(x_{n+1}) = y(x_n) + h y'(\epsilon), x_n < \epsilon < x_{n+1}
$$
其中$h=x_{n+1}-x_n$，$\epsilon=x_n + ph, 0<p<1$表示相对于$x_n$的增量。

【注意】函数$y(x)$在区间$[x_n,x_{n+1}]$的斜率$k$也可也表示成：
$$
k=\frac{y(x_{n+1})-y(x_{n})}{x_{n+1}-x_n}=\frac{y(x_{n+1})-y(x_{n})}{h},h=x_{n+1}-x_n
$$
利用$y'(x_n)=f(x_n, y_n)$(**注：所有一阶常微分方程都可以写成这种显示形式，利用$f(x_n,y_n)$来估计斜率，逐步推进到下一个点$y_{n+1}$；很多实际问题中，系统的演化速率$y'$通常是时间和状态变量的函数，即$f(x,y)$**)可得：
$$
y(x_{n+1})=y(x_n)+hf(\epsilon, y(\epsilon))=y(x_n)+hf(x_n + ph, y_n(x_n+ph))
$$
其中，$f(\epsilon, y(\epsilon))$为区间$(x_n, x_{n+1})$上的**平均斜率**，记为$\bar{k}$。因此，只要能设法提供一种算法求得$\bar{k}$，就可相应地导出一种计算格式。

那么$r$**阶Runge-kutta方法**可以表示成
$$
\begin{cases}
k_1 = f(x_n, y_n) \\
k_2 = f(x_n+a_2k_1, y_n+hb_{21}k_1) \\
\cdot \\
\cdot \\
\cdot \\
k_r = f(x_n + a_rh, y_n + h\sum_{j=1}^{r-1}b_{rj}k_j)
\end{cases}
$$
那么$\bar{k} = \sum_{i=1}^{r}c_ik_i, y_{n+1}=y_n + h \sum_{i=1}^{r}c_ik_i$

当$r=2$时，2阶Runge-Kutta可以表示成
$$
\begin{cases}
y_{n+1} = y_n + h(c_1k_1+c_2k_2) \\
k_1 = f(x_n, y_n) \\
k_2 = f(x_n + a_1h, y_n + h b_21k_1)
\end{cases}
$$
其中$c_1, c_2, a_1, b_{21}$都是未知数，可以通过对$y(x_{n+1})$施加泰勒展开。然后通过计算$y_{n+1}-y(x_{n+1})=O(h^3)$确定未知数的值。

## 2. 基本计算步骤

上节介绍了Runge-kutta方法的基本方法，这节详细阐述二阶和四阶Runge-kutta的基本计算步骤和推导。

### 2.1 二阶Runge-Kutta

在改进的Euler格式的基础上加以修改，即在区间$[x_n, x_{n+1}]$内，除$x_n$外再取区间中任一点p，则有：
$$
x_{n+p} = x_n + ph, 0<p<1
$$
取$x_n$和$x_{n+1}$两点处的斜率$k_1$和$k_2$加权平均作为平均斜率$\bar{k}$，即
$$
\bar{k} = (1-\lambda)k_1 + \lambda k_2, \lambda 为待定系数
$$
这样构造处的计算格式为：
$$
y_{n+1} = y_n + h[(1-\lambda)k_1 + \lambda k_2] \\
k_1 = f(x_n, y_n) \\
k_2 = f(x_{n+p}, y_n + phk_1)  \tag{1}
$$
(1)式称为二阶Runge-kutta格式，其中p，λ为待定系数。在确定p，λ的同时，使二阶Runge-Kutta格式具有较高的精度，具体推导如下：

根据假设$y_n=y(x_n)$，有：
$$
k_1 = f(x_n, y_n)=y'(x_n) \\
k_2 = f(x_{n+p}, y_n + phk_1)=f(x_n+ph, y_n + phk_1)
$$
**将$k_2$右端在点$(x_n,y_n)$处展开为Talyor级数**：
$$
k_2 = f(x_n, y_n)+ ph \cdot f_x(x_n, y_n) + ph \cdot y'(x_n) \cdot f_y(x_n, y_n) + O(h^2)
$$
而
$$
ph \cdot f_x(x_n, y_n) + ph\cdot y'(x_n) \cdot f_y(x_n, y_n) = ph \cdot f'(x_n, y_n) = phy''(x_n)
$$
其中由**微分方程**可得
$$
y'(x_n)=f(x_n,y(x_n))=f(x_n,y_n)\\
y''(x_n)=f_x(x_n, y_n)+f_y(x_n,y_n)y'(x_n)\\
=f_x(x_n, y_n)+f_y(x_n,y_n)f(x_n,y_n)
$$
故
$$
k_2 = f(x_n, y_n) + ph \cdot y''(x_n) + O(h^2) = y'(x_n) + phy''(x_n)+ O(h^2)
$$
将$k_1$和$k_2$代入$y_{n+1}=y_n + h[(1-\lambda)k_1 + \lambda k_2]$中，有：
$$
y_{n+1}=y_n + h[(1-\lambda)k_1 + \lambda k_2] = y(x_n) + hy'(x_n)+\lambda ph^2y''(x_n)+\lambda O(h^3)
$$
再将$y(x_{n+1})$在点$(x_n, y_n)$处展开为Taylor级数：
$$
y(x_{n+1}) = y(x_n + h) = y(x_n) + hy'(x_n) + \frac{1}{2}h^2y''(x_n)+O(h^3)
$$
两式相减得：
$$
y(x_{n+1}) - y_{n+1} = (\frac{1}{2}h^2 - \lambda ph^2)y''(x_n) - \lambda O(h^3)
$$
因此，当$\lambda p=\frac{1}{2}$时，二阶Runge-Kutta格式具有为2阶精度。可见二阶Runge-kutta格式有很多具体形式：

若取$\lambda=\frac{1}{2}, p=1$,则可得改进的Euler格式；

若取$\lambda=\frac{3}{4}, p=\frac{2}{3}$，则可得Heun（休恩）格式；

若取$\lambda=1, p=\frac{1}{2}$，则可得变形的Euler格式，即中点格式：
$$
\begin{cases}
y_{n+1} = y_n + hk_2\\
k_1 = f(x_n, y_n) \\
k_2 = f(x_{n+\frac{1}{2}}, y_n+\frac{h}{2}k_1)
\end{cases}
$$

### 四阶Runge-Kutta格式

在区间$[x_n, x_{n+1}]$内，使用两个不同的点可以构造处二阶Runge-Kutta格式。依次规律，在区间$[x_n, x_{n+1}]$内，取3个不同的店可以构造出三阶Runge-Kutta格式；取4个不同的点可以构造四阶Runge-Kutta格式。对此可以加以证明。在实际中，应用最广泛的是四阶经典的Runge-Kutta格式：
$$
\begin{cases}
y_{n+1} = y_n + \frac{h}{6}(k_1 + 2k_2+2k_3+k_4)\\
k_1 = f(x_n, y_n) \\
k_2 = f(x_n+\frac{1}{2}h, y_n+\frac{h}{2}k_1) \\
k_3 = f(x_n+\frac{1}{2}h, y_n+\frac{h}{2}k_2) \\
k_4 = f(x_n+h, y_n+hk_3)
\end{cases}
$$
四阶Runge-Kutta方法的优点如下：

（1）它是一种高精度的单步法，可达四阶精度$O(h^5)$

（2）数值稳定性较好；

（3）只需要一阶导数，无需明确定义或计算其他高阶导数；

（4）只需给出$y_n$就能计算出$y_{n+1}$，所以能够自启动；

（5）编程容易。

四阶Runge-Kutta法除经典的格式外，还有其他的格式。例如：

（1）Kutta格式
$$
\begin{cases}
y_{n+1} = y_n + \frac{h}{8}(k_1 + 3k_2+3k_3+k_4)\\
k_1 = f(x_n, y_n) \\
k_2 = f(x_n+\frac{1}{3}h, y_n+\frac{h}{3}k_1) \\
k_3 = f(x_n+\frac{2}{3}h, y_n - \frac{h}{3}k_2+hk_2) \\
k_4 = f(x_n+h, y_n+hk_1-hk_2+hk3)
\end{cases}
$$
（2）Gill格式
$$
\begin{cases}
y_{n+1} = y_n + \frac{h}{6}(k_1 + (2-\sqrt{2})k_2+(2+\sqrt{2})k_3+k_4)\\
k_1 = f(x_n, y_n) \\
k_2 = f(x_n+\frac{1}{2}h, y_n+\frac{h}{2}k_1) \\
k_3 = f(x_n+\frac{1}{2}h, y_n+\frac{\sqrt{2}-1}{2}hk_1+\frac{2-\sqrt{2}}{2}hk_2) \\
k_4 = f(x_n+h, y_n-\frac{\sqrt{2}}{2}hk_2+\frac{2+\sqrt{2}}{2}hk_3)
\end{cases}
$$


## 3. 例子解析

1. 经典4阶Runge-Kutta方法的程序（见代码实现）

2. Adams隐式3阶方法的程序，预估步分别用向前Euler格式，改进的Euler格式，4阶Runge-Kutta格式（待续...）

3. 求解如下的问题
   $$
   \begin{cases}
   y'(x)=-x^2y^2,\\
   y(0)=3
   \end{cases}
   $$
   分别取步长h为$0.1, \frac{0.1}{2}, \frac{0.1}{4}, \frac{0.1}{8}$计算$y(1.5)$

   解：由于$h=0.1$，需要从$x=0$到$x=1.5$共进行15次迭代（因为$\frac{1.5-0}{0.1}=15$）

   由$y(0)=3$可知，x=0，y=3。

   由经典四阶Runge-Kutta知
   $$
   \begin{cases}
   y_{n+1} = y_n + \frac{h}{6}(k_1 + 2k_2+2k_3+k_4)\\
   k_1 = f(x_n, y_n) \\
   k_2 = f(x_n+\frac{1}{2}h, y_n+\frac{h}{2}k_1) \\
   k_3 = f(x_n+\frac{1}{2}h, y_n+\frac{h}{2}k_2) \\
   k_4 = f(x_n+h, y_n+hk_3)
   \end{cases}
   $$
   第一次迭代：

   $k_1 = f(x_n,y_n)=y'(x)=-0^2\times3^2=0$

   $k_2=f(x_n+\frac{1}{2}h,y_n+\frac{1}{2}hk_1)=-(0+0.5*0.1)^2*(3+0.5*0)^2=-0.0225$

   $k_3=f(x_n+\frac{1}{2}h,y_n+\frac{1}{2}hk_2)=-0.0025*(3-0.5*0.0225)^2\approx-0.02248312$

   $k_4=f(x_n+h,y_n+hk_3)=-(0+0.1)^2*(3-0.1*0.0223315)^2=-0.089865$

   $y(0+0.1)=y(0.1)=y(0)+\frac{1}{6}(k_1+2k_2+2k_3+k_4)\\=3+\frac{1}{6}(0-0.0225-0.0223315-0.089866)=2.9977$

   第二次迭代：

   $k_1 = f(x_n,y_n)=y'(0.1)=-(0.1)^2\times(2.9700785)^2=-0.089821$

   $k_2=f(x_n+\frac{1}{2}h,y_n+\frac{1}{2}hk_1)=\\-(0.1+0.5*0.1)^2*(2.9700785+0.5*(-0.0882136))^2=-0.20149$

   $k_3=f(x_n+\frac{1}{2}h,y_n+\frac{1}{2}hk_2)=\\-(0.15)^2*(2.9700785-0.5*0.192629)^2\approx-0.2007392$

   $k_4=f(x_n+h,y_n+hk_3)=-(0.1+0.1)^2*(2.9700785-0.1*0.18581669)^2=\\-0.354484$

   $y(0+0.1)=y(0.1)=y(0)+\frac{1}{6}(k_1+2k_2+2k_3+k_4)\\=2.9700785+\frac{1}{6}(-0.0882136-0.192629-0.192629-0.3484533)=2.97619$

4. 给出$x=1.5$处的误差，并计算误差阶。（精确解为$y=\frac{3}{1+x^3}$）

## 4. 代码实现

```python
def f(x, y):
    """定义微分方程"""
    return -x**2 * y**2

def runge_kutta_solver(x0, y0, x_end, h):
    """
    使用四阶Runge-Kutta方法求解微分方程
    :param x0: 初始x值
    :param x0: 初始y值
    :param x_end: 结束x值
    :param h: 步长
    return: 计算过程中所有(x,y)点的列表
    """
	points = [(x0, y0)]
    xn, yn = x0, y0
    while xn < x_end:
        k1 = f(xn, yn)
        k2 = f(xn + h/2, yn+h*k1/2)
        k3 = f(xn + h/2, yn+h*k2/2)
        k4 = f(xn + h, yn+h*k3)
        
        yn = yn + h*(k1+2*k2+2*k3+k4)/6
        xn = xn + h
        
       points.append([xn,yn]) 
   	return points
x0 = 0
y0 = 3
h=0.1
x_end=1.5
runge_kutta_solver(x0,y0,x_end, h)
```

## 5. 使用场景

计算机视觉中涉及许多动态过程（如视频分析、运动建模），这些过程可以用微分方程描述，而Runge-Kutta可用于数值求解。

见[通义千问](https://lxblog.com/qianwen/share?shareId=9ac7fd91-606d-474a-992f-fa635e18b1d4)回答。

## 6. 参考资料

1. [第二节基本计算步骤](https://blog.csdn.net/SanyHo/article/details/107017076)
2. [B站讲解视频](https://www.bilibili.com/video/BV1rF41187YM/?share_source=copy_web&vd_source=5b0aa7deba380c6b5ea6f80c8717d5f3)
3. [ustc课件](http://staff.ustc.edu.cn/~rui/textbooks/nm/slides/num-ode-rk.html#/num-homework-ode)

## 7. 附录

多元函数的泰勒展开式如下：

+ 一元函数在点$x_k$处的泰勒展开式为：

$$
f(x)=f(x_k) + (x-x_k)f'(x_k)+\frac{1}{2!}(x-x_k)^2f''(x_k)+O^n
$$

+ 二元函数在点$(x_k,y_k)$处的泰勒展开式为：

$$
f(x,y)=f(x_k,y_k)+(x-x_k)f_x’(x_k,y_k)+(y-y_k)f_y'(x_k,y_k)+\frac{1}{2!}(x-x_k)^2f''_{xx}(x_k,y_k)+\\ \frac{1}{2!}(y-y_k)^2f''_{yy}(x_k,y_k)+\frac{1}{2!}(x-x_k)(y-y_k)f''_{xy}(x_k,y_k)+\frac{1}{2!}(x-x_k)(y-y_k))f''_{yx}(x_k,y_k)+O^n
$$

+ 多元函数在点$(x_k,y_k)$处的泰勒展开式为：

$$
f(x^1,x^2,...,x^n)=f(x_k^1,x_k^2,...,x_k^n) + \sum^n_{i=1}(x^i-x_k^i)f'_{x^i}(x_k^1,x_k^2,...,x_k^n)+\\ \frac{1}{2!}\sum^n_{i,j=1}(x^i-x^i_{k})(x^j-x^j_{k})f''_{ij}(x_k^1,x_k^2,...,x_k^n)+O^n
$$

把Taylor展开式写成矩阵的形式：
$$
f(\text{x}) = f(\text{x_k})+|\nabla f(\text{x}_k)|+\frac{1}{2!}[\text{x-x}_k]^TH(\text{x}_k)[\text{x-x}_k]+O^n
$$
其中：
$$
H(\text{x}_k)=
\begin{bmatrix}
\frac{\partial^2f(x_k)}{\partial x^2_1}, \frac{\partial^2f(x_k)}{\partial x_1\partial x_2},...,\frac{\partial^2f(x_k)}{\partial x_1\partial x_n}\\
\frac{\partial^2f(x_k)}{\partial x_2 \partial x_1}, \frac{\partial^2f(x_k)}{\partial x^2_2},...,\frac{\partial^2f(x_k)}{\partial x_2\partial x_n}\\
..., ...,...,....\\
\frac{\partial^2f(x_k)}{\partial x_n\partial x_1}, \frac{\partial^2f(x_k)}{\partial x_n\partial x_2},...,\frac{\partial^2f(x_k)}{\partial x^2_n}
\end{bmatrix}
$$
