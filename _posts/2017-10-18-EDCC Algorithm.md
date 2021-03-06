---
layout:     post
title:      关于EDCC算法
subtitle:   EDCC算法的详细说明
date:       2017-10-18
author:     Leosocy
header-img: img/post-bg-black-series-4.jpg
catalog: true
tags:
    - EDCC
    - 算法
---

# EDCC(Enhanced and Discriminative Competitive Code): 高效、准确的掌纹识别算法

## 掌纹识别

### 掌纹识别技术的优势

作为新崛起的生物特征识别技术之一，掌纹识别可以从分辨率较低的图像中提取出稳定、抗噪能力强且具有识别力的特征。对比其他的生物特征识别技术，掌纹识别技术有以下优势：

1. 与指纹相比，掌纹的识别面积更大，包含的信息更丰富，而且掌纹比指纹更不易受损具有长期稳定性。掌纹识别对图像的分辨率要求不是特别高，所以在采集设备的造价比指纹低很多。
1. 与人脸相比，掌纹不受眼镜、表情、妆容等因素的影响，稳定性较高，并且在用户接受度方面，掌纹的采集方式对用户更加友好。
1. 与虹膜、DNA相比，掌纹的识别准确率虽然不及它们，但是掌纹采集设备造价远低于这两种生物特征识别技术的采集设备。
1. 与签名、步态等行为特征相比，掌纹识别不受人们习惯的影响，特征不会随着时间改变，并且识别的准确率远高于行为特征。

综上所述，掌纹识别有识别精度高、采集设备成本低、稳定性高、用户接受度高等优点，在生活中的应用越来越广泛。

### 掌纹识别的基本步骤

掌纹特征识别过程分为数据库的创建和特征库的检索。

- 数据库创建通过采集注册样本，进行预处理、特征提取工作，之后形成数据库
- 特征库检索通过采集待测试的样本，在预处理和提取特征之后与掌纹数据库中的数据进行匹配，得到该待测样本的类别。

### 掌纹特征提取方法

掌纹特征提取方法主要分为四大类，分别为基于结构的方法、基于统计的方法、基于子空间的方法以及基于编码的方法。

1. 基于结构的方法主要是利用掌纹中主线和褶皱的方向和位置信息实现掌纹识别的方法，此类方法最为直观。但是无论是哪一种边缘检测算子都无法提取出所有的纹线，所以此类方法实用性很差，已经被人们渐渐放弃。
1. 基于统计的方法主要是利用统计特征，例如均值、方差等组成一组用来描述掌纹图像的特征向量，可以根据是否分块这一特性分为局部统计变量方法和全局统计量方法。基于局部统计量的方法通过将图像分成若干小块，分别统计每个小块的统计信息，然后将这些统计信息组合表示为整个掌纹的统计特征向量，如采用傅里叶变换、小波变换等方式获得掌纹图像的每个分块统计信息并进行识别。
1. 基于子空间的方法是将原始掌纹图像看做普通的图片模式，将图片对应的高维矩阵通过投影映射运算转化为低维向量或矩阵。根据投影变换的实现方式，分为线性子空间法和非线性子空间法。常用的子空间特征提取方法有主成分分析法(PCA)，FisherPalm方法，BDPCA(Bi-directional PCA)等。
1. 基于编码的方法是将掌纹图像看作纹理图像，根据某些规则对纹理图像进行编码。Zhang等提出了一种名为PalmCode的编码方法，该方法首先采用2DGabor对图像进行滤波处理，然后根据滤波结果的实部和虚部的正负来进行编码。Kong等提出使用六个方向的Gabor滤波器对掌纹图像滤波, 并对幅值最小的方向编码, 称为竞争编码(Competitive code)。由于竞争编码考察了掌纹图像的方向信息，对光照不敏感，因此识别精度很高。

## EDCC

掌纹充满了线条和纹理特征，具备丰富的方向信息。因此，基于方向编码被认为是最有效的掌纹特征提取方法。而竞争编码是辨识度极高的编码方法之一，它使用不同方向的滤波器与掌纹图像进行卷积，之后根据一定的编码规则对掌纹图像进行编码。

EDCC算法有以下关键点:

