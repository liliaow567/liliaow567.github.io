# GSL 系列 6 — 线性代数 5 — 完全正交分解


<!--more-->


## 1 写在前面

若无特别说明，本篇代码均来自头文件 `gsl_linalg.h`

## 2 完全正交分解

完全正交分解可以看作是QR分解的推广，对于矩阵 $A$ ($M\times N$)，有如下分解：
$$A P=Q\left(\begin{array}{cc}R_{11} & 0 \\ 0 & 0\end{array}\right) Z^{T}$$
其中：

$P$：转置矩阵，$N\times N$

$Q$：正交矩阵，$M\times M$

$R_{11}$：上三角矩阵，$r\times r, r=rank(A)$

$Z$：正交矩阵，$N\times N$

如果 $A$ 满秩，$R_{11}=R,Z=I$，也就是带列转置的 QR 分解

对于**不满秩** $A$，其对应的最小二乘解 ($\min_x\|Ax-b\|$) 不唯一，如果进一步加上条件 $\min_x\|x\|$，利用完备正交解，可以得到 $x$

$$x=PZ\left(\begin{array}{c} R_{11}^{-1}c_1\\ 0\end{array}\right)$$

其中 $c_1$ 是 $Q^Tb$ 的前 $r$ 个元素

以吉洪诺夫 (Tikhonov) 正则化的形式表述下列问题

$$\min_x(\|Ax-b\|^2+\lambda^2\|x\|^2)$$

该问题的解为：

$$x=PZ\left(\begin{array}{c} y_1\\ 0\end{array}\right)$$

其中 $y_1$ 长度为 $r$，可以根据下列方程求出：

$$\left(\begin{array}{c} R_{11}\\ \lambda I_r\end{array}\right)y_1=\left(\begin{array}{c} c_1\\ 0\end{array}\right)$$

该方程可以用 QR 分解方法求解

## 3 完全正交分解相关函数

### 完全正交分解

$A=QRZP^T$

$Q,Z$ 一部分存在 `A` 中，另一部分存在 `tau_Q`, `tau_Z` 中

$R_{11}$ 存在 `A` 中，

`rank` 计算方式，参考：{% post_link GSL线性代数3 GSL 系列 6 — 线性代数 3 — QR 分解 false %}

`work` 为工作空间，长度为 $N$

```cpp
int gsl_linalg_COD_decomp(gsl_matrix * A, gsl_vector * tau_Q, gsl_vector * tau_Z,
                          gsl_permutation * p, size_t * rank, gsl_vector * work);
int gsl_linalg_COD_decomp_e(gsl_matrix * A, gsl_vector * tau_Q, gsl_vector * tau_Z,
                            gsl_permutation * p, double tol, size_t * rank, gsl_vector * work);
```
### 计算带正则的最小二乘解

$\min_x(\|Ax-b\|^2+\|x\|^2)$，当 $A$ 满秩时，不考虑正则项

```cpp
int gsl_linalg_COD_lssolve (const gsl_matrix * QRZT, const gsl_vector * tau_Q, const gsl_vector * tau_Z,
                            const gsl_permutation * perm, const size_t rank, const gsl_vector * b,
                            gsl_vector * x, gsl_vector * residual);                        
```
$\min_x(\|Ax-b\|^2+\lambda\|x\|^2)$

工作空间矩阵 `S` 维度为 `rank x rank`，工作空间向量 `work` 维度为 `rank`
```cpp
int
gsl_linalg_COD_lssolve2 (const double lambda, const gsl_matrix * QRZT, const gsl_vector * tau_Q, const gsl_vector * tau_Z,
                         const gsl_permutation * perm, const size_t rank, const gsl_vector * b,
                         gsl_vector * x, gsl_vector * residual, gsl_matrix * S, gsl_vector * work);
```

### 解包

```cpp
int gsl_linalg_COD_unpack(const gsl_matrix * QRZT, const gsl_vector * tau_Q,
                          const gsl_vector * tau_Z, const size_t rank, gsl_matrix * Q,
                          gsl_matrix * R, gsl_matrix * Z);
```
### 计算 $AZ$

$A$ 必须为 $N$ 列，`work` 长度为 $N$

```cpp
int gsl_linalg_COD_matZ(const gsl_matrix * QRZT, const gsl_vector * tau_Z, const size_t rank,
                        gsl_matrix * A, gsl_vector * work);
```
