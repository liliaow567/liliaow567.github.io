# GSL 系列 5 — 向量和矩阵 4 — 基本线性代数运算 (BLAS)


<!--more-->

## 1 写在前面
关于向量，矩阵的定义参考

* [向量 (vector)]({{< relref "posts/GSL向量和矩阵2.md" >}})

* [矩阵 (matrix)]({{< relref "posts/GSL向量和矩阵3.md" >}})

若无特别说明，本篇代码均来自头文件 `gsl_blas.h`


## 2 概述
将基本的线代运算分为三个层次：

* 层次1，向量运算，比如 $\alpha x+y$
* 层次2，矩阵和向量的运算，比如 $\alpha Ax+\beta y$
* 层次3，矩阵和矩阵的运算，比如 $\alpha AB+C$

将如下矩阵类型做名字简单的名字标记：

| 名字标记 | 矩阵类型   | 名字标记 | 矩阵类型   |
| -------- | ---------- | -------- | ---------- |
| GE       | 一般矩阵   | GB       | 一般带矩阵 |
| SY       | 对称矩阵   | SB       | 对称带矩阵 |
| SP       | 对称矩阵包 | HE       | 共轭矩阵   |
| HB       | 共轭带矩阵 | HP       | 共轭矩阵包 |
| TR       | 三角矩阵   | TB       | 三角带矩阵 |
| TP       | 三角矩阵包 |          |            |

以下名字标记代表线代运算

| 名字标记 | 矩阵运算               |
| -------- | ---------------------- |
| DOT      | 标量乘 $x^Ty$          |
| AXPY     | 向量加 $\alpha x+y$    |
| MV       | 矩阵向量乘 $Ax$        |
| SV       | 矩阵向量求解 $A^{-1}b$ |
| MM       | 矩阵矩阵乘 $AA$        |
| SM       | 矩阵矩阵求解 $A^{-1}B$ |

以下名字标记代表数据类型

| 名字标记 | 数据类型   |
| -------- | ---------- |
| S        | 单精度实数 |
| D        | 双精度实数 |
| C        | 单精度复数 |
| Z        | 双精度复数 |

## 3 层次 1 运算
$\alpha +x^Ty$

```cpp
int gsl_blas_sdsdot (float alpha,
                     const gsl_vector_float * X,
                     const gsl_vector_float * Y,
                     float * result
                     );
```

$x^Ty$

```cpp
int gsl_blas_dsdot (const gsl_vector_float * X,
                    const gsl_vector_float * Y,
                    double * result
                    );
int gsl_blas_sdot (const gsl_vector_float * X,
                   const gsl_vector_float * Y,
                   float * result
                   );
int gsl_blas_ddot (const gsl_vector * X,
                   const gsl_vector * Y,
                   double * result
                   );
int  gsl_blas_cdotu (const gsl_vector_complex_float * X,
                     const gsl_vector_complex_float * Y,
                     gsl_complex_float * dotu);
int  gsl_blas_zdotu (const gsl_vector_complex * X,
                     const gsl_vector_complex * Y,
                     gsl_complex * dotu);                                        
```
$x^Hy$

```cpp
int  gsl_blas_cdotc (const gsl_vector_complex_float * X,
                     const gsl_vector_complex_float * Y,
                     gsl_complex_float * dotc);
int  gsl_blas_zdotc (const gsl_vector_complex * X,
                     const gsl_vector_complex * Y,
                     gsl_complex * dotc);                     
```

$\|x\|_2$

```cpp
float  gsl_blas_snrm2  (const gsl_vector_float * X);
double gsl_blas_dnrm2  (const gsl_vector * X);
float  gsl_blas_scnrm2 (const gsl_vector_complex_float * X);
double gsl_blas_dznrm2 (const gsl_vector_complex * X);
```

$\sum|x_i|$

```cpp
float  gsl_blas_sasum  (const gsl_vector_float * X);
double gsl_blas_dasum  (const gsl_vector * X);
```

$\sum\left(\left|\Re\left(x_{i}\right)\right|+\left|\Im\left(x_{i}\right)\right|\right)$

