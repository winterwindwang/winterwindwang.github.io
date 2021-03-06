---
layout: post
title: opencv学习笔记
category: 读书
tag: React
keywords: opencv，C++
---

## opncv 读书笔记（1）

1. 一旦没有了指向cv::Mat对象的引用，分配的内存就会被自动释放。这可以避免C++动态内存分配中经常发生的内存泄露问题。

   ```
   // 所有图像都指向同一个数据块
   cv::Mat image1(image3);
   image2 = image3;
   ```

   对上面图像中的任何一个图像进行转换都会影响到其他图像。

2. 如果要对图像内容做一个深复制，可以使用copyTo方法，目标图像将会调用create方法。另一个生成图像副本的方法是clone,即创建一个完全相同的新图像。

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


11. 使用`cv::Mat`的`at(int x, int y)`方法可以访问元素，其中x是列号，y是行号。在编译时必须明确方法返回值的类型，因为`cv::Mat`可以接受任何类型的元素，所以程序员需要指定返回值的预期类型。因此，`at`方法被实现成一个模板方法，且不会进行任何类型转换。使用如下的方式

    ```
    image.at<uchar>(j,i) = value;
    image.at<cv::Vec3b>(j,i)[channel]=value;
    或使用
    image.at<cv::Vec3b>(j,i) = cV::Vec3V::Vec3b(value,value,value) // 使用短向量赋值
    ```

12. OpenCV存储通道数据的次序是蓝色、绿色和红色。

13. 类似的向量类型。所有类型都用cv::Vec<T,N>模板类定义，其中T是类型，N是xiangliang元素的数量。

    | 数据       | 向量                            |
    | ---------- | ------------------------------- |
    | 浮点数类型 | cv::Vec2f、cv::Vec3f、cv::Vec4f |
    | 无符号字符 | cv::Vec2b、cv::Vec3b、cv::Vec4b |
    | 短整型     | cv::Vec2d、cv::Vec3d、cv::Vec4d |

14. 修改图像内容时，图像参数没有必要采用引用传递的方式。编译器做代码优化时，用值传递参数的方法通常比较容易实现。

15. **cv::Mat_模板类** 如果知道矩阵的类型，就可以使用cv::Mat_类（cv::Mat类的模板子类）。在对矩阵元素进行修改时不必指明元素类型。

    ```
    // 用Mat模板操作图像
    cv::Mat_<uchar> image(image);  // 创建image时，就指定它的元素类型，因此可以使用下面的方式处理元素值
    image(500,100)=0; // 访问第50行，第100列处那个值
    ```

16. **基本减色算法** 假设N是减色因子，将图像中每个像素的值除以N（整数除法，不留余数）。然后将结果乘以N，得到N的倍数，并且刚好不超过原始像素值。加上N/2，就得到相邻的N倍数之间的中间值。对所有8位通道值重复这个过程，就会得到(256/N) x (256/N) x (256/N)种可能的颜色值。

    ```
    // data是一行的所有元素（指针地址）
    data[i] = data[i]/div*div + div /2;
    或*data++ = *data/div*div + div / 2;
    // 其他减色算法
    第一种：data[i] = (data[i]/div)*div + div / 2;// 取不超过又最接近的整数
    第二种：data[i] = data[i] - data[i]%div + div / 2; // 取模运算
    第三种：位运算。如果把减色因子限定为2的指数，那么把像素值得前n位掩码后就能得到最接近得div倍数。可以用简单得位移操作获得掩码，位运算的代码执行效率很高，适用效率为重的应用。
    	// 用来截取像素值的掩码
    	uchar mask = 0xFF<<n; // 如div=16, 则mask = 0XF0
    	减少运算:
    	*data &m= mask;   // 掩码
    	*data ++ += div>>1;   // 加上div/2
    	// 这里的+也可以改用“按位或”运算符
    ```

17. 在彩色图像种，图像数据缓存区的前3个字节表示左上角像素的三个通道的值，接下来的3个字节表示第1行的第2个像素，以此类推。一个宽为w高为H的图像所需的内存块大小为WxHx3 uchars。

18. 用下面代码可以获得每一行种像素值得个数

    ```
    int nc = image.cols * image.channels();
    ```

19. 使用下面代码可以直接访问图像种一行的地址，ptr方法，是一个模板方法，返回第j行的地址

    ```
    uchar* data = image.ptr<uchar>(j);
    ```

