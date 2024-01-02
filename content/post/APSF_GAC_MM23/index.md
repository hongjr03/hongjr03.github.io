---
layout: post
title: "MM23 Enhancing Visibility in Nighttime Haze Images Using Guided APSF and Gradient Adaptive Convolution"
date: 2023-10-22
tags: [MM23, Dehazing, Nighttime]
---

[[Paper](https://arxiv.org/abs/2308.01738)] [[Code](https://github.com/jinyeying/nighttime_dehaze)]

![1](https://raw.githubusercontent.com/hongjr03/img/main/1.png)

## Abstract

在有雾的夜间场景中，可见度通常受到低光强、强光、光线散射和多种颜色的光源存在的影响。现有的方法对光源或低光场景表现不好。在本文中作者提出了一种抑制辉光并增强低光区域的方法来增强夜间有雾图像。

为**处理辉光**，作者提出了一个光源感知网络来检测夜间图像的光源，然后进行由 **APSF** (角点扩散函数) 指导的辉光渲染。然后网络框架根据辉光渲染图像进行训练从而达到抑制辉光的效果。作者还利用了**梯度自适应卷积**来捕捉朦胧场景中的边缘和纹理。通过利用提取的边缘和纹理，网络能在不丢失重要结构细节的情况下增强场景的对比度。

为**增强低光区域**，网络对一张感知图进行学习，然后利用感知图进行伽马校正。这个感知在低光区域的值较高，而在朦胧区域和辉光区域的值较低。这样的感知图能够帮助网络在低光区域进行增强，而不会对辉光区域进行增强。

## Introduction

...传统的非基于学习的白天去雾方法依赖于雾霾成像模型。然而，由于人工光源的存在和照明颜色的复杂性，该模型在夜间无效。因此，与白天不同，我们不能假设均匀的大气光颜色。此外，这种白天的雾霾模型没有考虑辉光的影响，因此在夜间也不适用。

> 类比到 UIE 上，光线充足的情况下，比如浅水，应该可以类比成白天的去雾方法对光在水中的衰减进行建模，而在深水中，光线衰减严重，应该类比成夜间的去雾方法。

贡献：

- 第一个基于机器学习的一次性处理夜间辉光和低光强度图像的方法。
- 提出了光源感知网络和 APSF 指导的辉光渲染来抑制辉光。
- 提出了梯度自适应卷积来进行边缘增强，并使用双边核 (bilateral kernel) 来进行纹理增强。

## Proposed Method

![image-20231030112306502](https://raw.githubusercontent.com/hongjr03/img/main/image-20231030112306502.png)

### 光源感知网络

夜间辉光可以这样建模：

$$
I_h(x) = I_c(x)t(x) + A(x)(1-t(x)) + L_s(X)*\text{APSF},
$$

其中，$I_h$ 是观测到的朦胧图像，它由场景中的光（无雾状态下的）$I_c$经过传播$t$、大气光$A$经过传播$(1-t)$和光源$L_s$经过$\text{APSF}$大气角点扩散函数卷积操作三部分组成。光线传播$t$可建模为$t(x) = e^{-\beta d(x)}$，其中$\beta$指消光系数。

> Q: 为什么是$(1-t)$？

光源有重要的作用：

1. 给$G_c$提供光源位置信息

2. 指导$G_h$输出$O_h$

3. 结合$\text{APSF}$渲染光照图片

对于1，论文定义了光源一致性损失（consistency loss）：

$$
\mathcal{L}_{ls}=\left|O_c \odot M - L_s\right|,
$$

其中$L_s$是光源，$O_c$是输出的干净图像，$M$是软抠图（soft matting）后的图像。要获得光源图，论文提出算法1：

![image-20231030161007297](https://raw.githubusercontent.com/hongjr03/img/main/image-20231030161007297.png)

1. 通过对输入进行阈值处理来生成初始的光源掩码
2. 使用透明度抠图算法处理光源掩码进而获得光源亮度图
3. 将原始图片和光源图作element–wise乘积，获得光源图

![image-20231030161618242](https://raw.githubusercontent.com/hongjr03/img/main/image-20231030161618242.png)

### APSF 指导的夜间辉光渲染

![image-20231030161751522](https://raw.githubusercontent.com/hongjr03/img/main/image-20231030161751522.png)

在获取光源图之后，作者使用APSF渲染夜间辉光。首先计算 APSF 函数并将其转换为 2D 格式使其实现2D卷积，从而产生辉光图片。然后将干净图像和发光图像结合起来，在最终图像中渲染出逼真的发光效果。

![image-20231030175809723](https://raw.githubusercontent.com/hongjr03/img/main/image-20231030175809723.png)

> 这边应该是一个把2DAPSF函数转换成卷积核权重的一个算法？
>
> angle (-180◦ to 180◦) , 光学厚度 optical thickness , 前向散射参数 forward scattering parameter

### 梯度自适应卷积

夜间朦胧图像存在**对比度低**、**纹理缺失**和**照明不均匀**的问题。![img](https://raw.githubusercontent.com/hongjr03/img/main/v2-53ce3efea1f418c56566c6a9f58e4b51_1440w.webp)

![image-20231030182849477](https://raw.githubusercontent.com/hongjr03/img/main/image-20231030182849477.png)

#### 使用梯度卷积进行边缘捕获

主要的想法是用局部二值模式（LBP, local binary patterns）在$k \times k$邻域中进行梯度卷积。和传统卷积的区别在于，LBP注重的是一片邻域内的像素和中心像素的差。它将中心像素的值作为阈值，如果超过了就记为$1$，否则记为$0$。

![局部二值模式(Local Binary Patterns)进行纹理分类 - 走看看](https://raw.githubusercontent.com/hongjr03/img/main/OIP.bbwAQ0tsPfLCH62YbYBQgQHaEJ)

![image-20231030182203940](https://raw.githubusercontent.com/hongjr03/img/main/image-20231030182203940.png)

然后论文使用一个预训练的模型来建立一致性损失：
$$
\mathcal{L}_g=\left|D(O_c)-D(I_h)\right|.
$$

#### 使用双边内核进行纹理捕获

使用单独的卷积网络可能会丢失纹理信息，因此论文使用双边内核进行卷积以捕获纹理信息。

![image-20231030182834409](https://raw.githubusercontent.com/hongjr03/img/main/image-20231030182834409.png)

在这个式子中，$K$是双边卷积核。

> tbc

### 低光区域增强

论文使用低光增强模块来提高低光区域的可见度，这个模块生成低光区域的注意力图以专注增强可见度。注意力图 A 在物体区域表现出高值，在均匀区域（例如天空）表现出低值。因此，我们可以区分物体和雾区域。

![img](https://raw.githubusercontent.com/hongjr03/img/main/v2-0d6ad415ffacfd3af9bd43ac9adf5412_1440w.webp)

其中$\gamma=0.3$是增强参数， $O_c$是去雾后的图像， $O_e$是增强后的图像，$A$是注意力图。

### 网络和损失
