# GSL 系列 6 — 线性代数 4 — LQ 分解


<!--more-->


## 1 写在前面
关于 LQ 分解的背景知识介绍，参见：[LQ分解]({{< relref "posts/GSL线性代数1.md#LQ" >}})，本篇只说明相关函数

若无特别说明，本篇代码均来自头文件 `gsl_linalg.h`


## 2 LQ 分解相关函数

### LQ 分解

$A=LQ$
分解得到 L Q, L 存放在 `A` 的下三角 (包含对角线) ，Q 分为两部分，分别存在 `A` 的上三角部分 (不包含对角线) 和向量 `tau` 中
```cpp
int gsl_linalg_LQ_decomp (gsl_matrix * A, gsl_vector * tau);
```

### LQ 解包

```cpp
int gsl_linalg_LQ_unpack (const gsl_matrix * LQ, const gsl_vector * tau, 
			  gsl_matrix * Q, gsl_matrix * L);
```

### 求解线性方程组

$Ax=b$ ($A:M\le N,A=LQ$)
```cpp
int gsl_linalg_LQ_lssolve(const gsl_matrix * LQ, const gsl_vector * tau,
                          const gsl_vector * b, gsl_vector * x, gsl_vector * residual);
```
$x^TL=b^TQ^T$
```cpp
int gsl_linalg_LQ_solve_T (const gsl_matrix * LQ, const gsl_vector * tau, 
			 const gsl_vector * b, gsl_vector * x);
int gsl_linalg_LQ_svx_T (const gsl_matrix * LQ, const gsl_vector * tau, 
                         gsl_vector * x);
int gsl_linalg_LQ_LQsolve (gsl_matrix * Q, gsl_matrix * L, 
			   const gsl_vector * b, gsl_vector * x);                         
```
$x^TA=b^T$($A:M\le N,A=LQ$) 相当于超定方程的 LQ 解法
```cpp
int gsl_linalg_LQ_lssolve_T (const gsl_matrix * LQ, const gsl_vector * tau, 
			   const gsl_vector * b, gsl_vector * x, 
			   gsl_vector * residual);
```
$x^TL=b^T$

```cpp
int gsl_linalg_LQ_Lsolve_T (const gsl_matrix * LQ, const gsl_vector * b, 
			  gsl_vector * x);
int gsl_linalg_LQ_Lsvx_T (const gsl_matrix * LQ, gsl_vector * x);
int gsl_linalg_L_solve_T (const gsl_matrix * L, const gsl_vector * b, 
			gsl_vector * x);
```
### 其他

$Q^Tv$
```cpp
int gsl_linalg_LQ_QTvec(const gsl_matrix * LQ, const gsl_vector * tau, gsl_vector * v);
```
$vQ$

```cpp
int gsl_linalg_LQ_vecQ (const gsl_matrix * LQ, const gsl_vector * tau, 
			gsl_vector * v);
```

$vQ^T$
```cpp
int gsl_linalg_LQ_vecQT (const gsl_matrix * LQ, const gsl_vector * tau, 
			 gsl_vector * v);
```
$L^{'}Q^{'}=(L+vw^T)Q$

```cpp
int gsl_linalg_LQ_update (gsl_matrix * Q, gsl_matrix * R,
			  const gsl_vector * v, gsl_vector * w);
```

## 3 参考
[https://github.com/ludvigak/gsl/blob/master/linalg/lq.c](https://github.com/ludvigak/gsl/blob/master/linalg/lq.c)
