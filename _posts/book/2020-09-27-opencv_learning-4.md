---
layout: post
title: opencv学习笔记(二)
category: 读书
tag: React
keywords: opencv，C++
---

## opncv 读书笔记（3）

## 描述和匹配兴趣点

198. 好的描述子要具有足够的独特性，能唯一地表示图像中地每个关键点。它还要有足够地鲁棒性，在照度变化或视角变动时仍能较好地体现同一批点集。理想地描述子还要简洁，以减少对内存地占用、提高计算效率。

199. *图像匹配是关键点的常用功能之一*，它的作用包括关联同一场景的两幅图像、检测图像中事务的发生地点。

200. 通过特征点匹配，可以将一幅图像的点集和另一幅图像（或一批图像）的点集关联起来。如果两个点集对应着现实世界中的同一场景元素，它们就应该是匹配的。

201. 比较图像块内像素的强度值来衡量两个正方形图像块的相似度，常见的方案是采用简单的**差的平方和**（Sum of Squared Differences, SSD）算法。

202. 图像分析中的一个常见任务是检测图像中是否存在特定的图像或物体。实现方法是把包含该物体的小图像作为模板，然后在指定图像上搜索与模板相似的部分。搜索的范围通常仅限于可能发现该物体的区域。在这个区域上滑动模板，并在每个像素位置计算相似度。

203. 特征描述子通常是一个N维的向量，在光照变化和拍摄角度发生微小扭曲时，它描述特征点的方式不会发生变化。通常可以用简单的差值矩阵来比较描述子，例如欧几里得距离。**特征描述子是一种非常强大的工具，能进行目标的匹配。**

204. 兴趣点描述子的计算结果是一个矩阵（即cv::Mat实例），矩阵的行数等于关键点容器的元素个数。每行是一个N维的描述子容器。SURF描述子的默认尺寸是64，而SIFT的默认尺寸是128。这个容器用于区分特征点周围的强度值图案。两个特征点越相似，它们的描述子容器就会越接近。SURF兴趣点并不一定要使用SURF描述子，SIFT也一样；**检测器和描述子可以任意搭配**。

205. 好的特征描述子不受照明和视角微小变动的影响，页不受图像中噪声的影响，因此它们通常基于局部强度值得差值。

206. 三种可以提高匹配质量的策略：

     + **交叉检查匹配项** 有一种简单的方法可以验证得到的匹配项，即重新进行同一个匹配过程，但在第二次匹配时，将第二幅图像的每个关键点逐个与第一幅图像的全部关键点进行比较。只有在两个方向都匹配了同一对关键点（即两个关键点互为最佳匹配）时，才认为是一个有效的匹配项。

       ```
       cv::BFMathcer matcher2(cv::NORM_L2,	// 度量距离 
       						true);		// 交叉检查标志
       ```

     + **比较检验法**， 若场景中相似的物体有很多时，一个关键点可以与多个其他关键点匹配。其中错误的匹配项非常多，最好能够把它们排除掉。为此我们需要维每个关键点找到两个最佳的匹配项，可以用cv::Descriptor Mathcer类的knnMatcher实现这个功能。

       ```
       // 为每个关键点找出两个最佳匹配项 
       std::vector<std::vector<cv::DMathc>> mathces;
       matcher.knnMatch(descriptors1, descriptor2,
       			matches, 2); // 找出k个最佳匹配项
       ```

     + **匹配差值的阈值化**， 还有一种更加简单的策略，就是把描述子之间差值太大的匹配项排除。

       ```
       // 指定范围的匹配
       float maxDist = 0.4;
       std::vector<std::vector<cv::DMatch>> matches2;
       matcher.radiusMatch(descriptor1, descriptor2, matches2, maxDist);
       // 两个描述子之间的最大允许差值
       ```

## 用二值图描述匹配关键点

207. 二值描述子一律使用Hamming规范
208. 因为OpenCV检测器和描述子具有泛型接口，所以二值描述子（例如ORB）的用法与SURF、SIFT没有什么区别。
209. ORB就是在BRIEF描述子的基础上构建的（前面介绍过BRIEF描述子），然后在关键点周围的邻域内随机选取一对像素点，创建一个二值描述子。比较这两个像素点的强度值，如果第一个点的强度值较大，就把对应描述子的位置（bit）设为1，否则就设为0。对一批随机像素点进行上述处理，就产生了一个由若干位（bit）组成的描述子，通常采用128到512位（成对地测试）。
210. **FREAK（Fast Retina Keypoint, 快速视网膜关键点）**，与BRISK一样，也是基于同心圆定义地采样模式。但为了设计描述子，设计者们使用人眼进行了类比。发现，随着离中央凹距离越来越远，视网膜上地神经节细胞密度越来越小。