20. 构建与某个图像大小和类型都与输入图像相同的矩阵

    ```
    cv::Mat result;
    result.creat(image.rows, image.cols, image.type());
    ```

21. `cv::Mat`的isContinuous方法可以轻松判断图像有没有被填充。如果图像中没有填充像素，它就返回true。此外，还可使用如下的方法测试矩阵的连续性

    ```
    // 检查行的长度（字节数）与“列的个数x单个像素”的字节数是否相等
    image.step == image.cols * image.elemSize();
    ```

    为确保完整性，测试时还需要检查矩阵是否只有一行；如果是，这个矩阵就是连续的。但是不管哪种情况，都可以用isContinuous方法检查矩阵的连续性。

22. 用reshape方法修改矩阵的维数，就不需要复制内存或重新分配内存了。第一个参数是新的通道数，第二个参数是新的行数。列数会进行相应的修改。`image.reshape(1,1)`

23. 用`image.step`属性可以得到一行的总字节数（包括填充像素）。通常可以用下面方法得到第j行、第i列的像素的地址：

    ```
    // (j,i)像素的地址，即&image.at(j,i)
    data = image.data + j*image.step + i*image.elemSize();(不推荐使用)
    ```

24. 彩色图像的迭代器：

    ```
    cv::MatIterator_<cv::Vec3b> it;
    ```

    也可以使用在Mat_模板类内部定义的iterator类型：

    ```
    cv::Mat_<cv::Vec3b>::iterator it;
    ```

    然后就可以使用常规的迭代器方法begin和end对象进行循环遍历了。不同之处在于它们仍然是模板方法。

25. 常量迭代器定义如下：

    ```
    cv::MatConstIterator_<cv::Vec3b> it;
    \\ 或者
    cv::Mat_<cv::Vec3b> const_iterator it;
    ```

26. 如果要从图像的第二行开始，可以使用`image.begin<cv::Vec3b>()+image.cols`初始化`cv::Mat`迭代器。

27. 通过`cv::Mat_`实例的引用来获取迭代器的开始位置和结束位置，这样就不需要再begin和end方法中指定迭代器的类型了，因为再创建`cv::Mat_`引用时迭代器类型已被指定。

    ```
    cv::Mat_<cv::Vec3b> cimage(image);
    cv::Mat_<cv::Vec3b> iterator it = cimage.begin();
    cv::Mat_<cv::Vec3b> iterator itend = cimage.end();
    ```

28. 不要牺牲代码的清晰度来优化性能。简洁的代码总是更容易调试和维护。只有对程序效率至关重要的代码段，才需要进行重度优化。

29. OpenCV有一个非常实用的函数可以用来测算函数或代码段的运行使劲按，它就是`cv::getTickCount()`,该函数会返回从最近一次计算即开机到当前的时钟周期数。在代码开始和结束时记录这个时钟周期数，就可以计算代码的运行时间。若想得到**以秒为单位的代码运行时间**，可以使用另一个方法`cv::getTickFrequency()`,它返回每秒的时钟周期数，这里假定CPU的频率是固定的。

30. 需要反复计算的代码比优化后的代码慢10倍。处理的元素总数相同，使用较短的循环和多条语句通常也要比使用较长的循环和单条语句的运行效率高。与之类似，如果你要对一个像素执行N个不同的计算过程，那就在单个循环中执行全部计算，而不是写N个连续的循环，每个循环执行一个计算。

31. 针对连续图像生成一个循环，而不是对行和列运行常规的二重训练，使平均速度提高了10%。

32. 如果从图像中减去拉普拉斯算子部分，图像的边缘就会放大，因而图像也会变得更加尖锐。用以下方法计算锐化的数值:

    ```
    sharpened_pixel = 5* current - left - right - up - down; // 这里的left是与当前像素相邻的左侧像素，up是上一行的相邻像素，以此类推。
    ```

33. 如果要扫描相邻的像素，那么在使用锐化操作时，用户必须提供一个输出图像。图像扫描中使用三个指针，一个表示当前行，一个表示上面的行、一个表示下面的行。但是，有些像素的值是无法计算的看，比如在边界处。

34. 计算输出的像素的值时，调用了`cv::saturate_cast<uchar>`模板函数，并传入运算结果。（确保像素的值在0-255之间）。使用这个函数可以把结果调整到8位无符号数的范围内。如果输入参数是浮点数，就会得到最接近的整数。

35. 花时间处理极少数像素是没有意义的。（如处理边界的像素）