```cpp
float  gsl_blas_scasum (const gsl_vector_complex_float * X);
double gsl_blas_dzasum (const gsl_vector_complex * X);
```

$\max_i|x_i|$

```cpp
CBLAS_INDEX_t gsl_blas_isamax (const gsl_vector_float * X);
CBLAS_INDEX_t gsl_blas_idamax (const gsl_vector * X);

// gsl_blas_types.h
typedef  CBLAS_INDEX  CBLAS_INDEX_t;
// gsl_cblas.h
#define CBLAS_INDEX size_t
```

$\max_i(\left|\Re\left(x_{i}\right)\right|+\left|\Im\left(x_{i}\right)\right|)$

```cpp
CBLAS_INDEX_t gsl_blas_icamax (const gsl_vector_complex_float * X);
CBLAS_INDEX_t gsl_blas_izamax (const gsl_vector_complex * X);
```

交换 $x$，$y$

```cpp
int  gsl_blas_sswap (gsl_vector_float * X,
                     gsl_vector_float * Y);
int  gsl_blas_dswap (gsl_vector * X,
                     gsl_vector * Y);
int  gsl_blas_cswap (gsl_vector_complex_float * X,
                     gsl_vector_complex_float * Y);
int  gsl_blas_zswap (gsl_vector_complex * X,
                     gsl_vector_complex * Y);                                                               
```

复制 $x$ 到 $y$

```cpp
int  gsl_blas_scopy (const gsl_vector_float * X,
                     gsl_vector_float * Y);
int  gsl_blas_dcopy (const gsl_vector * X,
                     gsl_vector * Y);
int  gsl_blas_ccopy (const gsl_vector_complex_float * X,
                     gsl_vector_complex_float * Y);   
int  gsl_blas_zcopy (const gsl_vector_complex * X,
                     gsl_vector_complex * Y);                                       
```

$y=\alpha x+y$

```cpp
int  gsl_blas_saxpy (float alpha,
                     const gsl_vector_float * X,
                     gsl_vector_float * Y);
int  gsl_blas_daxpy (double alpha,
                     const gsl_vector * X,
                     gsl_vector * Y);
int  gsl_blas_caxpy (const gsl_complex_float alpha,
                     const gsl_vector_complex_float * X,
                     gsl_vector_complex_float * Y);
int  gsl_blas_zaxpy (const gsl_complex alpha,
                     const gsl_vector_complex * X,
                     gsl_vector_complex * Y);                                                                                    
```

$\alpha x$

```cpp
void gsl_blas_sscal  (float  alpha, gsl_vector_float * X);
void gsl_blas_dscal  (double alpha, gsl_vector * X);
void gsl_blas_cscal  (const gsl_complex_float alpha, gsl_vector_complex_float * X);
void gsl_blas_zscal  (const gsl_complex alpha, gsl_vector_complex * X);
void gsl_blas_csscal (float  alpha, gsl_vector_complex_float * X);
void gsl_blas_zdscal (double alpha, gsl_vector_complex * X);
```

计算满足如下旋转:
$$\left(\begin{array}{cc}c & s \\ -s & c\end{array}\right)\left(\begin{array}{l}a \\ b\end{array}\right)=\left(\begin{array}{l}r^{\prime} \\ 0\end{array}\right)$$
计算得到 `c`, `s`，同时，`a`, `b` 也进行重写
```cpp
int  gsl_blas_srotg (float a[], float b[], float c[], float s[]);
int  gsl_blas_drotg (double a[], double b[], double c[], double s[]);
```
应用旋转，计算 $(x,y) = (cx+sy,-sx+cy)$

```cpp
int  gsl_blas_srot (gsl_vector_float * X,
                    gsl_vector_float * Y,
                    float c, float s);
int  gsl_blas_drot (gsl_vector * X,
                    gsl_vector * Y,
                    const double c, const double s);                    
```
计算修正旋转

```cpp
int  gsl_blas_srotmg (float d1[], float d2[], float b1[], float b2, float P[]);
int  gsl_blas_drotmg (double d1[], double d2[], double b1[],
                      double b2, double P[]);
```

