---
layout: post
title: "离散数学Ⅱ 2023秋"
slug: "2023/DM"
date: 2024-01-15
tags: [Computer Science, Course]
categories:
    - 课程
image: cover.png
description: "图论，供复习"
hidden: true
---

## 第七章 图

### 邻域、顶点的度

- 邻域$N_G(v)$：与$v$相邻的**点**的集合，$N_G(v)=\lbrace u|u\in V(G) \land (u,v)\in E(G) \land u \ne v \rbrace$

> 从定义来看，有自环的点的邻域不算自身
>
- 关联集$I_G(v)$：与$v$关联的**边**的集合，$I_G(v)=\lbrace e|e与v关联\rbrace$
- 度$d_G(v)$：与$v$关联的边的次数之和
- 出度、入度

> 注意：**环**对于结点度的贡献是2，**孤立顶点**的度为0
>
- 最大度$\Delta(G)$，$\Delta$；最小度$\delta(G)$，$\delta$

### 周长与围长

- 周长$c(G)$：含圈的无向简单图$G$中，$G$中最长圈的长度。
- 围长$g(G)$：$G$中最短圈的长度。

### 握手定理

1. 每个图中，结点度数的总和等于边数的两倍。

    $$
    \sum_{v \in V}\deg(v) = 2|E|
    $$

2. 有向图中，出度和与入度和相等且等于边数。

    $$
    \sum_{i=1}^nd^+(v_i)=\sum_{i=1}^nd^-(v_i)=m
    $$

3. **推论**：在任何图中，奇数度顶点的个数是偶数。

### 简单图、零图、$k$-正则图

- 简单图：无环、无平行边
- 零图：无边

> **辨别**：平凡图：$1$阶零图；空图：无点

- $k$-正则图：无向简单图且各个点的度数均为$k$

### 可简单图化、同构

前提：可图化（度数总和为偶数）

- Havel定理：每次删去图上度数最大的一个点，若删去后的图仍为可图化的且未出现负数度顶点，则继续，否则不可简单图化。
- Erdös定理：
  
    ![](imgs/Untitled.png)

- 同构：

> Havel定理中能选择点的时候就可以出不同构的图了吧。
>

### 极大路径

原路径$\Gamma_l$使用扩大路径法扩大后的极大路径$\Gamma_{l+k}$的两个端点不与路径外的顶点相邻。

![](imgs/1.png)

![](imgs/2.png)

### 割点、桥、点连通度、边连通度

- 割点：$v是割点 \Leftrightarrow \lbrace v\rbrace是点割集$
- 桥：$(u,v)是桥 \Leftrightarrow \lbrace (u,v)\rbrace是边割集$
- 点连通度$\kappa$：最小的点割集中的点数，规定$\kappa(K_n)=n-1$，而如果$G$非连通，则$\kappa(G)=0$。
- 边连通度$\lambda$：最小的边割集中的边数，如果$G$非连通，则$\lambda(G)=0$。
- Whitney定理：$\kappa \le \lambda \le \delta$

### 二部图、完全二部图

- 二部图：（定理7.8）一个无向图$G＝<V,E>$是二部图当且仅当$G$中无奇圈。
- 完全二部图：完全二部图 $K_{n,n}$的周长是$2n$，围长是$4$。

### 重点习题