## 估算图像之间的投影关系

211. 在实际应用中，图像坐标通常用像素值表示，而三维坐标通常用实际长度表示（例如用米作单位）。

212. 同一场景的两幅图像之间的投影关系。可以移动相机，从两个视角拍摄两幅照片；也可以使用两个相机，分别对同一个场景拍摄照片。如果两个相机被刚性基线分割，我们就称之为**立体视觉**。

213. 要根据图像中的一个点找到另一幅图像中对应的点，就需要在第二个成像平面上沿着这条线的投影搜索。这条虚线称为点x的**对极限**。它规定了两个对应点必须满足的基本条件，即对于一个点，在另一个视图中与它匹配的点必须位于它的对极线上，并且对极线的准确方向取决于两个相机的相对位置。

214. 一幅图像的极点位于所有对极限的交叉点，是另一个相机中心点的投影。注意，对极限的交叉点很可能（也经常）在图像边界的外面。

215. 如果想要精确地计算基础矩阵，匹配项的选择是很重要的。一般来说，匹配项要在整幅图像中均匀分布，并包含场景中不同深度的点，否则结果就会不稳定。尤其当所选场景点位于同一平面时，基础矩阵就会变差。

216. 所有对极限都穿过同一个点（极点）对矩阵产生了一个约束条件，这个约束条件把计算基础矩阵所需的匹配次数缩减到7次。

217. **用RANSAC（随机抽样一致性）算法匹配图像** 当用两个相机拍摄同一个场景时，会在不同的视角下看到相同的元素。

218. 在匹配两幅图像的特征点时，只接受位于对极线上的匹配项。若要判断是否满足这个条件，必须先知道基础矩阵，但计算基础矩阵又需要优质的匹配项。

219. 可以根据一些特征点匹配项计算图像对的基础矩阵。为了确保结果准确，采用的匹配项必须都是优质的。

220. RANSAC算法旨在根据一个可能包含大量局外项的数据集，估算一个特定的数学实体。其原理是从数据集中随机选取一些数据点，并仅用这些数据点进行估算。选取的数据点数量，应该是估算数学实体所需要的最小数量。对于基础矩阵，最小数量是8个匹配对（实际上只有7个，但是8个点的线性算法速度较快）。用着8个随机匹配对估算基础矩阵后，对剩下的全部匹配项进行测试，验证其是否满足根据这个矩阵得到的极线约束。标识出所有满足极线约束的匹配项（即特征点与对极限距离很近的匹配项），这些匹配项就组成了基础矩阵的支撑集。

221. RANSAC算法背后的核心思想是：支撑集越大，所计算矩阵正确的可能性就越大。反之，如果一个（或多个）随机选取的匹配项是错误的，那么计算得到的基础矩阵也是错误的，并且它的支撑集肯定会很小。反复执行这个过程，最后留下支撑集最大的矩阵作为最佳结果。

222. 提高最终匹配集的质量，必须要考虑以下三点：
     + 计算复杂度
     + 最终匹配项数量
     + 要得到仅包含准确匹配项的匹配集所需的可信度等级

223. **改进匹配项**。在双视图系统中，每个点肯定位于与它对应的点的对极线上。这就是基础矩阵表示的极线约束。因此，如果已经有了很准确的基础矩阵，就可以利用极线约束来更正得到的匹配项，具体做法是将强制匹配项置于它们的对极线上。使用OpenCV可以实现该功能：

     ```
     std::vector<cv::Point2f> newPoints1, newPoints2;
     // 改进匹配项
     correctMatches(fundamental,		// 基础矩阵
     				points1, points2,	// 原始位置
     				newPoints1, newPoints2);	// 新位置
     ```

     这个函数修改每个对应点的位置，从而在最小化累积（平方）位移时能满足极线约束。

224. **单应矩阵**表示在特殊情况下（这里指纯旋转或平面目标），世界坐标系的点和它的影像之间是线性关系。此外，由于该矩阵是可逆的，所以只要两个视图只是经过了旋转，或者拍摄的是平面物体，那么就可以将一个视图中的像素点与另一个视图中对应的像素点直接关联起来。

