# GSL 系列 6 — 线性代数 2 — LU 分解


<!--more-->


## 1 写在前面

关于 LU 分解的背景知识介绍，参见：[LU分解]({{< relref "posts/GSL线性代数1.md#LU" >}})，本篇只说明相关函数

## 2 LU 分解相关对象和函数

### 转置矩阵对象

转置矩阵对象存储着一列索引。第 $j$ 个数为 $k$ ，表示转置矩阵第 $j$ 列是相应单位矩阵的第 $k$ 列，定义如下：

```cpp
// gsl_permutation.h
struct gsl_permutation_struct
{
  size_t size;
  size_t *data;
};
typedef struct gsl_permutation_struct gsl_permutation;
```
### LU 分解函数

将矩阵 `A` 进行 LU 分解，然后得到转置矩阵 `p`，`LU` 矩阵储存在 `A` 中，`signum` 为 1 或  -1，代表交换的次数，奇数次为 -1，偶数次为 1
因为下三角（梯形）矩阵对角线为 1，不储存，`A` 刚好可以方向 `LU`
```cpp
// gsl_linalg.h
int gsl_linalg_LU_decomp(gsl_matrix * A, gsl_permutation * p, int *signum);
int gsl_linalg_complex_LU_decomp(gsl_matrix_complex * A, 
                                  gsl_permutation * p, 
                                  int *signum);
```

### LU 求解方程

`solve`版本：给 `LU` 矩阵，`p` 转置矩阵，向量 `b`，得到解向量 `x`

`svx`版本: 给 `LU` 矩阵，`p` 转置矩阵，向量 b（就是 `x`），得到解向量 `x`，解向量存在 `x` 中，即 `solve` 的置换版本

`refine` 版本：应用迭代改进的办法求解 `x`，还需要给一个向量工作空间 `work`，长度为 `x` 的长度
```cpp
// gsl_linalg.h
int gsl_linalg_LU_solve (const gsl_matrix * LU,
                         const gsl_permutation * p,
                         const gsl_vector * b,
                         gsl_vector * x);
int gsl_linalg_complex_LU_solve (const gsl_matrix_complex * LU,
                                 const gsl_permutation * p,
                                 const gsl_vector_complex * b,
                                 gsl_vector_complex * x);
int gsl_linalg_LU_svx (const gsl_matrix * LU,
                       const gsl_permutation * p,
                       gsl_vector * x);
int gsl_linalg_complex_LU_svx (const gsl_matrix_complex * LU,
                               const gsl_permutation * p,
                               gsl_vector_complex * x);   
int gsl_linalg_LU_refine (const gsl_matrix * A,
                          const gsl_matrix * LU,
                          const gsl_permutation * p,
                          const gsl_vector * b,
                          gsl_vector * x,
                          gsl_vector * work);                               
int gsl_linalg_complex_LU_refine (const gsl_matrix_complex * A,
                                  const gsl_matrix_complex * LU,
                                  const gsl_permutation * p,
                                  const gsl_vector_complex * b,
                                  gsl_vector_complex * x,
                                  gsl_vector_complex * work);                                                   
```

### LU 求逆

类似地，分为无置换版本 (`invert`) 和置换版本 (`invx`)，无置换时，存在矩阵 `inverse` 中，有置换时，存在 `LU` 中
```cpp
// gsl_linalg.h
int gsl_linalg_LU_invert (const gsl_matrix * LU,
                          const gsl_permutation * p,
                          gsl_matrix * inverse);
int gsl_linalg_complex_LU_invert (const gsl_matrix_complex * LU,
                                  const gsl_permutation * p,
                                  gsl_matrix_complex * inverse);                          
int gsl_linalg_LU_invx (gsl_matrix * LU, const gsl_permutation * p);
int gsl_linalg_complex_LU_invx (gsl_matrix_complex * LU, const gsl_permutation * p);                                  
```

> 最好避免通过直接求逆的方法来求解线性方程组，因为线性方程组求解函数更有效率和更准确

### LU 求行列式

求解 $\det(A)$，需要给 `LU` 矩阵
```cpp
// gsl_linalg.h
double gsl_linalg_LU_det (gsl_matrix * LU, int signum);
gsl_complex gsl_linalg_complex_LU_det (gsl_matrix_complex * LU,
                                       int signum);                                
```
求解 $\ln(|\det(A)|)$，需要给 `LU` 矩阵
```cpp
// gsl_linalg.h
double gsl_linalg_LU_lndet (gsl_matrix * LU);
double gsl_linalg_complex_LU_lndet (gsl_matrix_complex * LU);   
```
求解 $\det(A)/|\det(A)|$，需要给 `LU` 矩阵
```cpp
// gsl_linalg.h
int gsl_linalg_LU_sgndet (gsl_matrix * lu, int signum);
gsl_complex gsl_linalg_complex_LU_sgndet (gsl_matrix_complex * LU,
                                          int signum);    
```
