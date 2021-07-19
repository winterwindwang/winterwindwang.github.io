---
layout: post
title: python中颜色空间转换，PyTorch实现颜色空间转换
category: 技术
tags: color space convert, pytorch convert rgb to lab
---

> 导语：很多文章中都提到在LAB颜色空间中计算图像的损失，因为LAB颜色空间与人类的感知较为接近

在网上找了相关资料后，在torch的讨论区中[forum](https://discuss.pytorch.org/t/is-there-rgb2lab-function-for-pytorch-data-varaible-or-tensor-or-torch-read-from-an-image/15594)中找到了类似的回答。以下将分成两个过程来介绍LAB颜色空间的转换

### 1 加载图像的时候就将RGB转换成LAB格式

```python
from skimage import io, color
class MyData(Dataset):
    ...
    def __getitem__(self, index):
        rgb = io.imread(self.images[index])
        lab = color.rgb2lab(rgb)
        x = torch.from_numpy(lab)
        ...
```



### 2 在训练中将RGB图像转换成LAB格式

在训练中会涉及到梯度的计算和回传，没办法使用方法一，因此参考了[cookbook](https://web.archive.org/web/20120502065620/http://cookbooks.adobe.com/post_Useful_color_equations__RGB_to_LAB_converter-14227.html)的实现，转换成纯PyTorch代码，从而可以在训练中使用。

```python
def rgb2xyz(img):
    """
    RGB from 0 to 255
    :param img:
    :return:
    """
    r, g, b = torch.split(img, 1, dim=1)

    r = torch.where(r > 0.04045, torch.pow((r+0.055) / 1.055, 2.4), r / 12.92)
    g = torch.where(g > 0.04045, torch.pow((g+0.055) / 1.055, 2.4), g / 12.92)
    b = torch.where(b > 0.04045, torch.pow((b+0.055) / 1.055, 2.4), b / 12.92)

    r = r * 100
    g = g * 100
    b = b * 100

    x = r * 0.412453 + g * 0.357580 + b * 0.180423
    y = r * 0.212671 + g * 0.715160 + b * 0.072169
    z = r * 0.019334 + g * 0.119193 + b * 0.950227
    return torch.cat([x,y,z], dim=1)


def xyz2lab(xyz):
    x, y, z = torch.split(xyz, 1, dim=1)
    ref_x, ref_y, ref_z = 95.047, 100.000, 108.883
    # ref_x, ref_y, ref_z = 0.95047, 1., 1.08883
    x = x / ref_x
    y = y / ref_y
    z = z / ref_z

    x = torch.where(x > 0.008856, torch.pow( x , 1/3 ), (7.787 * x) + (16 / 116.))
    y = torch.where(y > 0.008856, torch.pow( y , 1/3 ), (7.787 * y) + (16 / 116.))
    z = torch.where(z > 0.008856, torch.pow( z , 1/3 ), (7.787 * z) + (16 / 116.))

    l = (116. * y) - 16.
    a = 500. * (x - y)
    b = 200. * (y - z)
    return torch.cat([l,a,b], dim=1)

def lab2xyz(lab):
    ref_x, ref_y, ref_z = 95.047, 100.000, 108.883
    l, a, b = torch.split(lab, 1, dim=1)
    y = (l + 16) / 116.
    x = a / 500. + y
    z = y - b / 200.

    y = torch.where(torch.pow( y , 3 ) > 0.008856, torch.pow( y , 3 ), ( y - 16 / 116. ) / 7.787)
    x = torch.where(torch.pow( x , 3 ) > 0.008856, torch.pow( x , 3 ), ( x - 16 / 116. ) / 7.787)
    z = torch.where(torch.pow( z , 3 ) > 0.008856, torch.pow( z , 3 ), ( z - 16 / 116. ) / 7.787)

    x = ref_x * x
    y = ref_y * y
    z = ref_z * z
    return torch.cat([x,y,z],dim=1)


def xyz2rgb(xyz):
    x, y, z = torch.split(xyz, 1, dim=1)

    x = x / 100.
    y = y / 100.
    z = z / 100.

    r = x * 3.2406 + y * -1.5372 + z * -0.4986
    g = x * -0.9689 + y * 1.8758 + z * 0.0415
    b = x * 0.0557 + y * -0.2040 + z * 1.0570

    r = torch.where(r > 0.0031308, 1.055 * torch.pow( r , ( 1 / 2.4 ) ) - 0.055,  12.92 * r)
    g = torch.where(g > 0.0031308, 1.055 * torch.pow( g , ( 1 / 2.4 ) ) - 0.055,  12.92 * g)
    b = torch.where(b > 0.0031308, 1.055 * torch.pow( b , ( 1 / 2.4 ) ) - 0.055,  12.92 * b)

    r = torch.round(r * 255.)
    g = torch.round(g * 255.)
    b = torch.round(b * 255.)

    return torch.cat([r,g,b], dim=1)
im = Image.open(filepath)
im = transforms.ToTensor()(im)
im = torch.unsqueeze(im, dim=0)
lab = xyz2lab(rgb2xyz(im))

```

#### 待续

图片测试、其他颜色空间转换