225. 计算单应矩阵后，就可以把一副图像的点转移到另一幅图像。实际上，图像中的每个像素都可以转移，因此可以把整幅图像迁移到另一幅图像的视点上。这个过程称为图像的**拼接**， 常用于将多幅图像构建成一幅大型全景图。

     ```
     // 找到第一幅图像和第二幅图像之间的单应矩阵
     std::vector<char> inliers;
     cv::Mat homography = cv::findHomography(
     					points1,
     					points2,	// 对应的点
     					inliers,	// 输出的局内匹配项
     					cv::RANSAC,	// RANSAC方法
     					1.);		//	到重复投影点的最大距离
     // 将第一幅图像扭曲到第二幅图像
     cv::Mat result;
     cv::warpPerspective(image1,		// 输入图像
     					result,		// 输出图像
     					homography,	// 单应矩阵
     					cv::Size(2*image1.cols, iamge.rows));
     					// 输出图像的尺寸
     // 把第一幅图像复制到完整图像的第一个半边（根据两幅图像中相同的区域拼接两幅图像）
     cv::Mat half(result, cv::Rect(0,0,image2.cols, image2.rows));
     images.coptyTo(half);	// 把images2复制到image1的ROI区域
     ```

226. 由于第二个视图中的部分场景在第一个视图中是不可见的，因此可以用单应矩阵通过读取另一幅图像中额外的像素点的颜色来扩展图像。

227. 计算单应矩阵时至少需要四个匹配项，并且它也使用了RANSAC算法。在找到具有最佳支撑集的单应矩阵后，cv::findHomography方法就会用到所有识别到的局内项对它进行优化。

228. **用cv::Stitcher生成全景图**。它会估计相机的内部和外部参数，以便图像能更好地对齐。它还可以修正曝光条件上的差距，提高图像的拼合质量。

     ```
     // 读取输入的图像
     std::vector<cv::Mat> images;
     images.push_back(cv::imread("parliament1.jpg"));
     images.push_back(cv::imread("parliament2.jpg"));
     
     cv::Mat panorama; // 输出的全景图
     // 创建拼接器
     cv::Stitcher stitcher = cv::Stitcher::createDefault();
     // 拼接图像
     cv::Stitcher::Status status = stitcher.stitch(images, panorama);
     ```

229. **检测图像中的平面目标** 利用一个平面上的多幅图像也可以生成视角之间的单应矩阵这个特点，识别出图像的平面目标。

## 三维重建

230. 针孔相机最关键的参数是焦距和成像平面发小（它决定了相机的视野）。此外，对于数字图像来说，成像平面上像素的数量（即分辨率）也是相机的重要参数。三维空间的点（X,Y,Z)会投射到成像平面上（fX/Z, fY/Z）。如果想把这个坐标转换成以像素为单位，需要将二维图像上的坐标分别除以像素的宽度（px）和高度（py）。将焦距的长度值（通常以毫米为单位）除以py，结果为焦距（水平方向）的像素数，这个值定义为fx。同样，fy=f/py是焦距在垂直方向的像素数。

231. 可以用图像传感器的大小（单位一般为毫米）除以像素数量(水平或垂直方向)，得到像素的实际大小。现在图像传感器的像素是正方形的，即水平和垂直方向的大小相等。

232. 相机标定就是设置相机各种参数（即投影公式中的项目）的过程。当然也可以使用相机厂家提供的技术参数，但是对于某些任务（例如三维重建）来说，这些技术参数是不够精确的。利用正确的相机标定方法，即可得到精确的标定信息。

233. 真正有效的标定过程，就是用相机拍摄特定的图像并分析得到的图像，然后在优化过程中确定最佳的参数值。OpenCV的标定函数已经让这个复杂过程变得很容易。

234. 标定后，相机就可以用来构建照片与现实场景的对应关系。如果一个物体的三维结构是已知的，就能得到它在相机传感器上的成像情况。

235. 相机的标定参数是能够获取到的，因此可以使用世界坐标系，还可以用它在相机姿态和对应点的位置之间建立一个物理约束。这里引入一个新的数学实体---**本质矩阵**。简单来说，本质矩阵就是经过标定的基础矩阵。

236. 但经过了标定。和基础矩阵一样，你可以用图像对应关系计算出本质矩阵，但后者是用世界坐标系表示的。

