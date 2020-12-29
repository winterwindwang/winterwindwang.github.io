---

layout: post

titile: Opencv findContours()函数

category: 技术，图像处理

tag: React

keyword: React

---

## Python opencv中findContours()函数

假设图像如下图所示：

```
(0,0)--------------->x 轴
|
|
|
|
|
y轴
```

该函数提取的轮廓按以下逻辑实现[参考][]：

1. 外轮廓为同一层级 ，同一外轮廓的内轮廓为同一层级
2. 双层轮廓顺序基于外轮廓最上像素点Y坐标从大到小排序（图片的从下到上），若存在Y坐标相同情况下根据X坐标从大到小排序（图片的从右到左）
3. 若有多个内轮廓，内轮廓排序基于内轮廓最上像素点Y坐标从大到小排序（图片的从下到上），若存在Y坐标相同情况下根据X坐标从大到小排序（图片的从右到左）

若需要对求出的轮廓点进行排序

```
# 求取轮廓点，一般输入图像是threshold，adaptive Threshold或使用canny算子提取边缘后的图
_, contours, hierachy = cv2.findContours(img_gray, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)
# 按轮廓面积大小从小到大排序
contours.sort(key=lambda cnt:cv2.contourArea(cnt), reverse=True)
```

[1]: https://blog.csdn.net/wangwenjie1997/article/details/100161215

