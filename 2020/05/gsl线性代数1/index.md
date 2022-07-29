# GSL 系列 6 — 线性代数 1 — 背景知识 1


<!--more-->


## 1 写在前面
本篇旨在对线性代数相关的背景知识做一些简要结论性说明，这样做的目的是为了更清楚的了解 `GSL` 线性代数部分的函数是在做什么，因此并不会进行推导性说明。

同时，本篇还对每个线性代数知识点提供一个说明对应 `GSL` 相关函数的链接页面，所以本篇还有对 `GSL` 线性代数部分的相关函数进行导航的功能。


## 2 LU 分解 {#LU}

### 相关符号

置换矩阵：$P$

上三角 (梯形) 矩阵：$U$

下三角 (梯形) 矩阵：$L$

### LU 分解

对于一般矩阵 $A(M\times N)$，有如下分解：
$$PA=LU$$

其中：$P:M\times M;  L:M\times \min(M,N); U: \min(M,N)\times M$; 

$M\ge N$ 时，$L$ 是下单位三角（梯形）矩阵

### 应用

 1. 求解线性方程组 $Ax=b$ 
 2. 求逆 $A^{-1}$ 
 3. 求行列式 $\det(A)$

### 相关函数说明

参见：[LU 分解函数]({{< relref "posts/GSL线性代数2.md" >}})


## 3 QR 分解 {#QR}

### 相关符号

$Q$：正交矩阵

$R$：上三角（梯形）矩阵

$H$：Householder 矩阵

$v$：Householder 向量

$\tau$：Householder 系数

### QR 分解

矩阵 $A$ ($M\times N$) 可以分解为如下形式：
$$A=QR$$
其中：$Q:M\times M\;\;\;\;R:M\times N$

### 应用

一般可以用来求超定方程 ($M>N$) 的最小二乘解

### QR 分解实施方式

这里说明两种，也是 GSL 当中实现的两种。

 1. Householder 变化法
	
	Householder 矩阵是一个正交矩阵，其作用在向量上是一个镜像变换，也就是说通过镜像变换可以将向量映射到坐标轴上，即将向量大部分元素置 0。可以通过多个 Householder 矩阵将矩阵 $A$ 变为上三角（梯形）矩阵

	**注意**：对于此种方法在 GSL 中，$Q$ 矩阵并不是直接存储的，而是通过 Householder 公式转换的方式进行存储，公式如下：
	$$Q=H_k\cdots H_2H_1$$ $$H_i=I-\tau_iv_iv_i^T$$ $$v_i=(0,0,\cdots,1,a_{i+1},a_{i+2},\cdots)$$
	因此，**在此种方式中， GSL 对 $Q$ 矩阵储存如下两个部分：**
	
	* 一个 $\tau$ 向量，$(\tau_1,\tau_2,\cdots,\tau_k)$
	* 每个 $v_i$ 向量，并且只储存后面的 $a$ 部分



2. 递归法（DGEQRF）

	参考文献：[Applying recursion to serial and parallel QR factorization leads to better performance](https://ieeexplore.ieee.org/document/5389143)
	
	**注意：** 同样在此种方法中，$Q$ 也不是直接储存的，按照如下公式进行转换储存：
	$$Q=1-VTV^T$$
	**其储存也分为如下两个部分：**
	- 储存 $V$，$V$ 的列向量就是 $v_i$，储存方式如同 Householder 变化法
	- 储存矩阵 $T$，矩阵 $T$ 的对角向量就是 $\tau$

### 特殊矩阵的 QR 分解 {#QR1}

$$\left(\begin{array}{l}S \\ A\end{array}\right)=Q R$$

$S$ 为 上三角矩阵，维度 $N\times N$；$A$ 为稠密矩阵，维度 $M\times N$ ，此时的 $Q$ 公式如下：

$$Q=I-VTV^T$$ $$V=\left(\begin{array}{l}I \\ Y\end{array}\right)$$

此时，**其储存分为三部分：**

1. $T$ 储存

2. $Y$ 储存，$Y$ 也是稠密矩阵，维度和 $A$ 一样

3. $R$ 储存

### 带列转置的 QR 分解

$$AP=QR$$ $$A=QRP^T$$

### 相关函数说明

参见：[QR 分解函数]({{< relref "posts/GSL线性代数3.md" >}})

## 4 LQ 分解 {#LQ}

### 相关符号

$L$:  下三角（梯形）矩阵

### LQ 分解

$$A=LQ$$

其中：$L:M\times N\;\;\;\;Q:N\times N$

### 应用

LQ 分解一般可以用来求亚定方程 ($M\le N$) 的最小范数解

### 相关函数

 参见：[LQ 分解函数]({{< relref "posts/GSL线性代数4.md" >}})