237. 三维重建只受限于缩放因子。如果要测量实际尺寸，就必须预先确定至少一个长度值，例如两个相机之间的实际距离或者画面中某个物体的实际高度。

238. 在计算机视觉研究中，三维重建的内容非常广泛。

     + **分解单应矩阵**， 本质矩阵是可以分解的，从而得到两个相机之间的旋转量和平移量。平面的两个视图之间存在一个单应矩阵，这里的单应矩阵也包含旋转量和平移量这两个分量。此外，它还包含平面的信息，即从每个相机到平面的法线。
     + **光束调整**， 首先根据匹配项计算相机位置，然后通过三角剖分实现三维重建。这个过程可以一般化，使用任意数量的视图。对每个视图都检测特征点，并与其他视图匹配。有了这些信息，就可以建立方程，将视图间旋转/偏移量、三维点集以及标定信息关联起来。然后，进行一个很长的优化过程，使所有点在每个视图（如果视图上有这个点）上的重投影误差达到最小，使全部未知项得到优化。这个经过组合的优化过程就是**光束调整**。

239. 装上两台相机后，机器也可以看到三维世界，这就是**立体视觉**。在同一个设备上安装两台相机，让它们观察同一个场景，并且两者之间有固定的基线（即相机之间的距离），就构成了一个立体视觉装置。

240. 计算视差的质量主要取决于场景中不同物体的分布情况。反差较大的区域比较容易准确匹配，因而计算得到的视差也更加精确。另外，基线越大，能检测到的深度值范围也越大。但是扩大基线后，视差的计算过程会更加复杂，可靠性也会降低。

241. 视差计算是一个非常复杂的过程，通常包含以下四个步骤：

     + 计算量很大的匹配过程
     + 计算量很大的聚合过程
     + 计算视差并优化
     + 细化结果。

242. 在匹配两个点时，必须计算出特定矩阵的匹配代价。这个过程可能是**简单的求绝对值、计算强度、色彩或梯度的均方差**

243. 视频信号是重要的视觉信息来源。视频由一些列图像构成，这些图像称为**帧**，帧是以固定时间间隔获取的（称为**帧速率**，通常用帧/秒表示），据此可以显示运动中的场景。

244. 用下面的方法获得视频文件的总帧数（整数）：

     ```
     long t = static_cast<long>(capture.get(CV_CAP_PROP_FPS))
     ```

245. cv::waitKey函数中指定的延时为0，那么它将永远等待下去，直到用户按下一个键。这种方法非常使用于需要通过逐帧检查以跟踪一个过程的情况。

246. Opencv用cv::ViedeoWriter类写视频文件。构建实例时需要指定文件名、播放视频的帧速率、每个帧的尺寸以及是否为彩色视频：

     ```
     writer.open(outputFile,	// 文件名
     			codec,		// 所用的编解码器
     			framerate,	// 视频的帧速率
     			frameSize,	// 帧的尺寸
     			isColor);	// 彩色视频？
     ```

     另外，必须指明保存视频数据的方式，即codec参数。

247. **编解码器**是一个软件模块，用于编码和解码视频流。把视频写入文件时，需要使用一个编解码器。编解码器定义了文件格式和用于存储信息的压缩方案。用某种编解码器进行编解码的视频，必须用同一种编解码器才能解码。用四个字符的代码来指定一种编解码器。这样，软件工具在写入视频文件之前，需要先读取这个四字符代码，以决定采用哪种编解码器。

     ```
     // 取得编解码器的四字符代码
     returned.value = static_cast<int>(capture.get(cv::CAP_PROP_FOURCC));
     // 取得四个字符
     codec[0] = returned.code[0];
     codec[1] = returned.code[1];
     codec[2] = returned.code[2];
     codec[3] = returned.code[3];
     // 返回代码的整数值
     return returned.value;
     ```

248. 对视频序列进行时序分析，以提取运动中的前景物体。实际上，用固定位置的像机拍摄时，背景部分基本上保持不变的。这种情况下，关注的是场景中的移动物体。为了提取这些前景物体，我们需要构建一个背景模型，然后将模型与当前帧做比较，检测出所有的前景物体。*前景提取是智能监控程序的基本步骤*。

249. 使用**滑动平均值（移动平均值）**动态地构建背景模型。这是一种计算时间信号平均值的方法，该方法还考虑了最新收到的数值。

