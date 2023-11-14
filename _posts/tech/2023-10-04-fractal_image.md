---
layout: post
title: 一种数据增强技术
category: 技术
keywords: python
tags: technical dety
---

# 一种数据增强技术

```python
def creat_fractal_image(train_folder, save_folder, image_size=640, fractal_factor=4):
    image_list = os.listdir(train_folder)
    image_new = np.zeros((image_size, image_size, 3), dtype=np.uint8)
    point_list = []

    # first middle point
    point_list.append((0, 0, image_size//2, image_size))
    # second middle point
    point_list.append((image_size // 2, 0, image_size, image_size // 2))
    # third middle point
    point_list.append((image_size // 2, image_size // 2, image_size - image_size // 4, image_size))

    # fourth middle point
    point_list.append((image_size - image_size // 4, image_size // 2, image_size, image_size - image_size // 4))
    point_list.append(
        (image_size - image_size // 4, image_size - image_size // 4, image_size, image_size))

    image_red = np.zeros((image_size, image_size, 3), dtype=np.uint8)
    image_red[:,:,0].fill(255)
    image_green = np.zeros((image_size, image_size, 3), dtype=np.uint8)
    image_green[:, :, 1].fill(255)
    image_blue = np.zeros((image_size, image_size, 3), dtype=np.uint8)
    image_blue[:, :, 2].fill(255)

    image_white = np.ones((image_size, image_size, 3), dtype=np.uint8) * 255

    for i, point in enumerate(point_list):
        if i == 0:
            image_new[point[1]:point[3], point[0]:point[2], :] = image_red[point[1]:point[3], point[0]:point[2], :]
        elif i == 1:
            image_new[point[1]:point[3], point[0]:point[2], :] = image_green[point[1]:point[3], point[0]:point[2], :]
        elif i == 2:
            image_new[point[1]:point[3], point[0]:point[2], :] = image_blue[point[1]:point[3], point[0]:point[2], :]
        elif i == 3:
            print(point)
            image_new[point[1]:point[3], point[0]:point[2], :] = image_white[point[1]:point[3], point[0]:point[2], :]
    Image.fromarray(image_new).save("fractal_test_image.png")

creat_fractal_image("None", "None")
```

