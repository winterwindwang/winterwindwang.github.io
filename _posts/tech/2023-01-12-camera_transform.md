---
layout: post
title: Camera矩阵与变换
category: 技术
keywords: camera matrix, transformation
tags: technical details
---

>  前言：了解世界坐标系、相机坐标系、图像物理坐标系、图像像素坐标系。并搞清楚坐标系之间的转换。

### 在介绍坐标系之前，介绍一下相机与图片之间的参数

+ 相机
  + FOV: 视野
  + 坐标：Position_X(0.3), Position_Y(0), Position_Z (1.30)
  + 俯仰角：RotationPitch=0, RotationRoll=0, RotationYaw=0
  + 图片尺寸：IMAGE-X (800:宽), Image-Y (600：高)
+ 物体坐标系
  + X,Y,Z
  + 物体尺寸（物体中心，或者说物体中心的坐标）

### 矩阵乘法

**左乘改变矩阵的行，右乘改变矩阵的列**（单位矩阵的话就是**左乘交换行**，**右乘交换列**）,参考[这里](www)

此外
$$
\left[
\begin{array}{1}
0& -1& 0& 0\\
1& 0& 0& 0\\
0& 0& 1& 0\\
0& 0& 0& 1\\
\end{array}
\right]
$$
该矩阵表示矩阵绕Z轴旋转90度。

例如有矩阵A，B

A为旋转矩阵（绕Z轴旋转$\theta$度，特征是A[3,3]为1）
$$
A = \left[
\begin{array}{1}
\cos\theta& -\sin\theta& 0& 0\\
\sin\theta& \cos\theta& 0& 0\\
0& 0& 1& 0\\
0& 0& 0& 1\\
\end{array}
\right]
$$
B为平移矩阵（4*4）特征为第B[3,:4]上有数字，分别对应X,Y,Z轴的平移量，


$$
B = \left[
\begin{array}{1}
1& 0& 0& 3\\
0& 1& 0& 4\\
0& 0& 1& 2\\
0& 0& 0& 1\\
\end{array}
\right]
$$


一般而言，先对矩阵X做平移变换，然后做旋转变换，即**ABX**。



# 世界坐标系



# 相机坐标系



# 图像物理坐标系



# 图像像素坐标系





# Carla中相机相关

## Camera: Depth map

+ 最大渲染距离为1km
+ 解码深度

```python
1. 解码深度首先得到int24数据，通过公式R+G*256+B*256*256
2. 归一化到[0,1]，ANs / (256*256*256 - 1)
3. 最后乘以我们想要得到的单位。代码中将最远的平面设置成1000米 Ans*far
```

## Transform

1. 首先，在Carla中获取物体实时的世界坐标，如下图所示

```
# 创建了一辆车my_vehicle
t = my_vehicle.get_transform()
global_location_x = t.location.x
global_location_y = t.location.y
global_location_z = t.location.z
global_rotation_yaw = t.rotation.yaw
global_rotation_pitch = t.rotation.pitch
global_rotation_roll = t.rotation.roll
```

获取世界坐标后，首先需要转到相机坐标系下。

```
camera.get_transform().get_matrix()
```

+ transform 计算以当前点为原点的坐标系A与世界坐标系B之间的变换。
+ get_matrix()获取当前点为原点的坐标系A到世界坐标系B之间的变换矩阵。