应用修正旋转

```cpp
int  gsl_blas_srotm (gsl_vector_float * X,
                     gsl_vector_float * Y,
                     const float P[]);
int  gsl_blas_drotm (gsl_vector * X,
                     gsl_vector * Y,
                     const double P[]);                     
```

## 4 层次 2 运算
$y=\alpha \operatorname{op}(A)x+\beta y$

当 `TransA` 等于 `CblasNoTrans`，`CblasTrans`，`CblasConjTrans` 时，$\operatorname{op}(A)$ 分别等于 $A$，$A^T$，$A^H$

```cpp
int  gsl_blas_sgemv (CBLAS_TRANSPOSE_t TransA,
                     float alpha,
                     const gsl_matrix_float * A,
                     const gsl_vector_float * X,
                     float beta,
                     gsl_vector_float * Y);
int  gsl_blas_dgemv (CBLAS_TRANSPOSE_t TransA,
                     double alpha,
                     const gsl_matrix * A,
                     const gsl_vector * X,
                     double beta,
                     gsl_vector * Y);
int  gsl_blas_cgemv (CBLAS_TRANSPOSE_t TransA,
                     const gsl_complex_float alpha,
                     const gsl_matrix_complex_float * A,
                     const gsl_vector_complex_float * X,
                     const gsl_complex_float beta,
                     gsl_vector_complex_float * Y);
int  gsl_blas_zgemv (CBLAS_TRANSPOSE_t TransA,
                     const gsl_complex alpha,
                     const gsl_matrix_complex * A,
                     const gsl_vector_complex * X,
                     const gsl_complex beta,
                     gsl_vector_complex * Y);            

// gsl_blas_types.h
typedef  enum CBLAS_TRANSPOSE   CBLAS_TRANSPOSE_t;
// gsl_cblas.h
enum CBLAS_TRANSPOSE {CblasNoTrans=111, CblasTrans=112, CblasConjTrans=113};                                                                        
```

三角矩阵，$\operatorname{op}(A)x$

当 `Uplo` 为 `CblasUpper`，`CblasLower` 时，分别使用 `A` 的上三角，下三角部分

当 `Diag` 为 `CblasNonUnit`，`CblasUnit` 时，分别使用 `A` 的对角线，为 1  的对角线

```cpp
int  gsl_blas_strmv (CBLAS_UPLO_t Uplo,
                     CBLAS_TRANSPOSE_t TransA, CBLAS_DIAG_t Diag,
                     const gsl_matrix_float * A,
                     gsl_vector_float * X);
int  gsl_blas_dtrmv (CBLAS_UPLO_t Uplo,
                     CBLAS_TRANSPOSE_t TransA, CBLAS_DIAG_t Diag,
                     const gsl_matrix * A,
                     gsl_vector * X);
int  gsl_blas_ctrmv (CBLAS_UPLO_t Uplo,
                     CBLAS_TRANSPOSE_t TransA, CBLAS_DIAG_t Diag,
                     const gsl_matrix_complex_float * A,
                     gsl_vector_complex_float * X);
int  gsl_blas_ztrmv (CBLAS_UPLO_t Uplo,
                     CBLAS_TRANSPOSE_t TransA, CBLAS_DIAG_t Diag,
                     const gsl_matrix_complex * A,
                     gsl_vector_complex * X);

// gsl_blas_types.h
typedef  enum CBLAS_UPLO        CBLAS_UPLO_t;
typedef  enum CBLAS_DIAG        CBLAS_DIAG_t;
// gsl_cblas.h                       
enum CBLAS_UPLO {CblasUpper=121, CblasLower=122};
enum CBLAS_DIAG {CblasNonUnit=131, CblasUnit=132};                                                             
```
三角矩阵，$inv(\operatorname{op}(A))x$