36. 用两个特殊的方法把边框的像素设置位了0，它们是row和col。这两个方法返回一个特殊的`cv::Mat`实例，其中包含一个单行的ROI（或单列ROI），具体范围取决于参数。这里没有进行复制，因为只要这个一维矩阵的元素被修改，原始图像也会被修改。用setTo方法来实现这个功能。

    ```
    result.row(0).setTo(cv::Scalar(0));  //  对于灰度图像
    result.row(0).setTo(cv::Scalar(a,b,c));  // 对于三通道彩色图像  
    ```

    这个语句把第一行的所有像素值设置为0。

37. 所有二进制运算符的用法都一样：提供两个输入参数，指定一个输出参数。有时还可以指定加权系数，作为运算时的缩放因子。

38. 两幅图像相加，可以用于创建特效图或覆盖图像中的信息。可以使用`cv::add`函数实现相加功能，但因为这次是想得到加权和，因此使用更精确的`cv::addWeighted`函数

    ```
    cv::addWeighted(image1, 0.7, image2, 0.9, 0., result);
    ```

39. `cv::add`是典型的具有多种格式的函数:

    ```
    // c[i] = a[i] + b[i];
    cv::add(imageA, imageB, imageC);
    // c[i] = a[i] + k;
    cv::add(imageA,cv::Scalar(k), resultC);
    // c[i] = k1 * a[i] + k2*b[i] + k3;
    cv::ADdWeighted(imageA, k1, imageB, k2, k3, resultC);
    // c[i] = k*a[i] + b[i];
    cv::scaleAdd(imageA,k,imageB,resultC);
    // 有些函数还可以指定一个掩码(使用掩码后，操作就只会在掩码值非空得像素上执行，掩码必须是单通道)。
    // 如果（mask[i]） c[i] = a[i] + b[i];
    cv::add(imageA, imageB, imageC, mask);
    ```

40. 所有场合都要使用`cv::saturate_cast`函数，以确保结果在预定的像素范围之内。

41. 这些图像必须有相同的大小和类型（如果输入图像的大小不匹配，输出图像会重新分配）。

42. 大部分C++运算符都已被重载，其中包括位运算&、|、^、~和函数min,max,abs。比较运算符`<、<=，==，!=，>, >=`也被重载，他们返回一个8位的二值图像。此外还有矩阵乘法m1*m2（都是cv::Mat实例）、矩阵求逆m1.inv()、变位m1.t()、行列式m1.determinant()、求范数v1.norm()、叉乘v1.cross(v2)、点乘v1.dot(v2)。

43. 有时需要分别对处理图像中的不同通道，例如只对图像中的一个通道执行某个操作。可以使用`cv::split`函数，将图像的三个通道分别复制到三个cv::Mat实例中。

    ```
    // 创建三幅图像的向量
    std::vector<cv::Mat> planes;
    // 将一个三通道图像分割为三个单通道图像
    cv::split(image, planes);
    // 加到蓝色通道上
    planes[0] ++ image2;
    // 将三个通道图像合并为一个三通道图像
    cv::merge(planes,result);
    ```

    `cv::merge`函数执行反向操作，即用三个单通道图像创建一个彩色图像。

## 处理图像的颜色

44. 一般来说，最好直接使用OpenCV函数。它可以快速简历复杂程序，减少潜在的错误，而且程序的运行效率通常也比较高。不过这样会执行很多的中间步骤，消耗更多内存。

45. RGB值差距的绝对值（也称**城区距离**）进行累加。注意，在现代体系结构中，浮点数的欧几里得距离的计算速度可能比简单的城区距离更快（还可以采用平方欧式距离，以避免耗时的平方根运算）。

46. 计算两个颜色向量间的距离

    ```
    return abs(color[0]-target[0]) + abs(color[1]-target[1]) + abs(color[2]-target[2]);
    // 也可使用计算向量的欧几里得范数的函数
    return static_cast<int>(cv::norm<int,3>(cv::Vec3i(color[0]-target[0],color[1]-target[1],color[2]-target[2])))
    ```

47. 计算与目标颜色的距离的绝对值`absdiff` ：计算图像的像素与标量之间差距的绝对值。该函数的第二个参数也可以不用标量值，而是改用另一幅图像，这样就可逐个像素地计算差距。因此两幅图像地尺寸必须相同。然后，用split函数提取出存放差距地图像地单个通道以便求和。

    ```
    cv::absdiff(image, cv::Scalar(target), output);
    ```

