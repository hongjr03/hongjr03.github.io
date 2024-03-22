---
layout: post
title: "CVPR04 Clear Underwater Vision"
date: 2024-03-15
tags: [Underwater Vision]
categories:
    - 计算机视觉
---

> ~~妈呀我说怎么怪怪的原来我一开始把标题写成了 IEEE04~~

{{<hlink "Clear underwater vision" "https://ieeexplore.ieee.org/abstract/document/1315078">}}

## 物理模型

### 光信号模型

#### 直接透射

光信号由直接透射和前向散射两部分组成。当光线从物体射向摄像机时，部分能量会因散射和吸收而损失。到达相机的部分是直接透射。

$$
D = L_{object}e^{-\eta z}
$$

其中，$\eta$ 是光在水中的衰减系数。这里的 $L$ 指的是物体无散射、吸收介质下我们本应观察到的光强。$z$ 是物体到相机的距离。

衰减系数是 $\eta = \alpha + \beta$，其中 $\alpha$ 是吸收系数，$\beta$ 是散射系数。散射系数 $\beta$ 表示无限小的水体向所有方向散射能量的能力。可以对所有固体角度 $\Theta$ 积分得到 $\beta$。

$$
\beta = \int_{\Theta} \beta(\Theta) d\Omega= 2\pi \int_{0}^{\pi} \beta(\theta) \sin(\theta) d\theta
$$

其中，$\theta$ 是相对于光传播方向的散射角，$\beta(\theta)$ 是角散射系数。$\alpha$, $\beta$, $\eta$ 和 $L_{object}$ 都是与波长 $\lambda$ 有关的。

#### 前向散射

前向散射的组成与直接透射类似。不过，它代表的是相对于观察视角的小角度前向散射光。这就造成了图像模糊，其卷积结果为

$$
F = D \ast g_z
$$

其中，$D$ 由[直接透射](#直接透射)得到。$g_z$ 是受距离 $z$ 影响的点扩散函数 (PSF)，也就是说物体越远，模糊核就越大。

有许多模型可以用来给出水下 PSF 的形式。由于 PSF 取决于悬浮在水中的水溶胶，因此模型通常采用各种经验常数作为参数。比如在这篇文献中[^1]的形式是这样的：

$$
g_z = (e^{-\gamma z}-e^{-\eta z}) \mathcal{F}^{-1} \{ G_z \}, \\
\text{其中} G_z = e^{-Kz\omega}
$$

其中，$K>0$ 和 $\gamma>0$ 是经验常数。$\mathcal{F}^{-1}$ 是傅里叶逆变换，$\omega$ 是图像层面的空间频率。滤波器 $G_z$ 是一个低通滤波器，它的频率响应随着距离 $z$ 的增加而减小。这反映了水下图像的模糊程度随着距离的增加而增加。常数 $\gamma$ 被限制为 $|\gamma| \le \eta$。请注意，根据经验和数值模拟 所得到的 PSF 模型在光沿 $z$ 方向传播时并不保存能量。因此，前向散射是一个模糊和衰减的 $D$。

> 思路：PSF

同时考虑直接透射和前向散射，我们可以得到

$$
S = D + F
$$

我们将有效物体辐射度 $L^\text{effective}_\text{object}$ 定义为

<div>$$
L^\text{effective}_\text{object} = L_\text{object} + L_\text{object} \ast g_z
$$</div>

这是 $L$ 物体的模糊版本。结合上述两式，信号 $S$ 可以写成

$$
S = e^{-\eta z}L^\text{effective}_\text{object}
$$

### 后向散射光

后向散射光并非源于我们观察的物体，反之，它是由光源被水体散射后到达相机的光。在综合分析所有照明的影响之前，先分析单个远处光源的影响。这里的光源从相对于观察方向的角度 $r=(\theta,\eta)$照射到物体上，它的光强是 $L_{source}$。光源到物体的距离是 $z_s$。根据[^2]和[^1]，这个光源对后向散射的贡献是：

$$
B(r) = \int_0^z \beta(\theta) I^\text{source}(r) e^{-\eta l} [1-f/(l+l_0)]^2 dl
$$

其中，$f$ 是摄像机的焦距，$l_0$ 是镜头与 underwater housing window 的距离。这个积分考虑了在一定距离 $l$ 处散射到物体表面，然后衰减直到到达相机的情况。它还通过 $f/(l+l_0)$ 考虑了辐照度的几何投影。

上式可以改写成

$$
B(r) = B_\infty(r)(1-e^{-\eta z}),
$$

其中，

$$
B_\infty(r) \equiv \kappa I^\text{source}(r) \beta(\theta)/\eta
$$

表示延伸到水中无限远处的光源的后向散射光。将所有方向的光源的后向散射光加起来，总的后向散射光是

<div>$$
B = \int_{r} B(r) dr = B_\infty(1-e^{-\eta z}),
$$</div>

其中

<div>$$
B_\infty = \int_{r} B_\infty(r) dr
$$</div>

是一个与 $\lambda$ 有关的标量。

以上推导的前提是光线是水平的，此时光照沿光源到物体的方向传播的变化可以忽略。

> 有一说一，看到这感觉就和之前的那篇 APSF 连上了。

{{<inblog "APSF_GAC_MM23">}}

###

[^1]: [B. L. McGlamery, "A computer model for underwater camera system," Proc. SPIE 208, 221-231 (1979).](https://www.spiedigitallibrary.org/conference-proceedings-of-spie/0208/0000/A-Computer-Model-For-Underwater-Camera-Systems/10.1117/12.958279.short)

[^2]: [J. S. Jaffe, "Computer modeling and the design of optimal underwater imaging systems," IEEE J. Oceanic Engin. 15, 101-111 (1990).](https://ieeexplore.ieee.org/abstract/document/100051)