[邻域、顶点的度](#邻域顶点的度) [握手定理](#握手定理)

![](imgs/3.png)

由边数得总度数为$2 \times 16 =32$，$32 - 3\times4-4\times3=8$，而其余顶点度数均小于$3$，则至少还有$8\div(3-1)=4$个顶点。图$G$中至少有$4+3+4=11$个顶点。

![](imgs/4.png)

对多面体建模，设图共有$n$个面，各个面编号为$1,2,...,n$。令图$G=<V,W>$，其中点集$V=\lbrace v_i|i是一个面\rbrace$，边集$E=\lbrace (v_i,v_j)|i与j有公共棱\rbrace$。则多面体编号为$i$的面的棱的数量就是点$v_i$的度数$d(v_i)$。如果空间中存在有奇数个面且每个面均有奇数条棱的多面体，则$G$的总度数$\sum_{i=1}^nd(v_i)$为奇数。由握手定理，总度数必为偶数，则空间中不可能存在有奇数个面且每个面均有奇数条棱的多面体。

![](imgs/5.png)

![](imgs/6.png)

设共有$n$个选手，若下过则连边。如果找不到两名下过盘数是相同的选手，说明所有选手的盘数各不相同。而一个选手至多下$n-1$盘，至少下$1$盘，此时至多只能满足$n-1$个选手的盘数各不相同。因此总能找到两名选手下过的盘数是相同的。

![](imgs/7.png)

若$G$中两个奇数度顶点不连通，则$G$中的两个奇数度顶点分属两个连通分支$G_1$和$G_2$。此时$G_1$或$G_2$中都只有一个奇数度顶点，度数和为奇数，矛盾。

[可简单图化、同构](#可简单图化同构)

![](imgs/8.png)

![](imgs/9.png)

[极大路径](#极大路径)

![](imgs/10.png)

![](imgs/11.png)

![](imgs/12.png)

## 第八章 欧拉图与哈密顿图

### 欧拉图

- 欧拉回路：通过所有边一次的**简单**回路
- 欧拉路，欧拉通路：通过所有边一次的**简单**通路
- 欧拉图：有欧拉回路的图
- 半欧拉图：有欧拉通路，无欧拉回路的图

> 规定平凡图为欧拉图 [**辨别**：平凡图：$1$阶零图；空图：无点](#简单图零图-正则图)
环不影响图的欧拉性
>
- 定理1：设$G$是无向连通图，则
  
    $$
    G是欧拉图 \\
    \Leftrightarrow G中所有顶点都是偶数度 \\
    \Leftrightarrow G是若干个边不交的圈的并
    $$

- 定理2：设$G$是无向连通图，则
  
    $$
    G是半欧拉图 \\
    \Leftrightarrow G中恰有2个奇度顶点
    $$

- 定理3：设$G$是**有**向连通图，则

$$
G是欧拉图 \\
\Leftrightarrow \forall v \in V(G), d^+(v)=d^-(v) \\
\Leftrightarrow G是若干个边不交的有向圈的并
$$

- 定理4：设$G$是有向连通图，则

$$
G是半欧拉图 \\
\Leftrightarrow G中恰有2个奇度顶点，其中1个入度比出度大1，另1个出度比入度大1，其余顶点入度等于出度
$$

### 哈密顿图

- 哈密顿回路：经过所有顶点一次的**初级**回路
- 哈密顿通路：经过所有顶点一次的**初级**通路

> 初级意味着点不重
>
- 哈密顿图：有哈密顿回路的图
- 半哈密顿图：有哈密顿通路，无哈密顿回路的图

> 平凡图是哈密顿图
>
- 定理6（哈密顿图性质）：设无向图$G$是哈密顿图，则对任意$V_1 \in V$有$p(G-V_1) \le |V_1|$。
- 定理6推论：设无向图$G$是**半**哈密顿图，则对任意$V_1 \in V$有$p(G-V_1) \le |V_1| +1$。

> 挺好理解的，哈密顿回路就是一个大圈，拿掉一个点就可以变成哈密顿通路
>
- 定理7：若$n(\ge2)$阶无向简单图$G$中，任意不相邻的顶点$u$和$v$有$d(u)+d(v) \ge n-1$，则$G$中存在哈密顿通路。
  
    ![](imgs/13.png)

- 定理7推论（哈密顿图判定）：若$n(\ge3)$阶无向简单图$G$中，任意不相邻的顶点$u$和$v$有$d(u)+d(v) \ge n$，则$G$是哈密顿图。

### 完全图$K_n$中边不重的哈密顿回路的数量

- 定理11：完全图$K_{2k+1}(k\ge1)$中含$k$条边不重的哈密顿回路，且这些回路中含$K_{2k+1}$中的全部边。
- 定理11推论：完全图$K_{2k}(k\ge2)$中含$k-1$条边不重的哈密顿回路，删除这些边之后得到的是一个匹配。

### 重点习题

[欧拉图](#欧拉图)

![](imgs/14.png)

欧拉图是若干个边不交的圈的并，至少拿掉2条边才能破坏其连通性。

[哈密顿图](#哈密顿图)

![](imgs/15.png)

用这个定理[定理6（哈密顿图性质）：设无向图$G$是哈密顿图，则对任意$V_1 \in V$有$p(G-V_1) \le |V_1|$。](#哈密顿图) ，对于$(a)$拿掉$5$个$4$度顶点，此时产生了$7$个连通分支，不满足定理6。

![](imgs/16.png)

![](imgs/17.png)

![](imgs/18.png)

![](imgs/19.png)

构造哈密顿回路，保证每个点都能有边。

![](imgs/20.png)

![](imgs/21.png)

![](imgs/22.png)

![](imgs/23.png)

## 第九章 树

### 树

- 定理1（六个等价定义）：
  
    $$
    G是树（G连通且无回路）\\
    \Leftrightarrow G中任二顶点之间存在唯一路径 \\
    \Leftrightarrow G中无圈，m=n-1 \\
    \Leftrightarrow G连通，m=n-1 \\
    \Leftrightarrow G连通，每条边均为桥 \\
    \Leftrightarrow G中无圈，但在任二不同顶点之间增加新边可构造唯一的圈
    $$

### 生成树

- 生成子图：包含所有顶点的子图

> 导出子图：选边，再往点集加关联的点
>
- 生成树$T$：$T$是$G$的生成子图且是树。
- 定理3（存在定理）：无向图$G$具有生成树当且仅当$G$是连通的。
- 生成树的求法：
  - 破圈法：若图中无圈，则图本身就是生成树。否则删去圈上的任一条边，这不破坏连通性，重复进行直到无圈为止，剩下的图是一棵生成树。

  - 定理6（求生成树的个数$\tau(G)$）：$\tau(G)=\tau(G-e)+\tau(G\setminus e)$

  - 定理3：记$M^\prime_f$是$M_f(G)$中任意$n-1$列组成的方阵，则$T是G的生成树 \Leftrightarrow M^\prime_f的行列式|M^\prime_f|\ne0$。
        1. 忽略环，求关联矩阵
        2. 任选参考点，求基本关联矩阵
        3. 求所有$n-1$阶子方阵，计算行列式，行列式非$0$的是生成树

### 基本回路系统、环路空间

1. 通过弦确定基本回路，给出基本回路系统
2. 利用基本回路系统中的元素进行环合运算（参与运算的元素从$0$个开始到所有元素一起参与）

- 维数：$m-n+1$

![](imgs/24.png)

### 基本割集系统、断集空间

> 割集是断集，断集不一定是割集（可满足连通分支数目增加，但不一定满足极小性）
>
1. 通过树枝确定割集，给出基本割集系统
2. 利用基本割集系统中的元素进行对称差运算

- 维数：$n-1$

![](imgs/25.png)

### 根树

- 根树是有向树，其为平凡树或只有一个顶点入度为$0$而其他顶点的入度均为$1$。
- 顶点$v$的层数$L(v)$：从树根到$v$路径长度
- 树高$h(T)$：顶点的最大层数

### $r$叉树

- $r$叉树：$\forall v \in V(T), d^+(v)\le r$
- 正则$r$叉树：每个分支点恰好有$r$个儿子
- 完全正则$r$叉树：树叶的层数均为树高的$r$叉正则树
- 定理14.13：设正则$r(≥2)$叉树$T$有$i$个分支点和$t$个树叶，则$(r-1)i=t-1$。
  - 证明：握手定理，有向图的出度和和入度和相等且等于边数。

### 重点习题

[树](#树) [根树](#根树)

![](imgs/26.png)

$$
\sum d = 9+3\times3+(n-9-3)\times4 =2\times(n-1) \\
\Rightarrow n =14, n-9-3=2 \\
$$

![](imgs/27.png)

![](imgs/28.png)

![](imgs/29.png)

![](imgs/30.png)

![](imgs/31.png)

![](imgs/32.png)

[生成树](#生成树)

![](imgs/33.png)

[基本回路系统、环路空间](#基本回路系统环路空间) [基本割集系统、断集空间](#基本割集系统断集空间)

![](imgs/34.png)

![](imgs/35.png)

[$r$叉树](#r叉树)

![](imgs/36.png)

## 第十章 图的矩阵表示

### 关联矩阵

行为点，列为边。

- 有向图：
  
    ![](imgs/37.png)

- 无向图：
  
    ![](imgs/38.png)

- 性质：
  - 平行边对应的列相同
  - 不能表示自环
  - 无向图关联矩阵中，每行所有$1$对应的边构成断集
- 基本关联矩阵：选取一个参考点，从关联矩阵$M(G)$中删除参考点对应的行，记作$M_f(G)$。

### 邻接矩阵、相邻矩阵

可以表示环，$a_{ij}$表示从$v_i$到$v_j$的边数。

- 有向图邻接矩阵：
  
    ![](imgs/39.png)

  - 每行和为出度，每列和为入度，主对角元表示环
- 邻接矩阵求通路数：$a_{it} \cdot a_{tj}$即$v_i$经过$v_t$到$v_j$的边数
  
    ![](imgs/40.png)

    ![](imgs/41.png)

    ![](imgs/42.png)

    ![](imgs/43.png)

  - 对每个非$0$元和主对角元置$1$，即可得到可达矩阵。
- 无向图相邻矩阵：是对称矩阵。
  - 使用相邻矩阵求通路数、连通矩阵，和邻接矩阵相似。

### 重点习题

![](imgs/44.png)

![](imgs/45.png)

使用基本关联矩阵法。

![](imgs/46.png)

## 第十一章 平面图

### 平面图

- 可平面图/平面图：可以画在平面上，使得边与边不在非顶点处相交的图
- 平面嵌入（平面表示）：画在平面上使得边与边不在非顶点处相交的图

> 可平面图主要表明图具有平面性质，平面嵌入是平面图的一种表示形式，平面图的平面嵌入不唯一
>
- 性质
  - 定理2：所有面的次数和为边数的两倍。（其实就是握手定理）
  - 定理8、定理9：

      ![](imgs/47.png)

  - 定理10：

      ![](imgs/48.png)

  - 定理11：

      ![](imgs/49.png)

  - 定理12：简单平面图$G$，$\delta(G)\le5$。
- Kuratowski定理（平面图判定定理）
  
    $$
    图G是平面图 \Leftrightarrow G没有与K_5同胚的子图，也没有与K_{3,3}同胚的子图 \\
    \Leftrightarrow G没有可以边收缩到K_5的子图，也没有可以边收缩到K_{3,3}的子图
    $$

    例题：

    ![](imgs/50.png)

    ![](imgs/51.png)

    > 这里为什么可以删除边呢？因为定理要求的是没有和$K_{3,3}$或$K_5$同胚的**子图**。

    ![](imgs/52.png)

    ![](imgs/53.png)

### 极大平面图

- 极大平面图：是平面图，但在任意两个不相邻顶点之间加边就是非平面图。
- 性质：
  - 一定连通，否则不极大
  - 不含有割点和桥。如果含有割点，则一定可以加边。
  - 定理4：$n(≥3)阶简单连通平面图是极大平面图\Leftrightarrow\forall R, \deg(R)=3$

      ![](imgs/54.png)

      ![](imgs/55.png)

      ![](imgs/56.png)

### 欧拉公式

- 欧拉公式：$n-m+r=2$
- 推广：$n-m+r=p+1$

### 对偶图、自对偶图

1. 在$G$的每个面内取一个点作为顶点
2. 如果两个面有公共边，则将两个面取的顶点过公共边连边。如果是悬挂边，那就是自环。

- 性质：
  - 无论原图如何，对偶图$G^\ast$是连通的。

    > 很好理解，因为一定有一个外部面的点
    >
  - 定理15：

    $$
    n^\ast = r \newline
    m^\ast = m \newline
    r^\ast = n \newline
    d_{G^\ast}(v^\ast)=\deg(R)
    $$

  - 定理16同欧拉公式推广。
- 自对偶图：$G \cong G^\ast$
- 轮图$W_n$：阶数$n$，外圈$C_{n-1}$
  - $n \ge4$时，轮图$W_n$是自对偶图

    ![](imgs/57.png)

### 重点习题

[平面图](#平面图)

![](imgs/58.png)

只需构造其平面嵌入。

> 证明**非**平面图时需要用[Kuratowski定理（平面图判定定理）](#平面图) 。
>

![](imgs/59.png)

![](imgs/60.png)

[欧拉公式](#欧拉公式) [对偶图、自对偶图](#对偶图自对偶图)

![](imgs/61.png)

![](imgs/62.png)

平面图有$n-m+r=2$。

（$\Rightarrow$）对偶图$G^\ast$为欧拉图，则所有点度数为偶数，由对偶图性质得$G$中每个面的次数均为偶数。

（$\Leftarrow$）差不多这么证明吧。

## 第十二章 图的着色

### 色数

- 点色数$\chi(G)$
  - 特殊的点色数

    ![](imgs/63.png)

  - 定理5：$\chi(G) \le \Delta(G) +1$

    > 很好理解，这里的$+1$就是$d(v_k)=\Delta(G)$的那个点。
    >
  - 定理6（Brooks定理）：若连通图$G$不是完全图$K_n(n\ge3)$，也不是奇圈，则$\chi(G)\le\Delta(G)$。

    > 相邻点中有两个点着同色，只要这两点不相邻就行，不是完全图保证了这一点。而如果是奇圈，$d=2$，但$\chi=3$。
    >
- 边色数$\chi^\prime(G)$
  - 特殊的边色数
    - 二部图$G$，$\chi^\prime(G)=\Delta(G)$
    - 完全图$K_n$：$n>1$时，$\chi^\prime(K_n)=\begin{cases}
        n,n为奇数 \\
        n-1,n为偶数
        \end{cases}$
  - 定理17（Vizing定理）：$\Delta(G) \le \chi^\prime(G) \le \Delta(G)+1$
- 面色数$\chi^\ast(G)$
  - 定理13、14：研究平面图面着色⇔研究平面图点着色

### 五色定理

- 定理15：任何平面图都可$6$-着色。
  
    ![](imgs/64.png)

- 定理16：任何平面图都可$5$-着色。
  
    ![](imgs/65.png)

    ![](imgs/66.png)

    ![](imgs/67.png)

### 色数多项式

- 递推公式：

$$
f(G,k)=f(G \cup e,k)+f(G\setminus e,k), e \not\in E \newline
f(G,k)=f(G-e,k)-f(G\setminus e,k), e\in E
$$

> 加边约束了着色条件，所以加上收缩边的色数；减边放宽了着色条件，所以减去收缩边的色数。
>
- 特殊的色数多项式
  - 零图：$f(N_n,k)=k^n$

    > 各个点相互独立
    >
  - 完全图：$\begin{matrix}
    f(K_n,k)=\prod_{i=0}^{n-1}(k-i)
    \\
    f(K_n,k)=f(K_{n-1},k)(k-(n-1))
    \end{matrix}$

    > 每加入一个点，就用掉一种颜色
    >
  - 树：$f(T,k)=k(k-1)^{n-1}$

    > 树根有$k$种着色方式，对于每个后代只需要选择和父节点不同的颜色就可以，所以除了树根之外的$k-1$个顶点各有$n-1$种着色方式。
    >
  - 圈：$f(C_n,k)=(k-1)^n+(-1)^n(k-1)$

![](imgs/69.png)

### 应用

![](imgs/70.png)

![](imgs/71.png)

![](imgs/72.png)

- 同色边构成“边独立集”, 或“匹配”

![](imgs/73.png)

![](imgs/74.png)

![](imgs/75.png)

![](imgs/76.png)

### 重点习题

[色数](#色数) [色数多项式](#色数多项式)

![](imgs/77.png)

![](imgs/78.png)

![](imgs/79.png)

![](imgs/80.png)

![](imgs/81.png)

![](imgs/82.png)

[应用](#应用)

![](imgs/83.png)

![](imgs/84.png)

## 第十三章 支配集、覆盖集、独立集与匹配集

### 支配集

- 支配集：是点集$V$的子集，满足任何在$V-V^\ast$中的点能和$V^\ast$中的某点相邻。
- 极小支配集：真子集都不是支配集的支配集。
- 最小支配集：$|V^\ast|$最小的支配集
- 支配数$\gamma_0$：$\gamma_0(G)=|V^\ast|$，$V^\ast$是最小支配集
- 定理1：无向图$G$无孤立点，如果$V_1^\ast$是极小支配集，则在$V-V_1^\ast$中存在$V_2^\ast$也是极小支配集。

### 点覆盖

- 点覆盖：$\forall e \in E, \exist v \in V^\ast, v关联e$
- 极小点覆盖：真子集都不是点覆盖的点覆盖。
- 最小点覆盖：$|V^\ast|$最小的点覆盖
- 点覆盖数$\alpha_0$：$\alpha_0(G)=|V^\ast|$，$|V^\ast|$是最小点覆盖

### 边覆盖

- 边覆盖：$\forall v \in V, \exist e \in E^\ast, e关联v$
- 极小边覆盖：真子集都不是边覆盖的边覆盖。
- 最小边覆盖：$|E^\ast|$最小的边覆盖
- 点覆盖数$\alpha_1$：$\alpha_1(G)=|E^\ast|$，$|E^\ast|$是最小边覆盖

### 独立集

- 独立集：独立集中任意两点之间没有边
- 极大独立集：真母集都不是独立集的独立集
- 最大独立集：$|V^\ast|$最大的独立集
- 独立数$\beta_0$：$\beta_0(G)=|V^\ast|$$|V^\ast|$是最大独立集
- 定理2：无向图$G$无孤立点，则图中的极大独立集是极小支配集。

> **逆命题不成立**
>
>
> ![](imgs/85.png)
>
- 定理3：无向图$G$无孤立点，$V^\ast是点覆盖 \Leftrightarrow V-V^\ast是独立集$。
- 定理3推论：$V^\ast是极(最)小点覆盖 \Leftrightarrow V-V^\ast是极(最)大独立集$，$\alpha_0+\beta_0=n$
  
    ![](imgs/86.png)

### 匹配（边独立集）

- 匹配数$\beta_1$：$\beta_1(G)=|E^\ast|$，$E^\ast$是最大匹配

![](imgs/87.png)

- 最大匹配：
  - 定理9（Berge定理）：$M是G中最大匹配⇔G中无M可增广路径$
- 完美匹配：没有非饱和点的匹配。
  - 定理10（Tutte定理）：$G有完美匹配
    ⇔ ∀V^\prime⊂V(G), p_奇(G-V^\prime)≤|V^\prime|$，$p_奇$是奇数阶连通分支数
- 完备匹配：二部图小的那一部分均为饱和点的匹配。
  - 定理11（Hall条件）：$|V_1|≤|V_2|, ∀S⊆V_1, |S|≤|N(S)|$，相异性条件
  - 定理12（t条件）：设$G=<V_1, V_2, E>$是二部图，若$V_1$中每个顶点至少关联$t(t≥1)$条边，而$V_2$中每个顶点至多关联$t$条边，则$G$中存在完备匹配。
  - $t条件⇒ ∃完备匹配 ⇔ \text{Hall}条件$

    ![](imgs/88.png)

### 团

- 团：$G[V^\ast]$是完全子图
- 极大团：$V^\ast$是团，其真母集都不是
- 最大团：$|V^\ast|$最大的团
- 团数：$\nu_0(G)=|V^\ast|$，$V^\ast$是最大团

### 运算律（不知道有没有用）

![](imgs/89.png)

### 重点习题

![](imgs/90.png)

![](imgs/91.png)

![](imgs/92.png)