48. 用`cv::threshold`函数创建一个二值图像。这个函数通常用于将所有像素与某个阈值（第三个参数）进行比较，并在常规阈值化模式下，将所有大于指定阈值地像素值为预定地最大值，将其他像素赋值为0。

49. `floodFill函数`判断一个像素时，还要检查附近像素地状态，这是为了识别某种颜色地相关区域。用户只需指定一个起始位置和允许地误差，就可以找出颜色接近地连续区域。

    ```
    cv::floodFill(image,		// 输入/输出图像
    		cv::Point(100,50), 	//  起始点
    		cv::Scalar(255,255,255),	//  填充颜色
    		(cv:React*)0,		//	填充区域的边界矩形
    		cv::Scalar(35,35,35),	// 偏差的最小/最大阈值
    		cv::Scalar(35,35,35),	// 正差阈值，两个阈值通常相等
    		cv::FLOODFILL_FIXED_RANGE	// 与起始点像素比较
    );
    ```

50. **利用GrabCut算法分割图像** 上一节介绍了如何利用颜色信息，根据场景中的特点元素分割图像。物体通常有自己特有的颜色，通过识别颜色接近的区域，通常可以提取出这些颜色。该方法是OpenCV提供的常用的图像分割算法，虽然它比较复杂，计算量也很大，但结果通常很精确。适合从静态图像中提取前景物体。

51. RGB并不是**感知均匀的色彩空间**，这意味着，两种具有一定差距的颜色可能看起来非常接近，而另外两种具有同样差距的颜色看起来却差别很大。为解决这个问题，引入了一些具有感知均匀特性的颜色表示法。`CIEL*a*b*`就是一种这样的颜色模型。把图像转换到这种表示法后，我们就可以真正地使用图像像素与目标颜色之间地欧几里得距离，来度量颜色之间的视觉相似度。

52. 进行8位颜色转换时会产生舍入误差，因此转换过程并不是完全可逆的。YCrCb是JPEG压缩中使用的色彩空间。`CIE L*u*v和CIE L*a*b`是两种感知均匀的色彩空间。其对亮度通道使用同样的转换公式，但对色度通道则使用不同的表示法。为了实现视觉感知上的均匀，这两种色彩空间都扭曲了RGB的颜色范围，所以这些转换过程都是非线性的(因此计算量巨大).

53. CIE XYZ 色彩空间.他是一种标准色彩空间,用于设备无关的方式表示任何可见颜色.在luv和lab色彩空间的计算中,用XYZ色彩空间作为一种中间表示法.RGB和XYZ之间的转换是线性的.Y通道对应着图像的灰度版本.

54. HSV和HLS这两种色彩空间会把颜色分解成加值的色调饱和度组件或亮度组件.这种方式描述的颜色会更加自然.

55. RGB是一种被广泛接受的色彩空间。虽然它被视为一种在电子成像系统中采集和显示颜色的有效方法，但它其实并不直观，也并不符合人类对颜色的感知方式：我们更习惯用色彩、亮度或彩度（即表示该颜色是鲜艳的还是柔和的）来描述颜色。因此引入了基于色调、饱和度和亮度的色彩空间。

56. BGR转HSV

    ```
    // 转换成HSV色彩空间
    cv::Mat hsv;
    cv::cvtColor(image, hsv, CV_BGR2HSV);
    // 把3个通道分割进3副图像中
    std::Vector<cv::Mat> channels;
    cv::split(hsv, channels);
    // channels[0]  是色调
    // channels[1]	是饱和度
    // channels[2]	是亮度
    ```

    注意第三个通道表示颜色值，即颜色亮度的近似值。因为处理的是8位图像，所以OpenCV会把通道值得范围重新调节为0-255（色调除外，它的范围被调节为0-180）

57. 人类更喜欢用色彩、彩度、亮度等直观的属性来描述颜色，而大多数直觉色彩空间正是基于这三个属性。**色调(hue)**表示主色，我们使用的颜色名称（例如绿色、黄色和红色）就对应了不同的色调值；**饱和度（saturation）** 表示颜色的鲜艳程度，柔和的颜色饱和度较低，而彩虹的颜色饱和度就很高；最后，**亮度（brightness）**是一个主观的属性，表示某种颜色的光亮程度。而其他色彩空间使用颜色**明度（value）**或颜色**亮度（brightness）**的概念描述有关颜色的强度。

58. 在强度相同的情况下，人们会觉得绿色比蓝色等颜色的亮度更高。