```cpp
int  gsl_blas_strsv (CBLAS_UPLO_t Uplo,
                     CBLAS_TRANSPOSE_t TransA, CBLAS_DIAG_t Diag,
                     const gsl_matrix_float * A,
                     gsl_vector_float * X);
int  gsl_blas_dtrsv (CBLAS_UPLO_t Uplo,
                     CBLAS_TRANSPOSE_t TransA, CBLAS_DIAG_t Diag,
                     const gsl_matrix * A,
                     gsl_vector * X);
int  gsl_blas_ctrsv (CBLAS_UPLO_t Uplo,
                     CBLAS_TRANSPOSE_t TransA, CBLAS_DIAG_t Diag,
                     const gsl_matrix_complex_float * A,
                     gsl_vector_complex_float * X);
int  gsl_blas_ztrsv (CBLAS_UPLO_t Uplo,
                     CBLAS_TRANSPOSE_t TransA, CBLAS_DIAG_t Diag,
                     const gsl_matrix_complex * A,
                     gsl_vector_complex *X);                                                               
```

对称矩阵，$y=\alpha Ax+\beta y$

```cpp
int  gsl_blas_ssymv (CBLAS_UPLO_t Uplo,
                     float alpha,
                     const gsl_matrix_float * A,
                     const gsl_vector_float * X,
                     float beta,
                     gsl_vector_float * Y);
int  gsl_blas_dsymv (CBLAS_UPLO_t Uplo,
                     double alpha,
                     const gsl_matrix * A,
                     const gsl_vector * X,
                     double beta,
                     gsl_vector * Y);                     
```

共轭矩阵，$y=\alpha Ax+\beta y$

```cpp
int  gsl_blas_chemv (CBLAS_UPLO_t Uplo,
                     const gsl_complex_float alpha,
                     const gsl_matrix_complex_float * A,
                     const gsl_vector_complex_float * X,
                     const gsl_complex_float beta,
                     gsl_vector_complex_float * Y);
int  gsl_blas_zhemv (CBLAS_UPLO_t Uplo,
                     const gsl_complex alpha,
                     const gsl_matrix_complex * A,
                     const gsl_vector_complex * X,
                     const gsl_complex beta,
                     gsl_vector_complex * Y);                     
```
$A=\alpha xy^T+A$

```cpp
int  gsl_blas_sger (float alpha,
                    const gsl_vector_float * X,
                    const gsl_vector_float * Y,
                    gsl_matrix_float * A);
int  gsl_blas_dger (double alpha,
                    const gsl_vector * X,
                    const gsl_vector * Y,
                    gsl_matrix * A);
int  gsl_blas_cgeru (const gsl_complex_float alpha,
                     const gsl_vector_complex_float * X,
                     const gsl_vector_complex_float * Y,
                     gsl_matrix_complex_float * A);
int  gsl_blas_zgeru (const gsl_complex alpha,
                     const gsl_vector_complex * X,
                     const gsl_vector_complex * Y,
                     gsl_matrix_complex * A);                                                             
```

$A=\alpha xy^H+A$

```cpp
int  gsl_blas_cgerc (const gsl_complex_float alpha,
                     const gsl_vector_complex_float * X,
                     const gsl_vector_complex_float * Y,
                     gsl_matrix_complex_float * A);
int  gsl_blas_zgerc (const gsl_complex alpha,
                     const gsl_vector_complex * X,
                     const gsl_vector_complex * Y,
                     gsl_matrix_complex * A);                     
```
对称矩阵，$A=\alpha xx^T+A$

```cpp
int  gsl_blas_ssyr (CBLAS_UPLO_t Uplo,
                    float alpha,
                    const gsl_vector_float * X,
                    gsl_matrix_float * A);
int  gsl_blas_dsyr (CBLAS_UPLO_t Uplo,
                    double alpha,
                    const gsl_vector * X,
                    gsl_matrix * A);                    
```
共轭矩阵，$A=\alpha xx^H+A$

```cpp
int  gsl_blas_cher (CBLAS_UPLO_t Uplo,
                    float alpha,
                    const gsl_vector_complex_float * X,
                    gsl_matrix_complex_float * A);
int  gsl_blas_zher (CBLAS_UPLO_t Uplo,
                    double alpha,
                    const gsl_vector_complex * X,
                    gsl_matrix_complex * A);                    
```
对称矩阵，$A=\alpha xy^T+\alpha yx^T + A$

