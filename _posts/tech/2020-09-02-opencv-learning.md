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