250. 用`cv::accumulateWeighted`函数计算图像的滑动平均值非常方便，它在图像的每个像素上应用滑动平均值计算公式。作为结果的图像必须是浮点数类型的。所以，在比较背景模型与当前帧之前，必须先把前者转换成背景图像。对差异绝对值进行阈值化（先用cv::absdiff计算，再用cv::threshold）以提取前景图像。然后把这个前景图像作为cv::accumulateWeight函数的掩码，防止修改已被认定为前景的像素。因为在前景图像中，已被认定为前景的像素值为false，即0（这也是结果图像的前景物体呈黑色的原因）。

251. 如果构建彩色背景，就需要在多个彩色空间下计算滑动平均值。**上述方法的主要难点在于，如何针对特定的视频选择合适的阈值，以得到满意的结果。这也是参数化计算机视觉算法中经常遇到的问题。**

252. 因为物体以不同的速度在不同的方向上移动，或者是因为相机在移动（或者两者都有），从而会长生可见运动或者**表观运动**。

253. 跟踪表观运动都是极其重要的。它可以用来追踪运动中的物体，以测定它们的速度、判断它们的目的地。**对于手持摄像机拍摄的视频，可以用这种方法消除抖动或减少抖动幅度，使视频更加平稳**。运动估值还可用于视频编码，用以压缩视频，便于传输和存储。被跟踪的运动可以是稀疏的（图像的少数位置上有运动，称为**稀疏运动**），也可以是稠密的（图像的每个像素都有运动，称为**稠密运动**）。

254. **跟踪视频中的特征点**，跟踪在多个帧之间移动的特征点，对图像序列进行时序分析。

255. 如果想找到特征点在下一帧的新位置，就必须在它原来位置的周围进行搜索。可以由函数`cv::calcOpticalFlowPyrLK`实现。在函数中输入两个连续的帧和第一幅图像中特征点的向量，将返回新的特征点位置的向量。为了在整个视频序列中跟踪特征点，要一阵一阵地重复上述过程。

256. 要逐帧地跟踪特征点，就必须在后续帧中定位特征点的新位置。假设每个帧中特征点的强度值是不变的，这个过程就是寻找如下的位移(u,v):
                                                                             $I_t(x,y)=I_{t+1}(x+u,y+v)$
     其中$I_t$和$I_{t+1}$分别当前帧和下一个瞬间的帧。

257. **估算光流**，相机在进行拍摄时，物体的亮度值被投影到成像传感器上，从而形成了照片。

258. 三维运动向量的投影图被称作**运动场**。但是只有一个相机传感器的情况下，是不可能直接测量三维运动的，我们只能观察到帧与帧之间运动的亮度模式。亮度模式上的表现运动被称作**光流**。通常认为运动场和光流是等同的，其实不一定。

259. 通过优化亮度恒定约束和光滑函数的组合函数，可以估算出光流场。

260. 是否能长时间不失去目标，是判读啊跟踪器好坏最重要的指标。

261. 中值跟踪法是众多基于特征点跟踪的可视化物体跟踪方法中的一种。还有一类方法基于模板匹配，其中有代表性的是**Kernelized Correlation Filter滤波法，KCF**。该算法使用了一个特殊技巧，即基于傅里叶变换来计算模板。根据信号处理理论，在图像上关联模板就相当于是频域内的图像乘法运算。采用这种方法，可以显著提高在下一帧中识别匹配窗口的速度，KCF也因此成为最快和鲁棒性最好的跟踪器之一。

262. 样本的建模方式对分类器的性能有非常重要的影响。通常认为像素级别的建模方式过于低级，难以鲁棒地表示每个类别地内在特性。选用的模型最好能在多尺度下描述图像的独特图案。

263. 支撑增强型级联分类器的两个原理。第一个原理，将多个弱分类器（即基于单一特征的分类器）组合起来，可以形成一个强分类器。第二个原因，因为机器视觉中的负样本比正样本多，所以可以把分类过程分为多个阶段，以提高效率。前面的阶段可以快速排除掉明显不符合要求的实例，后面的阶段可以处理更复杂的样本，进行更精确的判断。

264. 级联分类器的核心思想是在早期排除掉不符合要求的样本。

265. **行人检测**。物体的图片主要靠形状和纹理区分彼此，这些特征可以用**方向梯度直方图**模型表示。这种模型的基础就是图像梯度的直方图；具体来说是梯度方向的分布图，因为我们更加关注形状和纹理。