```cpp
int  gsl_blas_ssyr2 (CBLAS_UPLO_t Uplo,
                     float alpha,
                     const gsl_vector_float * X,
                     const gsl_vector_float * Y,
                     gsl_matrix_float * A);
int  gsl_blas_dsyr2 (CBLAS_UPLO_t Uplo,
                     double alpha,
                     const gsl_vector * X,
                     const gsl_vector * Y,
                     gsl_matrix * A);                     
```
共轭矩阵，$A=\alpha xy^H+\alpha^{*} yx^H + A$

```cpp
int  gsl_blas_cher2 (CBLAS_UPLO_t Uplo,
                     const gsl_complex_float alpha,
                     const gsl_vector_complex_float * X,
                     const gsl_vector_complex_float * Y,
                     gsl_matrix_complex_float * A);
int  gsl_blas_zher2 (CBLAS_UPLO_t Uplo,
                     const gsl_complex alpha,
                     const gsl_vector_complex * X,
                     const gsl_vector_complex * Y,
                     gsl_matrix_complex * A);
```
## 5 层次 3 运算
$C=\alpha\operatorname{op}(A)\operatorname{op}(B)+\beta C$

```cpp
int  gsl_blas_sgemm (CBLAS_TRANSPOSE_t TransA,
                     CBLAS_TRANSPOSE_t TransB,
                     float alpha,
                     const gsl_matrix_float * A,
                     const gsl_matrix_float * B,
                     float beta,
                     gsl_matrix_float * C);
int  gsl_blas_dgemm (CBLAS_TRANSPOSE_t TransA,
                     CBLAS_TRANSPOSE_t TransB,
                     double alpha,
                     const gsl_matrix * A,
                     const gsl_matrix * B,
                     double beta,
                     gsl_matrix * C);     
int  gsl_blas_cgemm (CBLAS_TRANSPOSE_t TransA,
                     CBLAS_TRANSPOSE_t TransB,
                     const gsl_complex_float alpha,
                     const gsl_matrix_complex_float * A,
                     const gsl_matrix_complex_float * B,
                     const gsl_complex_float beta,
                     gsl_matrix_complex_float * C); 
int  gsl_blas_zgemm (CBLAS_TRANSPOSE_t TransA,
                     CBLAS_TRANSPOSE_t TransB,
                     const gsl_complex alpha,
                     const gsl_matrix_complex * A,
                     const gsl_matrix_complex * B,
                     const gsl_complex beta,
                     gsl_matrix_complex * C);                                                          
```

对称矩阵 $A$；$C=\alpha AB+\beta C$ 或 $C=\alpha BA+\beta C$

当 `Side` 为 `CblasLeft`，`CblasRight` 时，分别计算 $C=\alpha AB+\beta C$，$C=\alpha BA+\beta C$

```cpp
int  gsl_blas_ssymm (CBLAS_SIDE_t Side, CBLAS_UPLO_t Uplo,
                     float alpha,
                     const gsl_matrix_float * A,
                     const gsl_matrix_float * B,
                     float beta,
                     gsl_matrix_float * C);
int  gsl_blas_dsymm (CBLAS_SIDE_t Side,
                     CBLAS_UPLO_t Uplo,
                     double alpha,
                     const gsl_matrix * A,
                     const gsl_matrix * B,
                     double beta,
                     gsl_matrix * C);  
int  gsl_blas_csymm (CBLAS_SIDE_t Side,
                     CBLAS_UPLO_t Uplo,
                     const gsl_complex_float alpha,
                     const gsl_matrix_complex_float * A,
                     const gsl_matrix_complex_float * B,
                     const gsl_complex_float beta,
                     gsl_matrix_complex_float * C);          
int  gsl_blas_zsymm (CBLAS_SIDE_t Side,
                     CBLAS_UPLO_t Uplo,
                     const gsl_complex alpha,
                     const gsl_matrix_complex * A,
                     const gsl_matrix_complex * B,
                     const gsl_complex beta,
                     gsl_matrix_complex * C);      

// gsl_blas_types.h
typedef  enum CBLAS_SIDE        CBLAS_SIDE_t; 
// gsl_cblas.h
enum CBLAS_SIDE {CblasLeft=141, CblasRight=142};                                                                                     
```
共轭矩阵 $A$；$C=\alpha AB+\beta C$ 或 $C=\alpha BA+\beta C$



