---
layout: post
title: Carla数据采集
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

##
相机的几个参数：

生成相机时，可以指定相机的分辨率，即采样得到数据的分辨率。

```python
width， height = 400， 400
cam_rgb_bp = blueprint_library.find('sensor.camera.rgb') # 找到相机传感器
cam_rgb_bp.set_attribute("image_size_x", "{}".format(width)) # 设置相机生成图片的分辨率（宽度）
cam_rgb_bp.set_attribute("image_size_y", "{}".format(height)) # 设置相机生成图片的分辨率（高度）
```

Carla中相机的坐标一般是相对坐标，即相对于目标汽车的坐标，用于采集汽车形势过程中的数据。同时也有固定在场景中的相机，类似于监视器一样，不随汽车移动，通过获取汽车与相机的世界坐标，可以根据世界坐标计算出渲染相机的参数，用相机坐标减去汽车坐标，就得到了相机相对于汽车的坐标；在neural_renderer的nmt_test文件中，用`[cam_trans[0][i] - veh_trans[0][i] for i in range(3)]`实现。

**location**
x: 正值为汽车前面，负值表示汽车后面
y: 正值表示汽车的右面，负值表示汽车的左面(车头朝向前方)
z: 值越大，相机的位置越高

**rotation**

pitch: 控制相机的俯仰角；0为平视角度，-90为俯视角度，90为仰视角度。
yaw: 控制相机的绕圈水平旋转。[-180,-360]车头往左旋转；[180，360]车头往右旋转。
roll: 地面上对进行采样时可以固定不变，如果是无人机或者飞机就需要修改。

以下提供了4种不同距离，4种不同相机高度，8种不同相机方位角的坐标设置

**4种不同距离**
```
# 车后5米距离
camera_rgb_01 = world.spawn_actor(
    cam_rgb_bp,
    carla.Transform(carla.Location(x=-5, y=0,  z=1.5),
                    carla.Rotation(pitch=-22.5, yaw=0, roll=0)),
    attach_to=vehicle, attachment_type=Attachment.Rigid)

# 车后7米距离
camera_rgb_02 = world.spawn_actor(
    cam_rgb_bp,
    carla.Transform(carla.Location(x=-7, y=0,  z=1.5),
                    carla.Rotation(pitch=-22.5, yaw=0, roll=0)),
    attach_to=vehicle, attachment_type=Attachment.Rigid)

# 车后9米距离
camera_rgb_03 = world.spawn_actor(
    cam_rgb_bp,
    carla.Transform(carla.Location(x=-9, y=0,  z=1.5),
                    carla.Rotation(pitch=-22.5, yaw=0, roll=0)),
    attach_to=vehicle, attachment_type=Attachment.Rigid)

# 车后11米距离
camera_rgb_04 = world.spawn_actor(
    cam_rgb_bp,
    carla.Transform(carla.Location(x=-11, y=0,  z=1.5),
                    carla.Rotation(pitch=-22.5, yaw=0, roll=0)),
    attach_to=vehicle, attachment_type=Attachment.Rigid)
```
效果图：

![image-20220812223110168](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220812223110168.png)


**4种不同相机高度**

注意，当相机高度变高以后，相机的视野中可能看不到汽车，所以还要调整pitch来控制相机的俯仰角，来保证汽车始终在相机的视野内。

```
# 车后1米距离
camera_rgb_01 = world.spawn_actor(
    cam_rgb_bp,
    carla.Transform(carla.Location(x=-1, y=0,  z=1.0),
                    carla.Rotation(pitch=-22.5, yaw=0, roll=0)),
    attach_to=vehicle, attachment_type=Attachment.Rigid)

# 车后1.5米距离
camera_rgb_02 = world.spawn_actor(
    cam_rgb_bp,
    carla.Transform(carla.Location(x=-1.5, y=0,  z=1.5),
                    carla.Rotation(pitch=-22.5, yaw=0, roll=0)),
    attach_to=vehicle, attachment_type=Attachment.Rigid)

# 车后2米距离
camera_rgb_03 = world.spawn_actor(
    cam_rgb_bp,
    carla.Transform(carla.Location(x=-2, y=0,  z=2.0),
                    carla.Rotation(pitch=-22.5, yaw=0, roll=0)),
    attach_to=vehicle, attachment_type=Attachment.Rigid)

# 车后2.5米距离
camera_rgb_04 = world.spawn_actor(
    cam_rgb_bp,
    carla.Transform(carla.Location(x=-2.5, y=0,  z=2.5),
                    carla.Rotation(pitch=-22.5, yaw=0, roll=0)),
    attach_to=vehicle, attachment_type=Attachment.Rigid)
```

效果图如下

![image-20220812223323877](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220812223323877.png)

**八种不同方位角**

以下所描述的方位角均以车头朝向前方为规则：相机在车的后方表示正北，车的右后方表示西北，左后方表示东北；相机在车的左前方表示西南，右前方表示东南。两侧比表示正东、正西。

```
# 正北
camera_rgb_01 = world.spawn_actor(
    cam_rgb_bp,
    carla.Transform(carla.Location(x=-5, y=0,  z=1.5),
                    carla.Rotation(pitch=-22.5, yaw=0, roll=0)),
    attach_to=vehicle, attachment_type=Attachment.Rigid)

# 东北
camera_rgb_02 = world.spawn_actor(
    cam_rgb_bp,
    carla.Transform(carla.Location(x=-5, y=-3,  z=1.5),
                    carla.Rotation(pitch=-22.5, yaw=45, roll=0)),
    attach_to=vehicle, attachment_type=Attachment.Rigid)

# 正东
camera_rgb_03 = world.spawn_actor(
    cam_rgb_bp,
    carla.Transform(carla.Location(x=0, y=3,  z=1.5),
                    carla.Rotation(pitch=-22.5, yaw=-90, roll=0)),
    attach_to=vehicle, attachment_type=Attachment.Rigid)

# 东南
camera_rgb_04 = world.spawn_actor(
    cam_rgb_bp,
    carla.Transform(carla.Location(x=-5, y=-3,  z=1.5),
                    carla.Rotation(pitch=-22.5, yaw=135, roll=0)),
    attach_to=vehicle, attachment_type=Attachment.Rigid)

# 正南
camera_rgb_01 = world.spawn_actor(
    cam_rgb_bp,
    carla.Transform(carla.Location(x=-5, y=0,  z=1.5),
                    carla.Rotation(pitch=-22.5, yaw=-180, roll=0)),
    attach_to=vehicle, attachment_type=Attachment.Rigid)

# 西南
camera_rgb_02 = world.spawn_actor(
    cam_rgb_bp,
    carla.Transform(carla.Location(x=-5, y=3,  z=1.5),
                    carla.Rotation(pitch=-22.5, yaw=-135, roll=0)),
    attach_to=vehicle, attachment_type=Attachment.Rigid)

# 正西
camera_rgb_03 = world.spawn_actor(
    cam_rgb_bp,
    carla.Transform(carla.Location(x=0, y=-3,  z=1.5),
                    carla.Rotation(pitch=-22.5, yaw=90, roll=0)),
    attach_to=vehicle, attachment_type=Attachment.Rigid)

# 西北
camera_rgb_04 = world.spawn_actor(
    cam_rgb_bp,
    carla.Transform(carla.Location(x=-5, y=3,  z=1.5),
                    carla.Rotation(pitch=-22.5, yaw=-45, roll=0)),
    attach_to=vehicle, attachment_type=Attachment.Rigid)
```

效果图如下：

![image-20220812223407687](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220812223407687.png)
