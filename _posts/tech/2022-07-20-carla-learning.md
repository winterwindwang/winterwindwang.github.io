---
layout: post
title: Carla数据采集(一)
category: 技术
keywords: 渲染器、仿真平台
tags: technical details
---

# Carla配置

环境配置：

```
1. python 3.7
2. CARLA_0.9.13 Release版本
```

## 物体坐标

#### Carla.Transform
在mannual_control.py的1056行有以下代码
```python
(carla.Transform(carla.Location(x=-2.5, z=0.0), carla.Rotation(pitch=-8.0)), Attachment.SpringArm),
是一个(carla.Transform, Attachment.SpringArm)的形式
```

其中`Attachment.SpringArm`的定义是
```markdown
Rigid: With this fixed attatchment the object follow its parent position strictly. 
This is the recommended attachment to retrieve precise data from the simulation.

SpringArm: An attachment that expands or retracts the position of the actor, 
depending on its parent. This attachment is only recommended to record videos from
the simulation where a smooth movement is needed. SpringArms are an Unreal Engine
component so check the UE docs to learn more about them.
```

然后`carla.Transform`的定义是
```markdown
一个组合了location和rotation的一个类，没有scaling。
location(carla.Location) 描述了一个点在坐标系统下的坐标
---表示世界坐标系下的一个点,(x,y,z)分别表示从XYZ轴上从原点到点的距离
---方法distance(self, location)， 返回该点坐标到其他点坐标的欧拉距离

rotation(carla.Rotation--degrees(pitch, yaw, roll))描述一个物体在UE坐标系统下的旋转，用角度表示

Methods
__init__(self, location, rotation)
transform(self, in_point) 使用当前变换作为参考系，将3D点从局部平移到全局坐标

```

相机管理类`CameraManager`下还有一个`self._parent.bounding_box`，表示场景中的一个actor或一个element的几何形状，其中包含以下属性
```
extent(carla.Vector3D): 表示从box中心到一个顶点的向量。每个坐标系上的值等于该坐标系上box的尺寸的一半。extent.x * 2将返回在X坐标系下box的的尺寸。

location(carla.Location): bounding box的中心
rotation(carla.Rotation): bounding biox的方向
```