59. OpenCV用一个公式来计算饱和度，该公式基于BGR组件的最小值和最大值。
    $$\S=\frac{max(R,G,B)-min(R,G,B)}{max(R,G,B)},.$$  

    其原理是：灰度颜色包含的R、G、B的成分是相等的，相当于一种既不饱和的颜色，因此它的饱和度是0（饱和度是一个0~1.0的值）。对于8位图像，饱和度被调节成一个0~255的值，并且作为灰度图像显示的时候，较亮区域对应的颜色具有较高的饱和度。

60. 如果颜色的饱和度很低，它计算出来的色调就不可靠。

61. 排除较高亮度的颜色可以降低把明亮的淡红色误认为皮肤的可能性。

## 用直方图统计像素

62. 在OpenCV中计算直方图，可简单地调用cv:calcHist函数。这是一个通用的直方图计算函数，可以处理包含任何值类型和范围的多通道图像。

63. cv::SparseMat表示大型稀疏矩阵（即非零元素非常稀少的矩阵），这样不会消耗过多的内存。

64. 通过分析图像中像素值的分布情况，你可以利用这个信息来修改图像，甚至提高图像质量。

65. **查找表**是个一对一（或多对一）的函数，定义了如何把像素值转换成新的值。它是一个一维数组，对于规则的灰度图像，它包含256个项目。利用查找表的项目i，可得到对应灰度级的新强度值，如下所示：

    ```
    newIntensity = lookup[oldIntensity];
    ```

66. OpenCV中的cv::LUTh函数在函数上应用查找表生成一个新的图像。查找表通常根据直方图生成，因此在Histogram1D类中加入了这个函数：

    ```
    // 输出图像
    cv::Mat result;
    // 应用查找表
    cv::LUT(image, lookup, result);
    ```

67. 对于需要更换全部像素强度值得程序，都可以使用查找表。但是这个转换过程必须是针对整幅图像的。一个强度值对应的全部像素都必须使用同一种转换方法。

68. **伸展直方图以提高图像对比度**，使它布满可用强度值的全部范围。即使图像的直方图的柱状图的分布更为均匀。这种方法确实可以简单有效地提高图像质量，但很多时候，*图像的视觉缺陷并不因为它使用的强度值范围太窄，而是因为部分强度值的使用频率远高于其他强度值*

69. 减少算法的新方法：预先计算好所有的减色值，然后用查找表修改每个像素。这种减色方案之所以能起作用，是因为在多通道图像上应用一维查找表时，同一个查找表会独立地应用在所有通道上。如果查找表超过一个维度，那么它和所有图像地通道数必须相同。

70. 在中等灰度的强度值非常多，较暗和较亮的像素值则非常稀少。因此，均衡对所有像素强度值得使用频率可以作为提高图像质量得一种手段。即让图像的直方图尽可能地平稳，也就是**直方图均衡化**。

71. OpenCV提供了一个易用的函数，用于直方图均衡化处理。

    ```
    cv::equalizeHist(image, result);
    ```

72. 在一个完全均衡的直方图种，所有箱子所包含的像素值数量是相等的。这意味着50%像素的强度值小于128（强度中值），25%像素的强度值小于64，以此类推。这个现象可以用一条规则来表示：%p像素的强度值必须小于或等于255*p%。这条规则用于直方图均衡化处理，表示强度值i的映像对应强度值小于i的像素所占的百分比。因此可以用下面的语句构建所需的查找表：

    ```
    lookup.at<uchar>(i) = static_cast<uchar>(255.0*p[i]/image.total());
    ```

    其中p[i]是强度值小于或等于i的像素数量，通常称为累计直方图。

73. **反向投影直方图检测特定图像内容** 如果图像的某个区域含有特定的纹理或物体，这个区域的直方图就可以看作一个函数，该函数返回某个像素属于这个特殊纹理或物体的概率。

74. 反向投影直方图的过程包括：从归一化的直方图种读取概率值并把输入图像中的每个像素替换成与之对应的概率值。OpenCV中可使用`cv::calcBackPorject`函数完成此任务。为了提高可读性，对图像做了反色处理，属于该区域的概率从亮（低概率）到暗（高概率）。

75. 通常而言，采用BGR色彩空间识别图像中的彩色物体并不是最好的方法。为了提高可靠性，在计算直方图之前减少了颜色的数量（原始BGR色彩空间有超过1600万种颜色）。提取的直方图代表了天空区域的典型颜色分布情况。*用它在其他图像上反向投影，也能检测到天空区域。用多个天空图像构建直方图可以提高检测的准确性。*

