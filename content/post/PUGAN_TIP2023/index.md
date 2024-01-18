---
layout: post
title: "TIP23 PUGAN: A Physics-Guided Generative Adversarial Network for Underwater Image Enhancement"
date: 2023-12-04
image: cover.png
tags: [TIP, UIE, Physics-Guided]
categories:
    - 计算机视觉
    - 水下图像增强
---

Runmin Cong, Senior Member, IEEE, Wenyu Yang, Wei Zhang, Senior Member, IEEE, Chongyi Li, Senior Member, IEEE, Chun-Le Guo, Qingming Huang, Fellow, IEEE, and Sam Kwong, Fellow, IEEE

![img](https://raw.githubusercontent.com/hongjr03/img/main/cong2-3286263-large.gif)

## Proposed Method

### A. 物理模型指导的生成器（Phy-G）

#### 1) 参数估计子网络（Par-subnet）

$$I(x) = J(x)t(x) + A(1-t(x))$$

其中，$I(x)$表示接收到的图像，$J(x)$是期望的复原结果。$t(x)$是光线的传播图，而$A$表示环境背景光。更进一步表示，$t(x) = e^{-\beta d(x)}$，其中$\beta$表示光在水中的衰减系数。

通过以上物理模型，可以推导出期望的复原结果的表达式：

$$J(x) = \frac{1}{t(x)}I(x) - A(\frac{1}{t(x)}-1)$$

若使用传统方法估计物理参数，可能有参数复杂、鲁棒性不足等问题。因此文章使用学习的方式估计模型的参数以提高准确性。

![img](https://raw.githubusercontent.com/hongjr03/img/main/cong3-3286263-large.gif)

##### 衰减系数

分别将原图像的$\{r,g,b\}$三个通道进行两次$3 \times 3$的卷积并池化，然后经过线性回归-ReLU激活-线性回归的过程得到各个通道的衰减系数$\beta ^c$。将三通道的张量$\beta ^ c$进行通道拼接即可得到衰减系数$\beta$。

##### 深度估计

<img src="https://raw.githubusercontent.com/hongjr03/img/main/image-20231204154208091.png" alt="image-20231204154208091" style="zoom:33%;" />

在这里用到了一个[2020年提出的RBD模块](https://doi.org/10.15607/rss.2020.xvi.018)，其中这种密集的跳接结构有助于分层特征的学习。在RBD模块前后分别经过一次和两次卷积、使用Sigmoid函数激活即可得到深度图张量$d_1$。

由$t(x) = e^{-\beta d(x)}$，将深度图和衰减系数进行点积即可估计光线的传播图。得到传播图后再对其求反函数得到深度图$d_2 = -\frac{\ln t}{\beta}$。使用GT计算得的深度图约束$d_1$和$d_2$进而得到更精确的深度图。

参数估计后，即可计算得经过物理模型的复原图像$J^\prime$。

#### 2) 双流交互增强子网络（TSIE-subnet）

![img](https://raw.githubusercontent.com/hongjr03/img/main/cong4-3286263-large.gif)

在 1) 中仍未解决的是背景光$A$的影响。所以为了进一步增强水下图像，作者提出了使用一个输入原图像$I$和初步增强图像$J^\prime$的双流交互增强子网络。首先使用一系列的卷积残差块进行不同规模的特征的提取。对于不同层级的特征，再分别输入到退化量化（DQ）模块中。

原图像和初步增强图像的差$e^t_k - e^m_k$可以初步量化图像上某一些区域的退化程度，因此DQ中的其中一个结果$\text{dif}_k$通过作差、卷积来衡量这种彩色图像退化中的图像特征退化程度。这个值越大，表示该区域需要复原的程度更大。

而在光线传播过程中的衰减同样影响着图像质量。因此作者对第一步中预测的光线传播图进行最大值池化，进而得出一个结果$t_k$，这个值越大表示在这个区域的严重衰减越多，因此在这个区域的信息的解码（decoding）需要强化更多。

如此操作，在最后提取的特征中跳接地加入这部分权重，

$$\hat{e_k} = e^t_k + e^t_k \otimes w_k $$

其中$w_k$即$\text{dif}_k + t_k$经过卷积后的变换特征。这样做能够让网络后面的增强中更注重图像严重退化的区域的复原。

### B. 双鉴别器（Dual-D）

在基于GAN的水下图像增强方法中，负责水下图像增强的是生成器，而鉴别器负责的是判断生成图像的真伪。如果被鉴别出是假的（即增强不足），则继续送回生成器进行训练。因此，这个过程可以由一个对抗性损失约束：

![image-20231204165500470](https://raw.githubusercontent.com/hongjr03/img/main/image-20231204165500470.png)

作者提出的双鉴别器中，第一个鉴别器仅用来鉴别增强后的图像全局是不是像一张高质量的水下图像，而不注重局部区域的增强效果。也就是说，能通过第一次鉴别的图像可能存在一些过增强或者欠增强的区域。作者指出，人类视觉在关注水下图像的时候实际上有一些更偏好的区域，比如前景区域，而如果不使用分区域的方式进行增强显然不符合人类视觉美学。所以第二个鉴别器用于注重不同深度的结构，进而约束A中的TSIE-subnet，更好地鉴别增强结果。这两个鉴别器相互补充，以实现对增强结果的风格和内容的约束。

也正是因为对局部区域不同增强程度的关注，作者更倾向选择将图像分为几个块而非关注图像全局的鉴别器，以此观察局部区域不同的增强程度，而不是注重于图像全局的模糊、色彩平衡和对比度等等。因此作者选用马尔可夫PatchGAN作为鉴别器，对图像中各个$N \times N$的块进行鉴别。
![image-20231204170550294](https://raw.githubusercontent.com/hongjr03/img/main/image-20231204170550294.png)

### C. 训练策略和损失函数

#### Par-subnet的训练

交替训练两个参数。

![image-20231204170848035](https://raw.githubusercontent.com/hongjr03/img/main/image-20231204170848035.png)

#### PUGAN的训练

![image-20231204170939645](https://raw.githubusercontent.com/hongjr03/img/main/image-20231204170939645.png)

$L_1$是全局相似度损失，$L_{gdl}$是感知损失。
