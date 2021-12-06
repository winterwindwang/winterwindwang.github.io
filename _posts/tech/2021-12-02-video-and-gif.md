---
layout: post
title: 使用Python生成GIF图片和MP4视频
category: 技术
keywords: python, gif, video
tags: 视频、GIF图像的制作
---

# GIF图像的制作

```python
from glob import glob
import imageio

def make_gif(img_folder):
    """
    Make a gif image for giving image folder
    """
    images = []
	image_list = glob(os.path.join(img_folder, "*.png"))
    for img_path in image_list:
        images.append(imageio.imread(img_path))
    imageio.mimsave('save_dir/image.gif', images, fps=30)
```

这种方式生成gif图像会有点奇怪，颜色会发生变化，原因尚待查明



# MP4视频的制作

```python
import cv2
from glob import glob


def make_video(path):
    fps = 30  # 视频帧率
    size = (800, 1600)  # 需要转为视频的图片的尺寸
    # size = (1600, 800)  # 需要转为视频的图片的尺寸
    # video = cv2.VideoWriter(r"F:\Writing\PHD\condiate\output_yolov5s.avi", cv2.VideoWriter_fourcc('I', '4', '2', '0'), fps, size)
    video = cv2.VideoWriter(r"F:\Writing\PHD\AAAI2022\composed_vertical.mp4", 			cv2.VideoWriter_fourcc(*"mp4v"), fps, size)  # 创建视频流对象-格式一
    image_list = glob(os.path.join(img_folder, "*.png"))
    for img_path in image_list:
        image = cv2.imread(img_path)  # 注意image的尺寸与size的尺寸是相反的，比如size是(800, 1600)，那么image的尺寸就应该是（1600，800）
        video.write(image)

    video.release()
    cv2.destroyAllWindows()
```