```cpp
int  gsl_blas_chemm (CBLAS_SIDE_t Side,
                     CBLAS_UPLO_t Uplo,
                     const gsl_complex_float alpha,
                     const gsl_matrix_complex_float * A,
                     const gsl_matrix_complex_float * B,
                     const gsl_complex_float beta,
                     gsl_matrix_complex_float * C);
int  gsl_blas_zhemm (CBLAS_SIDE_t Side,
                     CBLAS_UPLO_t Uplo,
                     const gsl_complex alpha,
                     const gsl_matrix_complex * A,
                     const gsl_matrix_complex * B,
                     const gsl_complex beta,
                     gsl_matrix_complex * C);                     
```
三角矩阵 $A$；$B=\alpha\operatorname{op}(A)B$ 或 $B=\alpha B\operatorname{op}(A)$

```cpp
int  gsl_blas_strmm (CBLAS_SIDE_t Side,
                     CBLAS_UPLO_t Uplo, CBLAS_TRANSPOSE_t TransA,
                     CBLAS_DIAG_t Diag,
                     float alpha,
                     const gsl_matrix_float * A,
                     gsl_matrix_float * B);
int  gsl_blas_dtrmm (CBLAS_SIDE_t Side,
                     CBLAS_UPLO_t Uplo, CBLAS_TRANSPOSE_t TransA,
                     CBLAS_DIAG_t Diag,
                     double alpha,
                     const gsl_matrix * A,
                     gsl_matrix * B);     
int  gsl_blas_ctrmm (CBLAS_SIDE_t Side,
                     CBLAS_UPLO_t Uplo, CBLAS_TRANSPOSE_t TransA,
                     CBLAS_DIAG_t Diag,
                     const gsl_complex_float alpha,
                     const gsl_matrix_complex_float * A,
                     gsl_matrix_complex_float * B);         
int  gsl_blas_ztrmm (CBLAS_SIDE_t Side,
                     CBLAS_UPLO_t Uplo, CBLAS_TRANSPOSE_t TransA,
                     CBLAS_DIAG_t Diag,
                     const gsl_complex alpha,
                     const gsl_matrix_complex * A,
                     gsl_matrix_complex * B);                                                                    
```
三角矩阵 $A$；$B=\alpha\operatorname{op}(inv(A))B$ 或 $B=\alpha B\operatorname{op}(inv(A))$


```cpp
int  gsl_blas_strsm (CBLAS_SIDE_t Side,
                     CBLAS_UPLO_t Uplo, CBLAS_TRANSPOSE_t TransA,
                     CBLAS_DIAG_t Diag,
                     float alpha,
                     const gsl_matrix_float * A,
                     gsl_matrix_float * B);
int  gsl_blas_dtrsm (CBLAS_SIDE_t Side,
                     CBLAS_UPLO_t Uplo, CBLAS_TRANSPOSE_t TransA,
                     CBLAS_DIAG_t Diag,
                     double alpha,
                     const gsl_matrix * A,
                     gsl_matrix * B);     
int  gsl_blas_ctrsm (CBLAS_SIDE_t Side,
                     CBLAS_UPLO_t Uplo, CBLAS_TRANSPOSE_t TransA,
                     CBLAS_DIAG_t Diag,
                     const gsl_complex_float alpha,
                     const gsl_matrix_complex_float * A,
                     gsl_matrix_complex_float * B);     
int  gsl_blas_ztrsm (CBLAS_SIDE_t Side,
                     CBLAS_UPLO_t Uplo, CBLAS_TRANSPOSE_t TransA,
                     CBLAS_DIAG_t Diag,
                     const gsl_complex alpha,
                     const gsl_matrix_complex * A,
                     gsl_matrix_complex * B);                                                     
```
对称矩阵 $C$；$C=\alpha AA^T+\beta C$

