---
layout: post
title: "CVPR24 Zero-Reference Low-Light Enhancement via Physical Quadruple Priors"
date: 2024-03-25
tags: [CVPR, LLE, Physics-Guided]
categories:
  - 计算机视觉
---

## 摘要

在弱光环境下，理解光照和减少监督需求是一项重大挑战。目前的方法对训练过程中的数据使用和特定光照的超参数高度敏感，限制了它们处理未知场景的能力。在本文中，我们提出了一种新的零参考低照度增强框架，该框架可仅使用正常光照下的图像进行训练。为此，我们从物理光传递理论中汲取灵感，设计了一种光照不变先验。这个先验值是连接正常光线图像和弱光图像的桥梁。然后，我们开发了一个先验图像框架，在没有弱光数据的情况下进行训练。在测试过程中，该框架能够将我们的光照不变先验恢复到图像中，自动实现弱光增强。在这一框架内，我们利用预训练生成扩散模型来提高模型能力，引入旁路解码器来处理细节失真，并提供一个轻量级版本以提高实用性。广泛的实验证明了我们的框架在各种情况下的优越性，以及良好的可解释性、鲁棒性和效率。

## 贡献点

开发了一个光照不变先验，源于 Kubelka-Munk 理论，将其作为低照度和正常光照图像之间的桥梁。训练时，仅使用正常光照图像，模型从图像分布中学习明亮照明的概念；在测试时，模型会自动提取低照度图像与光照无关的特征，然后转移到正常光照的图像中。这样，无需任何低照度图像、或者与照度有关的超参数，模型就能够在未知场景中进行低照度增强。

总结：

- 我们提出了一种零参考低照度增强模型，该模型利用照度不变先验作为不同照度之间的中介。我们的模型无需依赖任何特定的低照度数据，就能在各种低照度场景中表现出卓越的性能。
- 我们建立了物理四重先验，这是一种新颖的可学习的光照不变先验，源自光传递理论。该先验捕捉到了不同光照条件下成像的本质，使弱光增强摆脱了对参考样本或人为设置的超参数的依赖。
- 我们开发了一种有效的先验 - 图像映射系统，将先验作为控制预训练大规模生成扩散模型的条件。我们引入了一个旁路解码器来解决失真问题，并证明我们的模型可以提炼成一个轻量级版本以用于实际应用。

## 基于物理先验的图像复原

### 可学习的照明不变先验

#### 基于物理的四重先验

从光传递的 Kubelka-Munk 理论出发。给定波长 $\lambda$，图像平面上位于空间位置 $\mathbf{x}$ 的入射光谱能量模型为

$$
E(\lambda,\mathbf{x}) = e(\lambda, \mathbf{x})\left((1-i(\mathbf{x}))^2R_\infty(\lambda,\mathbf{x})+i(\mathbf{x})\right)\text{.}\tag{1}
$$

其中，$e(\lambda, \mathbf{x})$ 表示光源光谱，$i(\mathbf{x})$ 表示镜面反射，$R_\infty(\lambda,\mathbf{x})$ 表示材料反射率。当物体是无光泽的时候，即 $i(\mathbf{x}) \approx 0$，式 (1) 可以简化为

$$
E(\lambda,\mathbf{x}) = e(\lambda, \mathbf{x})R_\infty(\lambda,\mathbf{x})\text{,}\tag{2}
$$

和 Retinex 模型相同。也就是说，Retinex 理论可以看作是 Kubelka-Munk 理论的一个特例。

首先，为了简便起见，我们将一些变量表示为

$$
E^{\lambda} &= \frac {\partial E(\lambda,\mathbf{x})} {\partial \lambda}, \;
R_\infty^{\lambda} = \frac {\partial R_\infty(\lambda,\mathbf{x})} {\partial \lambda}\text{,} \tag{3}
\\
E^{\lambda\lambda} &= \frac {\partial^2 E(\lambda,\mathbf{x})} {\partial \lambda^2}, \;
R_\infty^{\lambda\lambda} = \frac {\partial^2 R_\infty(\lambda,\mathbf{x})} {\partial \lambda^2}\text{.} \tag{4}
$$

直观地，$E$ 表示光谱强度，$E^\lambda$ 表示光谱斜率，$E^{\lambda\lambda}$ 表示光谱曲率。

根据 [^1]，通过简化假设，我们可以从式 (1) 得到一系列不变量。主要思路是，去掉 $i$ 和 $e$ 的影响，只保留 $R_\infty$ 的影响。由于 $R_\infty$ 是材料的固有属性，因此在不同光照条件下，$R_\infty$ 是不变的。因此导出的变量可以表现出光照不变性。

- 假设等能量照明，即 $e(\lambda, \mathbf{x})$ 被简化为与 $\lambda$ 无关的 $e(\mathbf{x})$，那么式 (1) 可以简化为

    $$
    E(\lambda,\mathbf{x}) = \tilde{e}(\mathbf{x})\left((1-i(\mathbf{x}))^2R_\infty(\lambda,\mathbf{x})+i(\mathbf{x})\right)\text{,}\tag{5}
    $$

    将式 (5) 代入 $E^{\lambda}/E^{\lambda\lambda}$ 可得

    $$
    \frac{E^{\lambda}} {E^{\lambda\lambda}} = \frac {\tilde{e}(\mathbf{x})(1-i(\mathbf{x}))^2R_\infty^{\lambda}} {\tilde{e}(\mathbf{x})(1-i(\mathbf{x}))^2R_\infty^{\lambda\lambda}} = \frac{R_\infty^{\lambda}} {R_\infty^{\lambda\lambda}}\text{,}\tag{6}
    $$

    其中照明属性 $i$ 和 $e$ 被消除，只剩下材料属性 $R_\infty$。由于它和光照无关，因此它建立了 $E^{\lambda}/E^{\lambda\lambda}$ 的光照不变性。这样，第一个光照不变量是

    $$
    H = \arctan \left(  {E^{\lambda}} / {E^{\lambda\lambda}} \right) \text{.}\tag{7}
    $$