1. 通过图像增强算子处理原始掌纹图像，使得纹线更加突出，提取的方向更加准确。
1. 使用一组方向不同的2DGabor小波滤波器对图像进行滤波。
1. 选择滤波响应值最大和次大的方向作为该点所在纹线的主要方向和次要方向，进而进行编码。

下面将对这三个关键点进行详细解释。

### 图像增强

拉普拉斯算子是图像锐化常用的方法，锐化可以增强图像的对比度，突出灰度值变化明显的部分，使比较模糊的纹线变得清晰。

典型的核大小为的拉普拉斯算子:![](https://i.loli.net/2017/10/21/59eb3382cb19d.png)

使用上式中的算子对如下掌纹图像进行卷积

![](https://i.loli.net/2017/10/21/59eb33f8436b2.bmp)

得到增强掌纹后的结果如下图所示。

![](https://i.loli.net/2017/10/21/59eb343537443.png)

纹线明显被增强显示出来，而且纹线处的灰度值大大高于周围皮肤灰度值。

实验表明，拉普拉斯增强算子可以有效的增强纹线对比度。所以EDCC算法在对图像使用2DGabor小波滤波之前，先对图像进行拉普拉斯变换。

### 2DGabor小波

2DGabor小波滤波器与人类视觉刺激响应非常相似。如下图所示，

![](https://i.loli.net/2017/10/21/59eb399728299.png)

第一行是人类视觉感受野，第二行是Gabor小波滤波器，第三行是两者之间的残差，可见两者及其相似。而且Gabor小波可以变换方向和尺度，对于不同方向的纹线都能很好地适应。

函数形式表示如下：

![](https://i.loli.net/2017/10/21/59eb3a260404b.png)

![](https://i.loli.net/2017/10/21/59eb3a4a20358.png)

取3个尺度，6个方向构成一组滤波器，并对掌纹图像进行卷积，得到结果如下图所示

![](https://i.loli.net/2017/10/21/59eb3ab3874ed.png)

不难发现，经过2DGabor小波滤波后的掌纹纹线非常清晰。

### 编码

采用一组尺度统一方向不同的2DGabor小波滤波器对图像做卷积后，可以得到掌纹图像每一个像素点对应的响应值，不难推断最大响应值对应的滤波器方向可以近似表示该点的掌纹线方向，但并不是纹线准确的方向。

如下图所示，选择掌纹图像主线上的两个像素点与一组方向个数为6的2DGabor小波滤波器进行卷积，并计算相应滤波响应值。然后确定具有最大滤波响应的取向作为主要方向，从图中可以看出，(a)和(b)主要方向是相同的。

实际上，这两个像素的真正的主要方向分别在提取的主要方向的左侧和右侧。这种现象也在掌纹的其他部位也可以发现。因此，基于最大滤波响应提取的主要方向并不能准确地表示掌纹真实的方向。

![](https://i.loli.net/2017/10/21/59eb3ce12a9be.png)

根据滤波响应提取掌纹主要方向的方法基于掌纹图像中的像素属于一条线的基本假设。当滤波器的方向与掌纹的主要方向相同时，滤波响应将达到最大值。

换句话说，滤波器响应与主线和滤波器之间的重叠程度成正比。然而，因为在实践中使用的滤波器方向有限，可能没有Gabor滤波器具有与掌纹图像的主要方向相同的取向。结果，在这种情况下，提取的方向不能准确地表示掌纹的主要方向特征。

通常来说，2DGabor小波滤波器方向越靠近掌纹的主要方向，得到的滤波响应就越大。因此，提取主要方向（通常也具有较大的滤波响应）的相邻方向可以与主要方向相结合，来更准确地表示掌纹图像。

#### 方法

首先使用一个尺度、N个方向的一组2DGabor小波滤波器对图像进行卷积。
掌纹纹线上的一点I(x, y)的主要方向通过计算该点最大响应值对应的滤波器方向来确定。即: ![](https://i.loli.net/2017/10/21/59eb44de0bec2.png)

式中：C是掌纹图像的主要方向。令Cleft和Cright表示主要方向相邻的方向![](https://i.loli.net/2017/10/21/59eb452103f23.png)

通过比较Cleft和Cright方向对应的响应值，得到次要方向Cs的编码![](https://i.loli.net/2017/10/21/59eb455ea7c52.png)

由于C表示掌纹图像的主要方向特征，Cs表示次要方向特征，所以结合C和Cs能够较为准确表示纹线的真实方向。

(C, Cs)的计算例子如下图所示

![](https://i.loli.net/2017/10/21/59eb45a465ef2.png)

#### 匹配方式

在掌纹图像的匹配阶段，使用与竞争编码方法相似但不同的角距离来确定两个掌纹图像之间的相似度。

两个掌纹图像和之间的匹配得分定义为：![](https://i.loli.net/2017/10/21/59eb56ad4c0fd.png)

![](https://i.loli.net/2017/10/21/59eb56db5a549.png)

## 实验

### 验证

在掌纹验证这一步中，令掌纹数据库中每个掌纹图像与剩下所有的图像一一对比。假如两幅图像来自同一人，则称之为同类匹配，否则称之为异类匹配。

在多光谱数据库中，每个人有12张手掌图片，每个光谱对应的数据库有6000张图片，所以有个匹配，其中同类匹配有33000个，异类匹配有17964000个。在同济大学掌纹数据库中，同类匹配有114000个，异类匹配有71880000个。图5.7展示了使用EDCC算法在多光谱数据库以及同济大学数据库上得到的匹配得分分布情况。可以发现，同类匹配得分与异类匹配得分明显分离，并且同类匹配得分远高于异类匹配得分。

![](https://i.loli.net/2017/10/21/59eb57ffdd9f8.png)

![](https://i.loli.net/2017/10/21/59eb588595119.png)
![](https://i.loli.net/2017/10/21/59eb584ff209b.png)

**(a)-(e)分别为Red,Green,Blue,NIR,同济大学数据库**

正确接受率(GAR)和错误接受率(FAR)可以评估算法的性能，ROC曲线通过设置所有可能的阈值进而得到对应的GAR和FAR值，并以FAR、GAR的值做为横、纵坐标绘制曲线。

![](https://i.loli.net/2017/10/21/59eb61e520e28.png)

![](https://i.loli.net/2017/10/21/59eb620f24c4a.png)
![](https://i.loli.net/2017/10/21/59eb622756e76.png)

**(a)-(e)分别为Red,Green,Blue,NIR,同济大学数据库**

上图展示了EDCC算法、DCC算法、小波变换分块统计算法以及BDPCA+LDA算法在不同数据库上验证得到的ROC曲线。忽略产生过拟合的BDPCA+LDA算法，不难发现，当FAR相同时，EDCC算法的GAR最高。

等误率(Equal Error Rate, EER)是FAR=FRR对应的值。去除掉存在过拟合问题的BDPCA+LDA算法，不同算法对应的EER如表下表所示。可以发现，EDCC算法在所有数据库上达到了最低的EER。对比DCC算法，EDCC算法的EER下降率最大达到了73%((5.5105-1.4728)/5.5105)，平均下降率也在50%左右。

|         | EDCC      | DCC   | 小波变换分块信息统计 |
| :----:  |:---------:|:-----:|:-----------------: |
| Red     | 1.2612    | 4.0145| 2.2772             |
| Green   | 1.8715    | 4.7460| 3.3249             |
| Blue    | 1.7455    | 4.5881| 3.2456             |
| NIR     | 1.4728    | 5.5105| 2.0192             |
| 同济大学 | 3.6116    | 5.3703| 6.6747             |

### 识别

识别是指通过逐个匹配数据库内的掌纹，确定一个未知掌纹来自何人。

在本文的掌纹识别实验中，将每个手掌的N(N=1,2,3,4)张掌纹图像组成训练集，剩下的掌纹图像组成测试集。将测试集中的每张图像与训练集一一比较，来计算匹配得分，训练集中得分最高的样本对应的类别作为该测试图片的类别，通过此方法可以计算不同算法在不同训练集大小下的错误识别率。

![](https://i.loli.net/2017/10/21/59eb648e1e44e.png)

![](https://i.loli.net/2017/10/21/59eb64bc52383.png)
![](https://i.loli.net/2017/10/21/59eb64d98edd3.png)

**(a)-(e)分别为Red,Green,Blue,NIR,同济大学数据库**

实验结果如上图所示。可以发现，EDCC算法在训练样本较少时，仍然可以取得较高的识别率，并且在训练样本个数相同的条件下，EDCC算法的错误率明显低于其他算法，换句话说，EDCC算法可以使用更少的训练样本得到更高的识别准确率。