```cpp
int  gsl_blas_ssyrk (CBLAS_UPLO_t Uplo, CBLAS_TRANSPOSE_t Trans,
                     float alpha,
                     const gsl_matrix_float * A,
                     float beta,
                     gsl_matrix_float * C);
int  gsl_blas_dsyrk (CBLAS_UPLO_t Uplo,
                     CBLAS_TRANSPOSE_t Trans,
                     double alpha,
                     const gsl_matrix * A,
                     double beta,
                     gsl_matrix * C);    
int  gsl_blas_csyrk (CBLAS_UPLO_t Uplo,
                     CBLAS_TRANSPOSE_t Trans,
                     const gsl_complex_float alpha,
                     const gsl_matrix_complex_float * A,
                     const gsl_complex_float beta,
                     gsl_matrix_complex_float * C);    
int  gsl_blas_zsyrk (CBLAS_UPLO_t Uplo,
                     CBLAS_TRANSPOSE_t Trans,
                     const gsl_complex alpha,
                     const gsl_matrix_complex * A,
                     const gsl_complex beta,
                     gsl_matrix_complex * C);                                                       
```

共轭矩阵 $C$；$C=\alpha AA^H+\beta C$


```cpp
int  gsl_blas_cherk (CBLAS_UPLO_t Uplo,
                     CBLAS_TRANSPOSE_t Trans,
                     float alpha,
                     const gsl_matrix_complex_float * A,
                     float beta,
                     gsl_matrix_complex_float * C);
int  gsl_blas_zherk (CBLAS_UPLO_t Uplo,
                     CBLAS_TRANSPOSE_t Trans,
                     double alpha,
                     const gsl_matrix_complex * A,
                     double beta,
                     gsl_matrix_complex * C);                     
```

对称矩阵 $C$；$C=\alpha A^TB+\alpha B^TA+\beta C$

```cpp
int  gsl_blas_ssyr2k (CBLAS_UPLO_t Uplo, CBLAS_TRANSPOSE_t Trans,
                      float alpha,
                      const gsl_matrix_float * A,
                      const gsl_matrix_float * B,
                      float beta,
                      gsl_matrix_float * C);
int  gsl_blas_dsyr2k (CBLAS_UPLO_t Uplo,
                      CBLAS_TRANSPOSE_t Trans,
                      double alpha,
                      const  gsl_matrix * A,
                      const  gsl_matrix * B,
                      double beta,
                      gsl_matrix * C);           
int  gsl_blas_csyr2k (CBLAS_UPLO_t Uplo,
                      CBLAS_TRANSPOSE_t Trans,
                      const gsl_complex_float alpha,
                      const gsl_matrix_complex_float * A,
                      const gsl_matrix_complex_float * B,
                      const gsl_complex_float beta,
                      gsl_matrix_complex_float * C); 
int  gsl_blas_zsyr2k (CBLAS_UPLO_t Uplo,
                      CBLAS_TRANSPOSE_t Trans,
                      const gsl_complex alpha,
                      const gsl_matrix_complex * A,
                      const gsl_matrix_complex * B,
                      const gsl_complex beta,
                      gsl_matrix_complex *C);                                                      
```

共轭矩阵 $C$；$C=\alpha A^HB+\alpha^* B^HA+\beta C$
```cpp
int  gsl_blas_cher2k (CBLAS_UPLO_t Uplo,
                      CBLAS_TRANSPOSE_t Trans,
                      const gsl_complex_float alpha,
                      const gsl_matrix_complex_float * A,
                      const gsl_matrix_complex_float * B,
                      float beta,
                      gsl_matrix_complex_float * C);
int  gsl_blas_zher2k (CBLAS_UPLO_t Uplo,
                      CBLAS_TRANSPOSE_t Trans,
                      const gsl_complex alpha,
                      const gsl_matrix_complex * A,
                      const gsl_matrix_complex * B,
                      double beta,
                      gsl_matrix_complex * C);                      
```