76. 如果要寻找色彩鲜艳的物体，使用HSV色彩空间的**色调通道**可能会更加有效。其他情况下，最好使用感知上均匀的色彩空间（例如`L*a*b`）的色调组件。

77. **用均值平移算法查找目标** 直方图反向投影的结果是一个概率分布图，表示一个指定图像片段出现在特定位置的概率。如果已经知道图像种某个物体的大致位置，就可以用概率分布图找到物体的准确位置。窗口中概率最大的位置就是物体最可能出现的位置。因此，从一个初始位置开始，在周围反复移动以提高局部匹配概率，也许就能找到物体的准确位置。该方法称为 ”均值平移算法给“。

78. 在使用颜色的色调分量时，要把它的饱和度考虑在内（饱和度是向量的第二个入口），这一点通常很重要。如果颜色的饱和度很低，它的色调信息就会变得不稳定且不可靠。这时因为低饱和度颜色的B、G和R分量几乎是相等的，这导致很难却低估它所表示的准确颜色。因此忽略低饱和度颜色的色彩分量，不把它们统计进直方图中（在getHueHistogram方法中使用minSat参数可以屏蔽掉饱和度低于此阈值的像素）。

79. 均值偏移是一个迭代过程，用于定位概率函数的局部最大值，方法是寻找预定义窗口内部数据点的重心或加权平均值。然后把窗口移动到重心的位置，并重复该过程，直到窗口中心收敛到一个稳定的点。其结果的好坏取决于指定初始位置提供的概率分布图的质量。

80. **比较直方图搜索相似图像** 根据一个已有的基准图像，找出一批内容相似的图像。直方图是标识图像内容的一种有效方式，因此值得研究一下能够用它来解决**基于内容**的图像**检索**问题。

81. OpenCV在`cv::compareHist`函数的实现过程中使用了其中的一些方法。即测量函数用于评估两个直方图之间的差异程度或相似程度。

82. 在测量两个颜色直方图的相似度时，把邻近颜色组合放进同一个箱子显得十分重要。比较器中使用交叉点方法（带有cv::HISTCMP_INTERSECT标志），逐个箱子地比较每个直方图中地数值，并保存最小地值。然后把这些最小值累加，作为相似度测量值。因此，两个没有相同颜色地直方图得到地交叉值为0，而两个完全相同的直方图得到的值就等于像素总数。
    其他可用算法：

    ```
    卡方测量法（cv::HISTCMP_CHISQR）累加各个箱子的归一化平方差
    关联性算法（cv::HISTCMP_CORREL）基于信号处理中的归一化交叉关联操作符测量两个信号的相似度；
    Bhattacharyya测量法（CV::HISTCMP_BHATTACHARYYA）和Kullback-Leibler发散度（cv::HISTCMP_KL_DIV）都用在统计学中，评估两个概率分布的相似度。
    ```

83. 推土机距离(Earth Mover Distance)是另一种流行的直方图比较方法，在OpenCV中通过`cv::EMD`函数实现。这种方法的优势在于，它在评估两个直方图的相似度时，考虑了邻近箱子中发现的数值。

84. **用积分图像统计像素** 使用积分图像可以有效地累计图像某个子区域内地像素总数。

85. **自适应的阈值化** 通过对图像应用阈值来创建二值图像是从图像中提取有意义元素的好方法。直接使用阈值化会丢失一部分图像的内容，还有部分文本会消失在阴影下。要解决该问题，有一个办法就是采用局部阈值，即根据每个像素的邻域计算阈值。*然后将每个像素的值与领域的平均值比较。如果某个像素的值与它的局部平均值差别很大，就会被当作异常值在阈值化过程中体重。*

86. 自适应阈值化是一种常用的图像处理技术。OpenCV中也实现了这种方法：

    ```
    cv::adaptiveThreshold(image,
    				binaryAdaptive,
    				255,
    				cv::ADAPTIVE_THRESH_MEAN_C, // 也可用高斯加权累加值,ADAPTIVE_THRESH_GAUSSIAN_C,且速度快
    				cv::THRESH_BINARY,
    				blockSize,
    				threshold
    				);
    ```

    此外，也可以使用OpenCV的运算符来编写自适应阈值化过程（图像滤波）。

87. **用直方图实现视觉追踪**。可用直方图表示物体外观的全局特征。



