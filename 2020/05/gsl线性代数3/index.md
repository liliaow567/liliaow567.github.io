# GSL 系列 6 — 线性代数 3 — QR 分解


<!--more-->



## 1 写在前面

关于 QR 分解的背景知识介绍，参见：[QR分解]({{< relref "posts/GSL线性代数1.md#QR" >}})，本篇只说明相关函数

若无特别说明，本篇代码均来自头文件 `gsl_linalg.h`


## 2 QR 分解相关函数

### QR 分解

$A=QR$

**Householder 版:**

将 A 分解为 Q R, Q 分别存储在矩阵 A 的下三角部分 ($v$) 和向量 tau ($\tau$) 中，R 存储在矩阵 A 的上三角部分（含对角）

推荐此方法用于中小型矩阵，或用于 $M<N$ 时
```cpp
int gsl_linalg_QR_decomp (gsl_matrix * A, gsl_vector * tau);
```
**递归版 (带 `_r`)：**

将 A 分解为 Q R, Q 分别存储在矩阵 A 的下三角部分 ($V$) 和矩阵 T ($T$) 中，R 存储在矩阵 A 的上三角部分（含对角）
```cpp
int gsl_linalg_QR_decomp_r (gsl_matrix * A, gsl_matrix * T);
```
此方法要求 $M\ge N$，并且该方法对于“高瘦”型矩阵 ($M\gg N$) 表现更佳

### QR 解包

根据 QR 和 tau (T) 得到矩阵 Q 和矩阵 R
```cpp
int gsl_linalg_QR_unpack(const gsl_matrix * QR, const gsl_vector * tau, gsl_matrix * Q, gsl_matrix * R);
int gsl_linalg_QR_unpack_r(const gsl_matrix * QR, const gsl_matrix * T, gsl_matrix * Q, gsl_matrix * R);
```

### QR 求解线性方程组

1. 根据 QR 分解得到的 QR 和 tau (或 T)，求解线性方程组 $Ax=b$

```cpp
int gsl_linalg_QR_solve (const gsl_matrix * QR, const gsl_vector * tau, const gsl_vector * b, gsl_vector * x);
int gsl_linalg_QR_solve_r (const gsl_matrix * QR, const gsl_matrix * T, const gsl_vector * b, gsl_vector * x);
// 置换版本
int gsl_linalg_QR_svx (const gsl_matrix * QR, const gsl_vector * tau, gsl_vector * x);
```
2. 求解 $Ax=b$ 的最小二乘解（使用欧几里得范数，$A(M>N)$）

	Householder 版还输出残差向量 (residual)

	递归版输出的向量 x 包含两个部分，x 的前 $N$ 个元素为解，后 $M-N$ 个元素的向量范数等于残差范数，递归版还需要一个向量工作空间 (work)，长度为 $N$

```cpp
int gsl_linalg_QR_lssolve (const gsl_matrix * QR, const gsl_vector * tau, const gsl_vector * b, 
                           gsl_vector * x, gsl_vector * residual);
int gsl_linalg_QR_lssolve_r (const gsl_matrix * QR, const gsl_matrix * T, const gsl_vector * b,
                             gsl_vector * x, gsl_vector * work);
```
3. 求解 $Rx=b$

* 使用 QR 矩阵中的 R
```cpp
int gsl_linalg_QR_Rsolve (const gsl_matrix * QR, const gsl_vector * b, gsl_vector * x);
int gsl_linalg_QR_Rsvx (const gsl_matrix * QR, gsl_vector * x);
```
* 直接使用 R (上三角矩阵)
```cpp
int gsl_linalg_R_solve (const gsl_matrix * R, const gsl_vector * b, gsl_vector * x);
int gsl_linalg_R_svx (const gsl_matrix * R, gsl_vector * x);
```

4. 求解 $Rx=Q^Tb$

```cpp
int gsl_linalg_QR_QRsolve (gsl_matrix * Q, gsl_matrix * R, const gsl_vector * b, gsl_vector * x);
```
### 计算其他

1. 计算 $Q^Tv$

	递归版需要一个向量工作空间，长度 $N$
```cpp
int gsl_linalg_QR_QTvec(const gsl_matrix * QR, const gsl_vector * tau, gsl_vector * v);
int gsl_linalg_QR_QTvec_r(const gsl_matrix * QR, const gsl_matrix * T, gsl_vector * b, gsl_vector * work);
```

2. 计算 $Qv$

```cpp
int gsl_linalg_QR_Qvec (const gsl_matrix * QR, const gsl_vector * tau, gsl_vector * v);
```

3. 计算 $Q^TB$，矩阵 $B$ 维度 ($M\times K$)

	递归版需要一个矩阵工作空间，维度为 $N\times K$
