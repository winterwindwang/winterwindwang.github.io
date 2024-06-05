---
layout: post
title: Pytorch报RuntimeError: result type Float can't be cast to the desired output type long int错误，原因分析和解决方案
category: 技术
keywords: python, torch
tags: technical details
---

## 在使用pytorch自带的U版yolo时遇到数据转换问题，以及解决方法

环境：

```
pytorch==1.12.1
Python==3.9.0
yolo==2.4.1
```



错误：

```
RuntimeError: result type Float can't be cast to the desired output type long int
```

错误location为：

```
yolo/utils/loss.py
---------------------
def build_targets(self, p, targets):
    # Build targets for compute_loss(), input targets(image,class,x,y,w,h)
    na, nt = self.na, targets.shape[0]  # number of anchors, targets
    tcls, tbox, indices, anch = [], [], [], []
    gain = torch.ones(7, device=targets.device)  # normalized to gridspace gain
    ai = torch.arange(na, device=targets.device).float().view(na, 1).repeat(1, nt)  # same as .repeat_interleave(nt)
    targets = torch.cat((targets.repeat(na, 1, 1), ai[:, :, None]), 2)  # append anchor indices

    g = 0.5  # bias
    off = torch.tensor([[0, 0],
                        # [1, 0], [0, 1], [-1, 0], [0, -1],  # j,k,l,m
                        # [1, 1], [1, -1], [-1, 1], [-1, -1],  # jk,jm,lk,lm
                        ], device=targets.device).float() * g  # offsets

    for i in range(self.nl):
        anchors = self.anchors[i]
        gain[2:6] = torch.tensor(p[i].shape)[[3, 2, 3, 2]]  # xyxy gain

        # Match targets to anchors
        t = targets * gain
        if nt:
            # Matches
            r = t[:, :, 4:6] / anchors[:, None]  # wh ratio
            j = torch.max(r, 1. / r).max(2)[0] < self.hyp['anchor_t']  # compare
            # j = wh_iou(anchors, t[:, 4:6]) > model.hyp['iou_t']  # iou(3,n)=wh_iou(anchors(3,2), gwh(n,2))
            t = t[j]  # filter

            # Offsets
            gxy = t[:, 2:4]  # grid xy
            gxi = gain[[2, 3]] - gxy  # inverse
            j, k = ((gxy % 1. < g) & (gxy > 1.)).T
            l, m = ((gxi % 1. < g) & (gxi > 1.)).T
            j = torch.stack((torch.ones_like(j),))
            t = t.repeat((off.shape[0], 1, 1))[j]
            offsets = (torch.zeros_like(gxy)[None] + off[:, None])[j]
        else:
            t = targets[0]
            offsets = 0

        # Define
        b, c = t[:, :2].long().T  # image, class
        gxy = t[:, 2:4]  # grid xy
        gwh = t[:, 4:6]  # grid wh
        gij = (gxy - offsets).long()
        gi, gj = gij.T  # grid xy indices

        # Append
        a = t[:, 6].long()  # anchor indices
        indices.append((b, a, gj.clamp_(0, gain[3] - 1), gi.clamp_(0, gain[2] - 1)))  # image, anchor, grid indices  # ERROR: 错误发生在这里
        tbox.append(torch.cat((gxy - gij, gwh), 1))  # box
        anch.append(anchors[a])  # anchors
        tcls.append(c)  # class

    return tcls, tbox, indices, anch
```

### 解决方案一：降低torch版本

### 解决方案二：

错误信息指的是数据类型转换不匹配，从代码中可以看出，indices列表中b，a，gi, gj都是长整型long()，直觉上不应该出现问题。那么问题会出现在哪里呢？通过检查发现gain是float类型的数组，而gj和gi都是长整型，因此使用clamp_()操作的时候系统认为无法将长整型的数据clamp到浮点型上，因此报了这个错误。

以下是猜测：但是使用更低版本的torch就没有这个问题，可能是torch替我们做了相应的类型转换操作，或者以前并没有把这个当作问题。torch更新以后，就需要显示地指定数据类型，以保证不会出现类型冲突。

以后碰到这种tensor.clamp_()操作时，不妨检查tensor的类型与clamp\_中上下界的类型是否一致。
