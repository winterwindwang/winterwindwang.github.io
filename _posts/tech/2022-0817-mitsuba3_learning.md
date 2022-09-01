---
layout: post
title: 可微分渲染器Mitsuba3学习
category: 技术
keywords: 渲染器、仿真平台
tags: technical detaian
---

# 1、安装

环境要求

```markdowm
Python >= 3.8
(optional) For computation on the GPU: NVidia driver >= 495.89
(optional) For vectorized / parallel computation on the CPU: LLVM >= 11.1
```

安装

```
pip install mitsuba
```



2、导入

```
>> import mitsuba as mi

>> print(mi.variants())
['scalar_rgb', 'scalar_spectral', 'cuda_ad_rgb', 'llvm_ad_rgb']

>> mi.set_variant("")
```

几种变量的作用

![image-20220817212039872](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220817212039872.png)





# 3、加载场景

```markdown
# 加载场景
>> scene = mi.load_file('src/scenes/cornell-box/scene_v3.xml', res=128, integrator='prb')
# 渲染图片
>> img_ref = mi.render(scene, seed=0, spp=512)
# 转化成bitmap图片
>> mi.multi.convert_to_bitmap(image_ref)
# 遍历场景中变量
>> scene_params  = mi.traverse(scene)
>> print(scene_params)
SceneParameters[
  ------------------------------------------------------------------------------------------------------------
  Name                                                     Flags    Type            Parent
  ------------------------------------------------------------------------------------------------------------
  PerspectiveCamera.near_clip                                       float           PerspectiveCamera
  PerspectiveCamera.far_clip                                        float           PerspectiveCamera
  PerspectiveCamera.shutter_open                                    float           PerspectiveCamera
  PerspectiveCamera.shutter_open_time                               float           PerspectiveCamera
  PerspectiveCamera.x_fov                                           Float           PerspectiveCamera
  PerspectiveCamera.to_world                                        Transform4f     PerspectiveCamera
  CarPaintBSDF.brdf_0.brdf_0.specular_reflectance.value    ∂        Color3f         SRGBReflectanceSpectrum
  CarPaintBSDF.brdf_0.brdf_0.alpha.value                   ∂, D     Float           UniformSpectrum
  CarPaintBSDF.brdf_0.brdf_0.eta.value                     ∂, D     Color3f         SRGBReflectanceSpectrum
  CarPaintBSDF.brdf_0.brdf_0.k.value                       ∂, D     Color3f         SRGBReflectanceSpectrum
  WindowGlassBSDF.eta                                      ∂, D     Float           ThinDielectric
  ChromeBSDF.brdf_0.specular_reflectance.value             ∂        Color3f         SRGBReflectanceSpectrum
  ChromeBSDF.brdf_0.alpha.value                            ∂, D     Float           UniformSpectrum
  ChromeBSDF.brdf_0.eta.value                              ∂, D     Color3f         SRGBReflectanceSpectrum
  ChromeBSDF.brdf_0.k.value                                ∂, D     Color3f         SRGBReflectanceSpectrum
  BlackRubberBSDF.brdf_0.diffuse_reflectance.value         ∂        Color3f         SRGBReflectanceSpectrum
  BlackRubberBSDF.brdf_0.alpha                             ∂, D     Float           RoughPlastic
  BlackRubberBSDF.brdf_0.eta                               ∂, D     Float           RoughPlastic
  SteelBSDF.brdf_0.eta.value                               ∂, D     Float           UniformSpectrum
  SteelBSDF.brdf_0.k.value                                 ∂, D     Float           UniformSpectrum
  SteelBSDF.brdf_0.specular_reflectance.value              ∂        Float           UniformSpectrum
  WhiteRubberBSDF.brdf_0.reflectance.value                 ∂        Color3f         SRGBReflectanceSpectrum
  GroundBSDF.brdf_0.reflectance.value                      ∂        Color3f         SRGBReflectanceSpectrum
  BlackBSDF.brdf_0.reflectance.value                       ∂        Color3f         SRGBReflectanceSpectrum
  LeatherBSDF.brdf_0.diffuse_reflectance.value             ∂        Color3f         SRGBReflectanceSpectrum
  LeatherBSDF.brdf_0.alpha                                 ∂, D     Float           RoughPlastic
  LeatherBSDF.brdf_0.eta                                   ∂, D     Float           RoughPlastic
  Leather2BSDF.brdf_0.diffuse_reflectance.value            ∂        Color3f         SRGBReflectanceSpectrum
  Leather2BSDF.brdf_0.alpha                                ∂, D     Float           RoughPlastic
  Leather2BSDF.brdf_0.eta                                  ∂, D     Float           RoughPlastic
  InnerBodyBSDF.brdf_0.reflectance.value                   ∂        Color3f         SRGBReflectanceSpectrum
  DashBSDF.brdf_0.diffuse_reflectance.value                ∂        Color3f         SRGBReflectanceSpectrum
  DashBSDF.brdf_0.alpha                                    ∂, D     Float           RoughPlastic
  DashBSDF.brdf_0.eta                                      ∂, D     Float           RoughPlastic
  CabinBSDF.brdf_0.reflectance.value                       ∂        Color3f         SRGBReflectanceSpectrum
  BlackRubber_0001.vertex_count                                     int             OBJMesh
  BlackRubber_0001.face_count                                       int             OBJMesh
  BlackRubber_0001.faces                                            UInt            OBJMesh
  BlackRubber_0001.vertex_positions                        ∂, D     Float           OBJMesh
  BlackRubber_0001.vertex_normals                          ∂, D     Float           OBJMesh
  BlackRubber_0001.vertex_texcoords                        ∂        Float           OBJMesh
  WhiteRubber_0001.vertex_count                                     int             OBJMesh
  WhiteRubber_0001.face_count                                       int             OBJMesh
  WhiteRubber_0001.faces                                            UInt            OBJMesh
  WhiteRubber_0001.vertex_positions                        ∂, D     Float           OBJMesh
  WhiteRubber_0001.vertex_normals                          ∂, D     Float           OBJMesh
  WhiteRubber_0001.vertex_texcoords                        ∂        Float           OBJMesh
  Chrome_0001.vertex_count                                          int             OBJMesh
  Chrome_0001.face_count                                            int             OBJMesh
  Chrome_0001.faces                                                 UInt            OBJMesh
  Chrome_0001.vertex_positions                             ∂, D     Float           OBJMesh
  Chrome_0001.vertex_normals                               ∂, D     Float           OBJMesh
  Chrome_0001.vertex_texcoords                             ∂        Float           OBJMesh
  Chrome_0002.vertex_count                                          int             OBJMesh
  Chrome_0002.face_count                                            int             OBJMesh
  Chrome_0002.faces                                                 UInt            OBJMesh
  Chrome_0002.vertex_positions                             ∂, D     Float           OBJMesh
  Chrome_0002.vertex_normals                               ∂, D     Float           OBJMesh
  Chrome_0002.vertex_texcoords                             ∂        Float           OBJMesh
  Ground.vertex_count                                               int             OBJMesh
  Ground.face_count                                                 int             OBJMesh
  Ground.faces                                                      UInt            OBJMesh
  Ground.vertex_positions                                  ∂, D     Float           OBJMesh
  Ground.vertex_normals                                    ∂, D     Float           OBJMesh
  Ground.vertex_texcoords                                  ∂        Float           OBJMesh
  InnerBody_0001.vertex_count                                       int             OBJMesh
  InnerBody_0001.face_count                                         int             OBJMesh
  InnerBody_0001.faces                                              UInt            OBJMesh
  InnerBody_0001.vertex_positions                          ∂, D     Float           OBJMesh
  InnerBody_0001.vertex_normals                            ∂, D     Float           OBJMesh
  InnerBody_0001.vertex_texcoords                          ∂        Float           OBJMesh
  Steel_0001.vertex_count                                           int             OBJMesh
  Steel_0001.face_count                                             int             OBJMesh
  Steel_0001.faces                                                  UInt            OBJMesh
  Steel_0001.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0001.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0001.vertex_texcoords                              ∂        Float           OBJMesh
  Chrome_0003.vertex_count                                          int             OBJMesh
  Chrome_0003.face_count                                            int             OBJMesh
  Chrome_0003.faces                                                 UInt            OBJMesh
  Chrome_0003.vertex_positions                             ∂, D     Float           OBJMesh
  Chrome_0003.vertex_normals                               ∂, D     Float           OBJMesh
  Chrome_0003.vertex_texcoords                             ∂        Float           OBJMesh
  Leather.vertex_count                                              int             OBJMesh
  Leather.face_count                                                int             OBJMesh
  Leather.faces                                                     UInt            OBJMesh
  Leather.vertex_positions                                 ∂, D     Float           OBJMesh
  Leather.vertex_normals                                   ∂, D     Float           OBJMesh
  Leather.vertex_texcoords                                 ∂        Float           OBJMesh
  Steel_0002.vertex_count                                           int             OBJMesh
  Steel_0002.face_count                                             int             OBJMesh
  Steel_0002.faces                                                  UInt            OBJMesh
  Steel_0002.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0002.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0002.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0003.vertex_count                                           int             OBJMesh
  Steel_0003.face_count                                             int             OBJMesh
  Steel_0003.faces                                                  UInt            OBJMesh
  Steel_0003.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0003.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0003.vertex_texcoords                              ∂        Float           OBJMesh
  Leather2.vertex_count                                             int             OBJMesh
  Leather2.face_count                                               int             OBJMesh
  Leather2.faces                                                    UInt            OBJMesh
  Leather2.vertex_positions                                ∂, D     Float           OBJMesh
  Leather2.vertex_normals                                  ∂, D     Float           OBJMesh
  Leather2.vertex_texcoords                                ∂        Float           OBJMesh
  Steel_0004.vertex_count                                           int             OBJMesh
  Steel_0004.face_count                                             int             OBJMesh
  Steel_0004.faces                                                  UInt            OBJMesh
  Steel_0004.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0004.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0004.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0005.vertex_count                                           int             OBJMesh
  Steel_0005.face_count                                             int             OBJMesh
  Steel_0005.faces                                                  UInt            OBJMesh
  Steel_0005.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0005.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0005.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0006.vertex_count                                           int             OBJMesh
  Steel_0006.face_count                                             int             OBJMesh
  Steel_0006.faces                                                  UInt            OBJMesh
  Steel_0006.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0006.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0006.vertex_texcoords                              ∂        Float           OBJMesh
  Chrome_0004.vertex_count                                          int             OBJMesh
  Chrome_0004.face_count                                            int             OBJMesh
  Chrome_0004.faces                                                 UInt            OBJMesh
  Chrome_0004.vertex_positions                             ∂, D     Float           OBJMesh
  Chrome_0004.vertex_normals                               ∂, D     Float           OBJMesh
  Chrome_0004.vertex_texcoords                             ∂        Float           OBJMesh
  CarPaint_0001.vertex_count                                        int             OBJMesh
  CarPaint_0001.face_count                                          int             OBJMesh
  CarPaint_0001.faces                                               UInt            OBJMesh
  CarPaint_0001.vertex_positions                           ∂, D     Float           OBJMesh
  CarPaint_0001.vertex_normals                             ∂, D     Float           OBJMesh
  CarPaint_0001.vertex_texcoords                           ∂        Float           OBJMesh
  InnerBody_0002.vertex_count                                       int             OBJMesh
  InnerBody_0002.face_count                                         int             OBJMesh
  InnerBody_0002.faces                                              UInt            OBJMesh
  InnerBody_0002.vertex_positions                          ∂, D     Float           OBJMesh
  InnerBody_0002.vertex_normals                            ∂, D     Float           OBJMesh
  InnerBody_0002.vertex_texcoords                          ∂        Float           OBJMesh
  WindowGlass_0001.vertex_count                                     int             OBJMesh
  WindowGlass_0001.face_count                                       int             OBJMesh
  WindowGlass_0001.faces                                            UInt            OBJMesh
  WindowGlass_0001.vertex_positions                        ∂, D     Float           OBJMesh
  WindowGlass_0001.vertex_normals                          ∂, D     Float           OBJMesh
  WindowGlass_0001.vertex_texcoords                        ∂        Float           OBJMesh
  BlackRubber_0002.vertex_count                                     int             OBJMesh
  BlackRubber_0002.face_count                                       int             OBJMesh
  BlackRubber_0002.faces                                            UInt            OBJMesh
  BlackRubber_0002.vertex_positions                        ∂, D     Float           OBJMesh
  BlackRubber_0002.vertex_normals                          ∂, D     Float           OBJMesh
  BlackRubber_0002.vertex_texcoords                        ∂        Float           OBJMesh
  WhiteRubber_0002.vertex_count                                     int             OBJMesh
  WhiteRubber_0002.face_count                                       int             OBJMesh
  WhiteRubber_0002.faces                                            UInt            OBJMesh
  WhiteRubber_0002.vertex_positions                        ∂, D     Float           OBJMesh
  WhiteRubber_0002.vertex_normals                          ∂, D     Float           OBJMesh
  WhiteRubber_0002.vertex_texcoords                        ∂        Float           OBJMesh
  Chrome_0005.vertex_count                                          int             OBJMesh
  Chrome_0005.face_count                                            int             OBJMesh
  Chrome_0005.faces                                                 UInt            OBJMesh
  Chrome_0005.vertex_positions                             ∂, D     Float           OBJMesh
  Chrome_0005.vertex_normals                               ∂, D     Float           OBJMesh
  Chrome_0005.vertex_texcoords                             ∂        Float           OBJMesh
  Chrome_0006.vertex_count                                          int             OBJMesh
  Chrome_0006.face_count                                            int             OBJMesh
  Chrome_0006.faces                                                 UInt            OBJMesh
  Chrome_0006.vertex_positions                             ∂, D     Float           OBJMesh
  Chrome_0006.vertex_normals                               ∂, D     Float           OBJMesh
  Chrome_0006.vertex_texcoords                             ∂        Float           OBJMesh
  Black_0001.vertex_count                                           int             OBJMesh
  Black_0001.face_count                                             int             OBJMesh
  Black_0001.faces                                                  UInt            OBJMesh
  Black_0001.vertex_positions                              ∂, D     Float           OBJMesh
  Black_0001.vertex_normals                                ∂, D     Float           OBJMesh
  Black_0001.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0007.vertex_count                                           int             OBJMesh
  Steel_0007.face_count                                             int             OBJMesh
  Steel_0007.faces                                                  UInt            OBJMesh
  Steel_0007.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0007.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0007.vertex_texcoords                              ∂        Float           OBJMesh
  Chrome_0007.vertex_count                                          int             OBJMesh
  Chrome_0007.face_count                                            int             OBJMesh
  Chrome_0007.faces                                                 UInt            OBJMesh
  Chrome_0007.vertex_positions                             ∂, D     Float           OBJMesh
  Chrome_0007.vertex_normals                               ∂, D     Float           OBJMesh
  Chrome_0007.vertex_texcoords                             ∂        Float           OBJMesh
  Chrome_0008.vertex_count                                          int             OBJMesh
  Chrome_0008.face_count                                            int             OBJMesh
  Chrome_0008.faces                                                 UInt            OBJMesh
  Chrome_0008.vertex_positions                             ∂, D     Float           OBJMesh
  Chrome_0008.vertex_normals                               ∂, D     Float           OBJMesh
  Chrome_0008.vertex_texcoords                             ∂        Float           OBJMesh
  CarPaint_0002.vertex_count                                        int             OBJMesh
  CarPaint_0002.face_count                                          int             OBJMesh
  CarPaint_0002.faces                                               UInt            OBJMesh
  CarPaint_0002.vertex_positions                           ∂, D     Float           OBJMesh
  CarPaint_0002.vertex_normals                             ∂, D     Float           OBJMesh
  CarPaint_0002.vertex_texcoords                           ∂        Float           OBJMesh
  Steel_0008.vertex_count                                           int             OBJMesh
  Steel_0008.face_count                                             int             OBJMesh
  Steel_0008.faces                                                  UInt            OBJMesh
  Steel_0008.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0008.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0008.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0009.vertex_count                                           int             OBJMesh
  Steel_0009.face_count                                             int             OBJMesh
  Steel_0009.faces                                                  UInt            OBJMesh
  Steel_0009.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0009.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0009.vertex_texcoords                              ∂        Float           OBJMesh
  CarPaint_0003.vertex_count                                        int             OBJMesh
  CarPaint_0003.face_count                                          int             OBJMesh
  CarPaint_0003.faces                                               UInt            OBJMesh
  CarPaint_0003.vertex_positions                           ∂, D     Float           OBJMesh
  CarPaint_0003.vertex_normals                             ∂, D     Float           OBJMesh
  CarPaint_0003.vertex_texcoords                           ∂        Float           OBJMesh
  Chrome_0009.vertex_count                                          int             OBJMesh
  Chrome_0009.face_count                                            int             OBJMesh
  Chrome_0009.faces                                                 UInt            OBJMesh
  Chrome_0009.vertex_positions                             ∂, D     Float           OBJMesh
  Chrome_0009.vertex_normals                               ∂, D     Float           OBJMesh
  Chrome_0009.vertex_texcoords                             ∂        Float           OBJMesh
  Steel_0010.vertex_count                                           int             OBJMesh
  Steel_0010.face_count                                             int             OBJMesh
  Steel_0010.faces                                                  UInt            OBJMesh
  Steel_0010.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0010.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0010.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0011.vertex_count                                           int             OBJMesh
  Steel_0011.face_count                                             int             OBJMesh
  Steel_0011.faces                                                  UInt            OBJMesh
  Steel_0011.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0011.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0011.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0012.vertex_count                                           int             OBJMesh
  Steel_0012.face_count                                             int             OBJMesh
  Steel_0012.faces                                                  UInt            OBJMesh
  Steel_0012.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0012.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0012.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0013.vertex_count                                           int             OBJMesh
  Steel_0013.face_count                                             int             OBJMesh
  Steel_0013.faces                                                  UInt            OBJMesh
  Steel_0013.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0013.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0013.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0014.vertex_count                                           int             OBJMesh
  Steel_0014.face_count                                             int             OBJMesh
  Steel_0014.faces                                                  UInt            OBJMesh
  Steel_0014.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0014.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0014.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0015.vertex_count                                           int             OBJMesh
  Steel_0015.face_count                                             int             OBJMesh
  Steel_0015.faces                                                  UInt            OBJMesh
  Steel_0015.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0015.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0015.vertex_texcoords                              ∂        Float           OBJMesh
  Chrome_0010.vertex_count                                          int             OBJMesh
  Chrome_0010.face_count                                            int             OBJMesh
  Chrome_0010.faces                                                 UInt            OBJMesh
  Chrome_0010.vertex_positions                             ∂, D     Float           OBJMesh
  Chrome_0010.vertex_normals                               ∂, D     Float           OBJMesh
  Chrome_0010.vertex_texcoords                             ∂        Float           OBJMesh
  Steel_0016.vertex_count                                           int             OBJMesh
  Steel_0016.face_count                                             int             OBJMesh
  Steel_0016.faces                                                  UInt            OBJMesh
  Steel_0016.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0016.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0016.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0017.vertex_count                                           int             OBJMesh
  Steel_0017.face_count                                             int             OBJMesh
  Steel_0017.faces                                                  UInt            OBJMesh
  Steel_0017.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0017.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0017.vertex_texcoords                              ∂        Float           OBJMesh
  CarPaint_0004.vertex_count                                        int             OBJMesh
  CarPaint_0004.face_count                                          int             OBJMesh
  CarPaint_0004.faces                                               UInt            OBJMesh
  CarPaint_0004.vertex_positions                           ∂, D     Float           OBJMesh
  CarPaint_0004.vertex_normals                             ∂, D     Float           OBJMesh
  CarPaint_0004.vertex_texcoords                           ∂        Float           OBJMesh
  Dash.vertex_count                                                 int             OBJMesh
  Dash.face_count                                                   int             OBJMesh
  Dash.faces                                                        UInt            OBJMesh
  Dash.vertex_positions                                    ∂, D     Float           OBJMesh
  Dash.vertex_normals                                      ∂, D     Float           OBJMesh
  Dash.vertex_texcoords                                    ∂        Float           OBJMesh
  Black_0002.vertex_count                                           int             OBJMesh
  Black_0002.face_count                                             int             OBJMesh
  Black_0002.faces                                                  UInt            OBJMesh
  Black_0002.vertex_positions                              ∂, D     Float           OBJMesh
  Black_0002.vertex_normals                                ∂, D     Float           OBJMesh
  Black_0002.vertex_texcoords                              ∂        Float           OBJMesh
  Cabin.vertex_count                                                int             OBJMesh
  Cabin.face_count                                                  int             OBJMesh
  Cabin.faces                                                       UInt            OBJMesh
  Cabin.vertex_positions                                   ∂, D     Float           OBJMesh
  Cabin.vertex_normals                                     ∂, D     Float           OBJMesh
  Cabin.vertex_texcoords                                   ∂        Float           OBJMesh
  CarPaint_0005.vertex_count                                        int             OBJMesh
  CarPaint_0005.face_count                                          int             OBJMesh
  CarPaint_0005.faces                                               UInt            OBJMesh
  CarPaint_0005.vertex_positions                           ∂, D     Float           OBJMesh
  CarPaint_0005.vertex_normals                             ∂, D     Float           OBJMesh
  CarPaint_0005.vertex_texcoords                           ∂        Float           OBJMesh
  CarPaint_0006.vertex_count                                        int             OBJMesh
  CarPaint_0006.face_count                                          int             OBJMesh
  CarPaint_0006.faces                                               UInt            OBJMesh
  CarPaint_0006.vertex_positions                           ∂, D     Float           OBJMesh
  CarPaint_0006.vertex_normals                             ∂, D     Float           OBJMesh
  CarPaint_0006.vertex_texcoords                           ∂        Float           OBJMesh
  WindowGlass_0002.vertex_count                                     int             OBJMesh
  WindowGlass_0002.face_count                                       int             OBJMesh
  WindowGlass_0002.faces                                            UInt            OBJMesh
  WindowGlass_0002.vertex_positions                        ∂, D     Float           OBJMesh
  WindowGlass_0002.vertex_normals                          ∂, D     Float           OBJMesh
  WindowGlass_0002.vertex_texcoords                        ∂        Float           OBJMesh
  Steel_0018.vertex_count                                           int             OBJMesh
  Steel_0018.face_count                                             int             OBJMesh
  Steel_0018.faces                                                  UInt            OBJMesh
  Steel_0018.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0018.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0018.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0019.vertex_count                                           int             OBJMesh
  Steel_0019.face_count                                             int             OBJMesh
  Steel_0019.faces                                                  UInt            OBJMesh
  Steel_0019.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0019.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0019.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0020.vertex_count                                           int             OBJMesh
  Steel_0020.face_count                                             int             OBJMesh
  Steel_0020.faces                                                  UInt            OBJMesh
  Steel_0020.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0020.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0020.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0021.vertex_count                                           int             OBJMesh
  Steel_0021.face_count                                             int             OBJMesh
  Steel_0021.faces                                                  UInt            OBJMesh
  Steel_0021.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0021.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0021.vertex_texcoords                              ∂        Float           OBJMesh
  WindowGlass_0003.vertex_count                                     int             OBJMesh
  WindowGlass_0003.face_count                                       int             OBJMesh
  WindowGlass_0003.faces                                            UInt            OBJMesh
  WindowGlass_0003.vertex_positions                        ∂, D     Float           OBJMesh
  WindowGlass_0003.vertex_normals                          ∂, D     Float           OBJMesh
  WindowGlass_0003.vertex_texcoords                        ∂        Float           OBJMesh
  WindowGlass_0004.vertex_count                                     int             OBJMesh
  WindowGlass_0004.face_count                                       int             OBJMesh
  WindowGlass_0004.faces                                            UInt            OBJMesh
  WindowGlass_0004.vertex_positions                        ∂, D     Float           OBJMesh
  WindowGlass_0004.vertex_normals                          ∂, D     Float           OBJMesh
  WindowGlass_0004.vertex_texcoords                        ∂        Float           OBJMesh
  Steel_0022.vertex_count                                           int             OBJMesh
  Steel_0022.face_count                                             int             OBJMesh
  Steel_0022.faces                                                  UInt            OBJMesh
  Steel_0022.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0022.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0022.vertex_texcoords                              ∂        Float           OBJMesh
  InnerBody_0003.vertex_count                                       int             OBJMesh
  InnerBody_0003.face_count                                         int             OBJMesh
  InnerBody_0003.faces                                              UInt            OBJMesh
  InnerBody_0003.vertex_positions                          ∂, D     Float           OBJMesh
  InnerBody_0003.vertex_normals                            ∂, D     Float           OBJMesh
  InnerBody_0003.vertex_texcoords                          ∂        Float           OBJMesh
  Chrome_0011.vertex_count                                          int             OBJMesh
  Chrome_0011.face_count                                            int             OBJMesh
  Chrome_0011.faces                                                 UInt            OBJMesh
  Chrome_0011.vertex_positions                             ∂, D     Float           OBJMesh
  Chrome_0011.vertex_normals                               ∂, D     Float           OBJMesh
  Chrome_0011.vertex_texcoords                             ∂        Float           OBJMesh
  Steel_0023.vertex_count                                           int             OBJMesh
  Steel_0023.face_count                                             int             OBJMesh
  Steel_0023.faces                                                  UInt            OBJMesh
  Steel_0023.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0023.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0023.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0024.vertex_count                                           int             OBJMesh
  Steel_0024.face_count                                             int             OBJMesh
  Steel_0024.faces                                                  UInt            OBJMesh
  Steel_0024.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0024.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0024.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0025.vertex_count                                           int             OBJMesh
  Steel_0025.face_count                                             int             OBJMesh
  Steel_0025.faces                                                  UInt            OBJMesh
  Steel_0025.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0025.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0025.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0026.vertex_count                                           int             OBJMesh
  Steel_0026.face_count                                             int             OBJMesh
  Steel_0026.faces                                                  UInt            OBJMesh
  Steel_0026.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0026.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0026.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0027.vertex_count                                           int             OBJMesh
  Steel_0027.face_count                                             int             OBJMesh
  Steel_0027.faces                                                  UInt            OBJMesh
  Steel_0027.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0027.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0027.vertex_texcoords                              ∂        Float           OBJMesh
  Steel_0028.vertex_count                                           int             OBJMesh
  Steel_0028.face_count                                             int             OBJMesh
  Steel_0028.faces                                                  UInt            OBJMesh
  Steel_0028.vertex_positions                              ∂, D     Float           OBJMesh
  Steel_0028.vertex_normals                                ∂, D     Float           OBJMesh
  Steel_0028.vertex_texcoords                              ∂        Float           OBJMesh
  EnvironmentMapEmitter.scale                              ∂        Float           EnvironmentMapEmitter
  EnvironmentMapEmitter.data                               ∂, D     TensorXf        EnvironmentMapEmitter
  EnvironmentMapEmitter.to_world                                    Transform4f     EnvironmentMapEmitter
]
# 修改场景中变量(指定)
>> key = 'BackWallBSDF.brdf_0.reflectance.value'
# 得到待修改变量的引用
>> param_ref = mi.Color3f(scene_params[key])
# 修改要改变变量
>> scene_params[key] = mi.Color3F(0.01,0.2,0.9)
# 更新参数
>> scene_params.update()

# 按新参数重新渲染
>> image_init = mi.render(scene, seed=0, spp=128)
# 得到重新渲染的图
>> mi.util.conver_to_bitmap(image_init)

# 使用Adam更新指定变量
>> import drjit as dri
# 设置优化器和优化变量
>> opt = mi.ad.Adam(lr=0.05)
>> opt[key] = params[key]
>> params.update(opt)

>> def mse(image):
>>		return dr.mean(dr.sqr(image - image_ref))

>> errors = []
>> for it in range(50):
>> 		image = mi.render(scene, param, spp=4)
>> 		loss = mse(image)
>>		dr.backward(loss)
>>		opt.step()
>> 		opt[key] = dr.clamp(opt[key], 0.0, 1.0)
>>		err_ref = dr.sum(dr.sqr(param_ref - params[key])) 
>>		errors.append(err_ref)
>> print("\nOptimization complete.")

>> image_final = mi.render(scene, seed=0)
>> mi.util.convert_to_bitmap(image_final)
# 绘制误差损失曲线
>> import matplotlib.pyplot as plt
>> plt.plot(errors)
>> plt.xlabel('Iteration'); plt.ylabel('MSE(param)'); plt.title('Parameter error plot');
>> plt.show()
```