但是get_matrix()默认相机位置为原点的这个坐标系的x,y,z轴，与世界坐标系的x,y,z轴是相同方向的，没有考虑轴旋转。但是实际上，相机坐标系是相机的正前方，光心方向总是是z轴方向。但是相机坐标系和世界坐标系存在差异，具体查看[这里](https://blog.csdn.net/qq_35588369/article/details/120629069)。具体为这两个坐标系存在一个旋转矩阵。

因此`camera.get_transform().get_matrix()`获得的是不考虑轴旋转的局部到全局的变换矩阵。`camera.get_transform().get_matrix()`获得的矩阵取逆得到的是不考虑轴旋转的全局到局部的变换矩阵。而世界坐标系到相机局部坐标系考虑旋转轴关系的对应矩阵为
$$
R=\left[
\begin{array}{1}
0 &1& 0 \\
0 &0 & 1 \\
1 &0& 0 \\
\end{array}
\right] （光轴如果朝向相机正后方，那么将第一、三行的1改为-1）
$$
那么，对于世界坐标系下点$P_w=(x_w,y_w,z_w)^T$，其在相机坐标系下的点$p_c=RP_w$。

2. 相机坐标系到像素坐标系

参考[这里]()

![img](https://img-blog.csdnimg.cn/c2660ca2998d432aa7948572a4dfb209.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcXFfMzU1ODgzNjk=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

参考[这里]()

![在这里插入图片描述](https://img-blog.csdnimg.cn/c9d91b3634d3486fa453c9a8885d63ab.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcXFfMzU1ODgzNjk=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

Carla中没有获取内参的函数，但是可以根据上述信息可以估计得到。

从在参考文档中 `Sensors refernece/RGB camera/Advancedcameraattributes` 中，可以得到相机的焦距。`focal_distance=1000.0`
光心一般是相机采集图片的中心。根据这些信息可以估计出相机内参。假设相机坐标下的点的坐标$P_c=(x_c,y_c,z_c)$，相机内参是K，$P_p=KP_c$

参考：

1. https://blog.csdn.net/qq_35588369/article/details/120629069

2. https://blog.csdn.net/weixin_43206570/article/details/84797361?spm=1001.2014.3001.5502
3. https://blog.csdn.net/a083614/article/details/78579163
4. https://keras.io/examples/vision/nerf/

+ Carla加载指定town

```python
world = World(client.get_world(), client.load_world("Town07"), hud, args)
```

+ Carla坐标系变换

1. 相机内参数矩阵

```python
k = np.identity(3)
k[0, 2] = WINDOW_WIDTH_HALF
k[1, 2] = WINDOW_HEIGHT_HALF
f = WINDOW_WIDTH / (2.0 * math.tan(90.0 * math.pi / 360.0))
k[0, 0] = k[1, 1] = f
```

2. 利用相机外参矩阵求出在相机坐标系下的三维坐标

```python
def proj_to_camera(pos_vector, extrinsic_mat):
    # transform the points to camera
    transformed_3d_pos = np.dot(inv(extrinsic_mat), pos_vector)
    return transformed_3d_pos
```

3. 利用相机内参矩阵求将相机的三位坐标转换成图片内的二维坐标(内参计算参考上面)

```python
def proj_to_2d(camera_pos_vector, intrinsic_mat):
    # transform the points to 2D
    cords_x_y_z = camera_pos_vector[:3, :]
    cords_y_minus_z_x = np.concatenate([cords_x_y_z[1, :], -cords_x_y_z[2, :], cords_x_y_z[0, :]])
    pos2d = np.dot(intrinsic_mat, cords_y_minus_z_x)
    # normalize the 2D points
    pos2d = np.array([
        pos2d[0] / pos2d[2],
        pos2d[1] / pos2d[2],
        pos2d[2]
    ])
    return pos2d
```

参考资料：https://blog.csdn.net/weixin_41823188/article/details/113624049?spm=1001.2014.3001.5501

Carla中采集数据所用的`transform.location/rotation`均来自`TelemetryPoint`.

##  Geometric transformations

[Source](https://carla.readthedocs.io/en/latest/tuto_G_bounding_boxes/#geometric-transformations)We want to take 3D points from the simulation and project them into the 2D plane of the camera. Firstly, we need to construct the camera projection matrix:

```py
def build_projection_matrix(w, h, fov):
    focal = w / (2.0 * np.tan(fov * np.pi / 360.0))
    K = np.identity(3)
    K[0, 0] = K[1, 1] = focal
    K[0, 2] = w / 2.0
    K[1, 2] = h / 2.0
    return K
```

We want to use the camera projection matrix to project 3D to 2D points. The first step is to transform the 3D coordinates in world coordinates into camera coordinates, using the inverse camera transform that can be retrieved using `camera.get_transform().get_inverse_matrix()`. Following this, we use the camera projection matrix to project the 3D points in camera coordinates into the 2D camera plane:

```py
def get_image_point(loc, K, w2c):
        # Calculate 2D projection of 3D coordinate

        # Format the input coordinate (loc is a carla.Position object)
        point = np.array([loc.x, loc.y, loc.z, 1])
        # transform to camera coordinates
        point_camera = np.dot(w2c, point)

        # New we must change from UE4's coordinate system to an "standard"
        # (x, y ,z) -> (y, -z, x)
        # and we remove the fourth componebonent also
        point_camera = [point_camera[1], -point_camera[2], point_camera[0]]

        # now project 3D->2D using the camera matrix
        point_img = np.dot(K, point_camera)
        # normalize
        point_img[0] /= point_img[2]
        point_img[1] /= point_img[2]

        return point_img[0:2]
```

Now that we have the functions to project 3D -> 2D we retrieve the camera specifications:

```py
# Get the world to camera matrix
world_2_camera = np.array(camera.get_transform().get_inverse_matrix())

# Get the attributes from the camera
image_w = camera_bp.get_attribute("image_size_x").as_int()
image_h = camera_bp.get_attribute("image_size_y").as_int()
fov = camera_bp.get_attribute("fov").as_float()

# Calculate the camera projection matrix to project from 3D -> 2D
K = build_projection_matrix(image_w, image_h, fov)
```

## Camera geometry

[source](https://carla.readthedocs.io/en/latest/tuto_G_pedestrian_bones/#camera-geometry)

Now we need to perform some geometric calculations. First, we want to transform the world coordinates of the bones into camera coordinates, we do this using the inverse of the camera transform. This means that the coordinates are transformed to be relative to a camera located at the origin facing in the positive x-direction.

```py
# get 4x4 matrix to transform points from world to camera coordinates
world_2_camera = np.array(camera.get_transform().get_inverse_matrix())
```

Then, we need to project the 3D points onto the 2D field of view (FOV) of the camera to overlay them on the output images using the [**camera matrix**](https://carla.readthedocs.io/en/latest/tuto_G_pedestrian_bones/#https://en.wikipedia.org/wiki/Camera_matrix) or **projection matrix**. The following function produces the camera matrix needed for this 3D -> 2D transformation.

```py
def build_projection_matrix(w, h, fov):
    focal = w / (2.0 * np.tan(fov * np.pi / 360.0))
    K = np.identity(3)
    K[0, 0] = K[1, 1] = focal
    K[0, 2] = w / 2.0
    K[1, 2] = h / 2.0
    return K
neural_renderer中renderer.py文件中的相关代码
self.eye = [0, 0, -(1. / math.tan(math.radians(self.viewing_angle)) + 1)] # viewing_angle被设置为45
```

## carla.Transform

[source](https://carla.readthedocs.io/en/latest/python_api/#carlatransform)

# NERF中相机坐标与世界坐标

参考[这里](https://keras.io/examples/vision/nerf/)

![image-20230112230422605](C:\Users\wdh\AppData\Roaming\Typora\typora-user-images\image-20230112230422605.png)

COLMAP[数据集](https://colmap.github.io/)

Neural Renderer 用的是欧拉旋转角

参考

https://blender.stackexchange.com/questions/18530/setting-euler-rotates-camera-but-setting-quaternion-doesnt

https://github.com/hiroharu-kato/neural_renderer_v2/blob/master/misc/prepare_blender_data.py

https://juejin.cn/post/7094065235275251749

https://zh.wikipedia.org/wiki/%E6%97%8B%E8%BD%AC%E7%9F%A9%E9%98%B5





Tait–Bryan angles

Euler angles

https://mathworld.wolfram.com/EulerAngles.html