```cpp
int gsl_linalg_QR_QTmat(const gsl_matrix * QR, const gsl_vector * tau, gsl_matrix * A);
int gsl_linalg_QR_QTmat_r(const gsl_matrix * QR, const gsl_matrix * T, gsl_matrix * B, gsl_matrix * work);
```
4. QR 分解的秩 1 更新

	$Q^{'} R^{'}=Q(R+wv^T)$
```cpp
int gsl_linalg_QR_update (gsl_matrix * Q, gsl_matrix * R, gsl_vector * w, const gsl_vector * v);
```

5. 计算 $R$ 的倒数范数 (使用 1 范数) 
$$\frac{1}{\|R\|_1\|R^{-1}\|_1}$$

	向量工作空间维度为 $3N$
```cpp
int gsl_linalg_QR_rcond(const gsl_matrix * QR, double * rcond, gsl_vector * work);
```

### 特殊矩阵 QR 分解

背景相关知识参见：[特殊矩阵的QR分解]({{< relref "posts/GSL线性代数1.md#QR1" >}})

将矩阵 $(S,A)^T$ QR 分解，得到矩阵 R, Y, T，矩阵 R 存在矩阵 S 中，矩阵 Y 存在 A 中
```cpp
int gsl_linalg_QR_TR_decomp (gsl_matrix * S, gsl_matrix * A, gsl_matrix * T);
```

### 带列转置的 QR 分解

$QRP^T$ 分解，`norm` 是一个向量工作空间，长度为 $N$

```cpp
// 分解之后 Q 的 v 部分和 R 存在 A 中 
int gsl_linalg_QRPT_decomp (gsl_matrix * A,
                            gsl_vector * tau,
                            gsl_permutation * p,
                            int *signum,
                            gsl_vector * norm);
// 分解之后Q R 分别储在 q r 中                           
int gsl_linalg_QRPT_decomp2 (const gsl_matrix * A, 
                             gsl_matrix * q, gsl_matrix * r, 
                             gsl_vector * tau, 
                             gsl_permutation * p, 
                             int *signum,
                             gsl_vector * norm);                            
```
利用 $QRP^T$ 分解求解 $Ax=b$

```cpp
int gsl_linalg_QRPT_solve (const gsl_matrix * QR,
                           const gsl_vector * tau,
                           const gsl_permutation * p,
                           const gsl_vector * b,
                           gsl_vector * x);
                           
int gsl_linalg_QRPT_svx (const gsl_matrix * QR,
                         const gsl_vector * tau,
                         const gsl_permutation * p,
                         gsl_vector * x);                          
```

利用 $QRP^T$ 分解求 $Ax=b$ 的最小二乘解 ($A$ 满秩，$A(M>N)$)

```cpp
int gsl_linalg_QRPT_lssolve (const gsl_matrix * QR,
                             const gsl_vector * tau,
                             const gsl_permutation * p,
                             const gsl_vector * b,
                             gsl_vector * x,
                             gsl_vector * residual);
```
利用 $QRP^T$ 分解求 $Ax=b$ 的最小二乘解 ($A$ 秩为 `rank`，$A(M>N)$)

```cpp
int gsl_linalg_QRPT_lssolve2 (const gsl_matrix * QR,
                              const gsl_vector * tau,
                              const gsl_permutation * p,
                              const gsl_vector * b,
                              const size_t rank,
                              gsl_vector * x,
                              gsl_vector * residual);
```
求解 $RP^Tx=Q^Tb$

```cpp
int gsl_linalg_QRPT_QRsolve (const gsl_matrix * Q,
                             const gsl_matrix * R,
                             const gsl_permutation * p,
                             const gsl_vector * b,
                             gsl_vector * x);
```

求解 $RP^Tx=b$

```cpp
int gsl_linalg_QRPT_Rsolve (const gsl_matrix * QR,
                             const gsl_permutation * p,
                             const gsl_vector * b,
                             gsl_vector * x);

int gsl_linalg_QRPT_Rsvx (const gsl_matrix * QR,
                           const gsl_permutation * p,
                           gsl_vector * x);
```
$QRP^T$ 分解的秩 1 更新

$$Q^{'}R^{'}=Q(R+wv^TP)$$

```cpp
int gsl_linalg_QRPT_update (gsl_matrix * Q,
                            gsl_matrix * R,
                            const gsl_permutation * p,
                            gsl_vector * u,
                            const gsl_vector * v);
```
$R$ 的倒数范数（使用 1 范数），向量工作空间 `work` 维度为 $3N$

$$\frac{1}{\|R\|_1\|R^{-1}\|_1}$$

```cpp
int gsl_linalg_QRPT_rcond(const gsl_matrix * QR, double * rcond, gsl_vector * work);
```
估计 $R$ 的秩，统计 $R$ 的对角元素绝对值大于 `tol` 的个数，如果 `tol` 为负，使用如下默认值：
$$20(M+N)eps(max(|diag(R)|))$$

```cpp
size_t gsl_linalg_QRPT_rank (const gsl_matrix * QR, const double tol);
```