- 再假设表面是无光泽的，即 $i(\mathbf{x}) \approx 0$，那么式 (1) 可以简化为

    $$
    E(\lambda,\mathbf{x}) = \tilde{e}(\mathbf{x})R_\infty(\lambda,\mathbf{x})\text{,}\tag{8}
    $$

    类似地，我们可以推导出第二个光照不变量

    $$
    C & = \log \left( \frac { (E^{\lambda})^2 + (E^{\lambda\lambda})^2} {E(\lambda,\mathbf{x})^2} \right) \nonumber \\
    & = \log \left( \frac { (R_\infty^{\lambda})^2 + (R_\infty^{\lambda\lambda})^2} {R_\infty(\lambda,\mathbf{x})^2} \right) \text{.}\tag{9}
    $$

- 进一步假设光照均匀，即 $\tilde{e}(\mathbf{x})$ 简化为 $\bar{e}$，那么式 (1) 可以简化为

    $$
    E(\lambda,\mathbf{x}) = \bar{e}R_\infty(\lambda,\mathbf{x})\text{,}\tag{10}
    $$

    类似地，我们可以推导出第三个光照不变量

    $$
    W &= \tan \left( \bigg| \frac {\partial E(\lambda,\mathbf{x})} {\partial \mathbf{x}} \frac 1 {E(\lambda,\mathbf{x})} \bigg| \right) \nonumber \\
    &= \tan \left( \bigg| \frac {\partial R_\infty(\lambda,\mathbf{x})} {\partial \mathbf{x}} \frac 1 {R_\infty(\lambda,\mathbf{x})} \bigg| \right) \text{.}\tag{11}
    $$

Kubelka-Munk 理论对灰度图像很有效，但在色彩的解释上有一定的局限性。上述的三个光照不变式丢失了部分色彩信息。因此对于第四个光照不变式，我们描述了 RGB 三个通道像素值之间的相对关系。

- 假设光照会保持颜色的顺序，我们将 RGB 三个通道的顺序作为基本的光照不变特征，记为 $O$。

#### 通过神经网络学习

我们通过高斯色彩模型[^2]和 CIConv[^3] 来从 RGB 图像中获取先验。首先，我们通过线性映射估计观测到的能量 $\hat{E}$ 和它的导数 $\hat{E}^\lambda$、$\hat{E}^{\lambda\lambda}$

$$
\begin{bmatrix}
  \hat{E}(x,y)\\
  \hat{E}^\lambda(x,y)\\
  \hat{E}^{\lambda\lambda}(x,y)\\
  \end{bmatrix}
  \!=\!
  \mathcal{W}
  \begin{bmatrix}
  R(x,y)\\G(x,y)\\B(x,y)
  \end{bmatrix}\text{,}\tag{12}
$$

其中，$x$ 和 $y$ 分别表示图像中的位置，$\mathcal{W}$ 是 $3\times 3$ 的矩阵。在 [^2] [^3] 中，$\mathcal{W}$ 是手动设计的，而我们用我们的先验到图像框架通过自然图像的分布来学习它。

公式 (11) 中的空间导数 $\partial E / \partial \mathbf{x}$ 是在 x 和 y 方向上计算的，表示为 $\partial E / \partial \mathbf{x}=(E_x, E_y)$ ，其大小由 $|\partial E / \partial \mathbf{x}| = \sqrt{E_x^2 + E_y^2}$ 给出。最终，通过将 $\hat{E}$ 与高斯颜色平滑和尺度为 $\sigma$ 的导数滤波器进行卷积来估计 $E$、$E_x$ 和 $E_y$ 。$\sigma$ 是从输入图像中预测得出的。类似地，$E^{\lambda}$ 是从 $\hat{E}^{\lambda}$ 获得的，$E^{\lambda\lambda}$ 是从 $\hat{E}^{\lambda\lambda}$ 获得的。现在我们可以从输入图像中计算$H$、$C$ 和 $W$。

我们的最后一个光照不变量，即 RGB 通道的顺序，定义为如下三个通道

$$
O(x,y) = \left[ O_R(x,y), O_G(x,y), O_B(x,y) \right]\text{,}\tag{13}
$$

其中，$O_R$ 表示 RGB 图像中 $R$ 通道的顺序，标准化为 $[-1,1]$。$O_G$ 和 $O_B$ 同理。

最后，$H$、$C$、$W$ 和 $O$ 在通道维度上连接在一起，形成我们的物理四元先验。

[^1]: Jan-Mark Geusebroek, Rein van den Boomgaard, Arnold W. M. Smeulders, and Hugo Geerts. Color invariance. IEEE TPAMI, 23(12):1338–1350, 2001.

[^2]: Theo Gevers, Arjan Gijsenij, Joost Van de Weijer, and Jan-Mark Geusebroek. Color in computer vision: Fundamentals and applications. John Wiley & Sons, 2012.

[^3]: Attila Lengyel, Sourav Garg, Michael Milford, and Jan C. van Gemert. Zero-shot domain adaptation with a physics prior. In ICCV, 2021.
