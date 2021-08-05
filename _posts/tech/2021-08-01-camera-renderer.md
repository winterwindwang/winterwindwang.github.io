---
layout: post
title: 使用CaralaUE4采集仿真世界车辆图像，并使用neural_renderer渲染位置与采集图像相同的图像
category: 技术
tags: CarlaUE4, neural_renderer, camera
---

> 导语：Carla在自动驾驶领域中是一个很重要的仿真器，它和UE4的结合可以生成非常精细的仿真世界；常被用于自动驾驶等研究任务中。神经渲染器是将传统的渲染过程参数化，使其整个过程变得可以微分。

###  1 CarlaUE4的安装和使用

#### 1.1 下载

首先，进入[Carla](https://carla.org/)的官方页面，然后点击Get Started，然后进入到`Quick start package installation`找到`Package installation`，并进入到`Carla repository`。选择`Carla 0.9.10`，进入到[详细页面](https://github.com/carla-simulator/carla/releases/tag/0.9.10)。看到Release 0.9.10中有两个Window版本zip文件，都下载下来。

#### 1.2 使用

1. 激活要使用Carla的虚拟环境；

2. 下载完成后，还需要安装一个python库

`pip install --user pygame numpy`

3. 安装完成以后，解压下载的Carla0.9.10.ZIP文件，得到以下的目录文件

```
-Carla_0.9.10
	-WindowsNoEditor
		- CarlaUE4
		- Co-Simulation
		- PythonAPI
			- carla
			- examples
				- manual_control.py
			- util
		- CarlaUE4.exe
		- ...
```

4. 运行上面的`CarlaUE4.exe`文件，会得到一个初始场景，是一个街道画面。
5. 然后，将目录切换到`PythonAPI/examples/`下，运行`python manual_control.py`会弹出另外一个界面，在该界面中车辆是Actor，我们可以通过相关的命令控制车辆。

#### 1.3 仿真数据集的收集

+ 收集的仿真数据集主要有三种：采集的当前图像、目标车辆的空间位置和旋转信息、渲染相机的相对位置和旋转信息。

+ 修改`manual_control.py`中相机的位置，来实现不同角度的渲染效果。

+ 然后，在运行命令`python manual_control.py`后，按下`R`键来采集信息，不需要保存的时候，再次按下`R`键即可。

+ 每条采集的数据都是一个字典文件：`{'img':images, 'veh_trans':[[x,y,z],[pitch, yaw, roll]], 'cam_trans':[[x,y,z],[pitch, yaw, roll]]}`；**注意：相机的位置和旋转信息是相对于目标车辆的位置和旋转信息，是个相对位置**。文件格式最好保存成`.npy`的格式。

  ```python
  原文件line 996
          # 相机的位置
          self._camera_transforms = [
              # (carla.Transform(carla.Location(x=-5.5, z=2.5), carla.Rotation(pitch=8.0)), Attachment.SpringArm),
              (carla.Transform(carla.Location(x=6, y=-2, z=3), carla.Rotation(pitch=215, yaw=-30, roll=180)),
               Attachment.Rigid),
              # 正左前方
              (carla.Transform(carla.Location(x=6, y=2, z=3), carla.Rotation(pitch=215, yaw=20, roll=180)), Attachment.Rigid),
              # 正右前方
              (carla.Transform(carla.Location(x=6, z=3), carla.Rotation(pitch=215, yaw=0, roll=180)), Attachment.Rigid),
              # 正前方
              (carla.Transform(carla.Location(x=0, y=4, z=1.3), carla.Rotation(pitch=0, yaw=-90, roll=0)),
               Attachment.Rigid),  # 右侧
              (carla.Transform(carla.Location(x=0, y=-4, z=1.3), carla.Rotation(pitch=0, yaw=90, roll=0)),
               Attachment.Rigid),  # 左侧
              (carla.Transform(carla.Location(x=-6, y=-2, z=3), carla.Rotation(pitch=-30, yaw=20, roll=0)),
               Attachment.Rigid),  # 左后方
              (carla.Transform(carla.Location(x=-6, y=2,  z=3), carla.Rotation(pitch=-30, yaw=-30, roll=0)), Attachment.Rigid),   # 右后方
              (carla.Transform(carla.Location(x=-6, z=3), carla.Rotation(pitch=0)), Attachment.Rigid),      # 正后方
  
          ]
  ```

  

### 2 神经渲染器neural_renderer

通过第一步采集得到的数据，使用相机的位置和旋转信息来计算神经渲染器的相机位置（eye变量），使用以下代码进行计算

````py
def get_params(carlaTcam, carlaTveh):  # carlaTcam: tuple of 2*3
    # scale = 0.41 整个scale是代表渲染的尺寸与原始尺寸的比值吗？
    scale = 0.35
    # calc eye
    eye = [0, 0, 0]
    for i in range(0, 3):
        # eye[i] = (carlaTcam[0][i] - carlaTveh[0][i]) * scale 使用这个计算方式会导致相机渲染不出任何东西
        eye[i] = carlaTcam[0][i] * scale # 因为相机使用的是相对位置。在神经渲染器中，由于目标的位置是空间原点，所以应该直接使用相机的位置来渲染（上面提到相机的位置信息是相对于目标车辆的，所以无论车辆怎么变，相机与车辆的位置都是固定的）

    # calc camera_direction and camera_up
    pitch = math.radians(carlaTcam[1][0])
    yaw = math.radians(carlaTcam[1][1])
    roll = math.radians(carlaTcam[1][2])
    # 需不需要确定下范围？？？
    cam_direct = [math.cos(pitch) * math.cos(yaw), math.cos(pitch) * math.sin(yaw), math.sin(pitch)]
    cam_up = [math.cos(math.pi/2+pitch) * math.cos(yaw), math.cos(math.pi/2+pitch) * math.sin(yaw), math.sin(math.pi/2+pitch)]
    
    # 如果物体也有旋转，则需要调整相机位置和角度，和物体旋转方式一致
    # 先实现最简单的绕Z轴旋转
    p_cam = eye
    p_dir = [eye[0] + cam_direct[0], eye[1] + cam_direct[1], eye[2] + cam_direct[2]]
    p_up = [eye[0] + cam_up[0], eye[1] + cam_up[1], eye[2] + cam_up[2]]
    p_l = [p_cam, p_dir, p_up]
    trans_p = []
    for p in p_l:
        if math.sqrt(p[0]**2 + p[1]**2) == 0:
            cosfi = 0
            sinfi = 0
        else:
            cosfi = p[0] / math.sqrt(p[0]**2 + p[1]**2)
            sinfi = p[1] / math.sqrt(p[0]**2 + p[1]**2)        
        cossum = cosfi * math.cos(math.radians(carlaTveh[1][1])) + sinfi * math.sin(math.radians(carlaTveh[1][1]))
        sinsum = math.cos(math.radians(carlaTveh[1][1])) * sinfi - math.sin(math.radians(carlaTveh[1][1])) * cosfi
        trans_p.append([math.sqrt(p[0]**2 + p[1]**2) * cossum, math.sqrt(p[0]**2 + p[1]**2) * sinsum, p[2]])
    
    
    return trans_p[0], \
        [trans_p[1][0] - trans_p[0][0], trans_p[1][1] - trans_p[0][1], trans_p[1][2] - trans_p[0][2]], \
        [trans_p[2][0] - trans_p[0][0], trans_p[2][1] - trans_p[0][1], trans_p[2][2] - trans_p[0][2]]


eye, camera_direction, camera_up = get_params(((-25, 16, 20), (-45, 180, 0)), ((-45, 3, 0.8), (0, 0, 0)))  # test example
self.renderer.renderer.eye = eye
# 以下两个参数在实际渲染过程中不起作用
self.renderer.renderer.camera_direction = camera_direction
self.renderer.renderer.camera_up = camera_up 

# 渲染
```渲染参数
vertices_-var: 顶点参数 （[1，13449， 3]）
faces_var: 渲染的表面 ([1, 23145, 3])
texture: 纹理信息  ([1, 23145, 6,6,6,3])
```
imgs_pred = self.mask_renderer.forward(self.vertices_var, self.faces_var, self.textures)

# 将渲染图与原图相加
img = img[:, :, ::-1]
img = cv2.resize(img, (self.img_size, self.img_size))
img = np.transpose(img, (2, 0, 1))
img = np.resize(img, (1, img.shape[0], img.shape[1], img.shape[2]))
img = torch.from_numpy(img).cuda(device=0)
imgs_pred = imgs_pred / torch.max(imgs_pred)
total2show = img
total_img = img + 255 * imgs_pred
````

### 3 相机知识

1. OPEGL学习笔记：[https://www.zhihu.com/column/c_1213149364098600960](https://www.zhihu.com/column/c_1213149364098600960)
2. Carmera旋转：[https://learnopengl-cn.github.io/01%20Getting%20started/09%20Camera/](https://learnopengl-cn.github.io/01%20Getting%20started/09%20Camera/)
3. 相机旋转：[https://www.zhihu.com/search?type=content&q=opengl%20%E7%9B%B8%E6%9C%BA%20%E6%97%8B%E8%BD%AC](https://www.zhihu.com/search?type=content&q=opengl%20%E7%9B%B8%E6%9C%BA%20%E6%97%8B%E8%BD%AC)

