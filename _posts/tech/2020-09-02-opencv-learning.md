---

layout: post

title:opencv学习笔记

category: 技术，opencv，C++

tag: React

keyword: React

---

## opncv 读书笔记

1. 一旦没有了指向cv::Mat对象的引用，分配的内存就会被自动释放。这可以避免C++动态内存分配中经常发生的内存泄露问题。

   ```
   // 所有图像都指向同一个数据块
   cv::Mat image1(image3);
   image2 = image3;
   ```

   对上面图像中的任何一个图像进行转换都会影响到其他图像。

2. 如果要对图像内容做一个深复制，可以使用copyTo方法，目标图像将会调用create方法。另一个生成图像副本的方法是clone,即创建一个完全相同的新图像:

   ```
   // 这些图像是原始图像的新副本
   image3.copyTo(image4);
   cv::Mat image5 = image3.clone();
   ```

3. 如果需要把图像复制到另一幅图像中，且两者的数据类型不一定相同，那就要使用convertTo方法了：

   ```
   // 转换成浮点型图像[0,1]
   image1.converTo(image2, CV_32F, 1/255.0, 0.0);
   ```

   注意**这两幅图像的通道数必须相同**

4. 在使用类的时候要特别小心，不要返回图像的类属性。如果某个函数调用了类方法对图像属性进行一个潜复制。副本一旦被修改，类属性也会被偷偷的修改。为避免这种类型的错误，需要将其改成返回属性的一个副本。

5. `cv::InputArray`也能使用常见的`std::vector`类来构造；也就是说，用这种方式构造的对象可以作为OpenCV方法和函数的输入参数（但千万不要在自定义类和函数中使用这个类）。

6. OpenCV内嵌了一个精致又简单的机制，可以定义图像的子区域，并把这个子区域当作普通函数进行操作。

7. 如果要实现**将一个小图像复制到一个大图像上**，可以定义一个**感兴趣区域（Region Of Interest, ROI）**,在此处进行复制操作，这个ROI的位置将决定标志的插入位置。

   ```
   // 插入标志
   logo.copyTo(imageROI); 其中imageROI指的是已经在右下角定义好的一个ROI，logo是指标志图像
   ```

   

8. 定义ROI的两种方法

   + 使用cv::Rect实例。正如其名，它通过指明左上角的位置（构造函数的前两个参数）和矩形的尺寸（后两个参数表示宽度（cols）和高度(rows)）描述一个矩形区域

     ```
     cv::Mat imageROI(image,		
     	cv::Rect(image.cols-logo.cols,
     		image.rows-logo.rows,
             logo.cols,
             logo.rows));
     ```

   + ROI还可以用行和列的值域来描述。值域是一个从开始索引到结束索引的连续序列(不含开始值和结束值)，可以用cv::Range结构来表示这个概念。

     ```
     imageROI = image(cv::Range(image.rows-logo.rows,image.rows),
     				cv::Range(image.cols-logo.cols, image.cols));
     ```

9. 要定义由图像中的一些行/列组成的ROI，可以使用下面的代码：

   ```
   cv::Mat imageROI = image.rowRange(start, end);  //行
   cv::Mat imageROI = image.colRange(start, end); // 列
   ```

   

10. **使用图像掩码** 函数或方法通常对图像中所有的像素进行操作，通过定义掩码可以限制这些函数或方法的作用范围。掩码是一个8位图像，如果掩码中某个位置的值不为0，在这个位置上操作就会起作用；反之则不会起作用。如，在调用copyTo方法时就可以使用掩码

    ```
    // 把标志作为掩码（必须是灰度图像）
    cv::Mat mask(logo);
    // 插入标志，只复制掩码部位0的位置
    logo.copyTo(imageROI, mask);
    ```

    OpenCV中大多数基于像素的才做都可以使用掩码。

    

