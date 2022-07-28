# GSL 系列 5 — 向量和矩阵 3 — 矩阵 (matrix)


<!--more-->



## 1 写在前面
因为矩阵是构建于块之上，请先理解块，参见：[块 (block)]({{< relref "posts/GSL向量和矩阵1.md#block" >}})

如果没有特殊说明，本篇代码均来自头文件 `gsl_matrix_double.h`，即以下的类型，函数等都是基于`double` 的。对于其他数据类型的类型、函数等，都是与之类似。

## 2 矩阵

`矩阵`建构在 `block` 之上，是对 `block` 的切片描述。矩阵结构定义如下：

```cpp
typedef struct 
{
  size_t size1; // 矩阵维度 1 —— 多少行
  size_t size2; // 矩阵纬度 2 —— 多少列
  size_t tda; // 物理行尺寸
  double * data; // 指向矩阵
  gsl_block * block; // 指向矩阵对应 block 
  int owner; // 矩阵对 block 的所有权
} gsl_matrix;
```
`所有权`的解释参见：[所有权]({{< relref "posts/GSL向量和矩阵2.md#vec" >}})

矩阵是按行存储的，即在内存中，一行是连续的。

## 3 矩阵内存分配和释放
### 3.1 矩阵分配和释放
```cpp
// n1 行，n2 列
gsl_matrix_alloc(const size_t n1, const size_t n2); // 无初始化
gsl_matrix * 
gsl_matrix_calloc(const size_t n1, const size_t n2); // 初始化为 0
// 根据块分配
gsl_matrix * 
gsl_matrix_alloc_from_block(gsl_block * b, 
                                   const size_t offset, 
                                   const size_t n1, 
                                   const size_t n2, 
                                   const size_t d2);
// 根据矩阵分配, k1 k2 是分配的矩阵左上角在原矩阵的索引
gsl_matrix * 
gsl_matrix_alloc_from_matrix(gsl_matrix * m,
                                    const size_t k1, 
                                    const size_t k2,
                                    const size_t n1, 
                                    const size_t n2);
// 释放
void gsl_matrix_free(gsl_matrix * m);
```

### 3.2 从矩阵中分配向量内存

```cpp
// 根据矩阵分配向量内存
gsl_vector * 
gsl_vector_alloc_row_from_matrix(gsl_matrix * m,
                                        const size_t i);

gsl_vector * 
gsl_vector_alloc_col_from_matrix(gsl_matrix * m,
                                        const size_t j);
```

## 4 矩阵元素获取

```cpp
// HAVE_INLINE 定义时，使用内联
INLINE_DECL double   gsl_matrix_get(const gsl_matrix * m, const size_t i, const size_t j);
INLINE_DECL void    gsl_matrix_set(gsl_matrix * m, const size_t i, const size_t j, const double x);
INLINE_DECL double * gsl_matrix_ptr(gsl_matrix * m, const size_t i, const size_t j);
INLINE_DECL const double * gsl_matrix_const_ptr(const gsl_matrix * m, const size_t i, const size_t j);
```
矩阵元素获取实际上是 `mp->data[i * mp->tda + j]`，`tda` 即物理行尺寸

## 5 矩阵初始化

```cpp
void gsl_matrix_set_zero(gsl_matrix * m); // 置 0
void gsl_matrix_set_identity(gsl_matrix * m); // 对角置 1，非对角置 0
void gsl_matrix_set_all(gsl_matrix * m, double x); // 置 x
```
## 6 矩阵读取和写入
和向量类似，参见：[向量读取和写入]({{< relref "posts/GSL向量和矩阵2.md#rw" >}})
```cpp
int gsl_matrix_fread(FILE * stream, gsl_matrix * m) ;
int gsl_matrix_fwrite(FILE * stream, const gsl_matrix * m) ;
int gsl_matrix_fscanf(FILE * stream, gsl_matrix * m);
int gsl_matrix_fprintf(FILE * stream, const gsl_matrix * m, const char * format);
```

## 7 矩阵及其行、列查看 (view)

### 7.1 矩阵查看对象

存放在`栈区`，有 `const` 和 `非const`，定义如下：

```cpp
typedef struct
{
  gsl_matrix matrix;
} _gsl_matrix_view;

typedef _gsl_matrix_view gsl_matrix_view;

typedef struct
{
  gsl_matrix matrix;
} _gsl_matrix_const_view;

typedef const _gsl_matrix_const_view gsl_matrix_const_view;
```

### 7.2 返回矩阵查看对象

```cpp
// 从矩阵中返回矩阵查看对象
// i,j 是返回对象在原矩阵的左上角坐标
// n1, n2 是返回对象的行数，列数
_gsl_matrix_view 
gsl_matrix_submatrix(gsl_matrix * m, 
                            const size_t i, const size_t j, 
                            const size_t n1, const size_t n2);
_gsl_matrix_const_view 
gsl_matrix_const_submatrix(const gsl_matrix * m, 
                                  const size_t i, const size_t j, 
                                  const size_t n1, const size_t n2);                             
// 从数组中返回矩阵查看对象
// tda 是物理行尺寸
_gsl_matrix_view
gsl_matrix_view_array(double * base,
                             const size_t n1, 
                             const size_t n2);
_gsl_matrix_view
gsl_matrix_view_array_with_tda(double * base, 
                                      const size_t n1, 
                                      const size_t n2,
                                      const size_t tda);
// 从向量中返回矩阵查看对象                                      
_gsl_matrix_view
gsl_matrix_view_vector(gsl_vector * v,
                              const size_t n1, 
                              const size_t n2);

_gsl_matrix_view
gsl_matrix_view_vector_with_tda(gsl_vector * v,
                                       const size_t n1, 
                                       const size_t n2,
                                       const size_t tda);                           
```
### 7.3 根据矩阵返回向量查看对象

```cpp
// 第 i 行
_gsl_vector_view 
gsl_matrix_row(gsl_matrix * m, const size_t i);
_gsl_vector_const_view 
gsl_matrix_const_row(const gsl_matrix * m, 
                            const size_t i);
// 第 i 行，偏置 offset, n 个元素
_gsl_vector_view
gsl_matrix_subrow(gsl_matrix * m, const size_t i,
                         const size_t offset, const size_t n);
_gsl_vector_const_view
gsl_matrix_const_subrow(const gsl_matrix * m, const size_t i,
                               const size_t offset, const size_t n);                              
// 第 i 列
_gsl_vector_view 
gsl_matrix_column(gsl_matrix * m, const size_t j);
_gsl_vector_const_view 
gsl_matrix_const_column(const gsl_matrix * m, 
                               const size_t j);
// 第 i 列，偏置 offset, n 个元素
_gsl_vector_view
gsl_matrix_subcolumn(gsl_matrix * m, const size_t j,
                            const size_t offset, const size_t n);
_gsl_vector_const_view
gsl_matrix_const_subcolumn(const gsl_matrix * m, const size_t j,
                                  const size_t offset, const size_t n);                                
// 对角
_gsl_vector_view 
gsl_matrix_diagonal(gsl_matrix * m);
_gsl_vector_const_view
gsl_matrix_const_diagonal(const gsl_matrix * m);
// 子对角
_gsl_vector_view 
gsl_matrix_subdiagonal(gsl_matrix * m, const size_t k);
_gsl_vector_const_view 
gsl_matrix_const_subdiagonal(const gsl_matrix * m, 
                                    const size_t k);
// 超对角
_gsl_vector_view 
gsl_matrix_superdiagonal(gsl_matrix * m, const size_t k);
_gsl_vector_const_view 
gsl_matrix_const_superdiagonal(const gsl_matrix * m, 
                                      const size_t k);                                                  
```

## 8 矩阵及其行、列复制

```cpp
// 复制一个矩阵到另一个矩阵
int gsl_matrix_memcpy(gsl_matrix * dest, const gsl_matrix * src);
// 复制矩阵的行、列到向量中
int gsl_matrix_get_row(gsl_vector * v, const gsl_matrix * m, const size_t i);
int gsl_matrix_get_col(gsl_vector * v, const gsl_matrix * m, const size_t j);
// 复制向量到矩阵的行、列中
int gsl_matrix_set_row(gsl_matrix * m, const size_t i, const gsl_vector * v);
int gsl_matrix_set_col(gsl_matrix * m, const size_t j, const gsl_vector * v);
```
**注意**：源码中提到，上面的后四个函数是过时的。

## 9 矩阵及其行、列交换

```cpp
// 交换两个矩阵
int gsl_matrix_swap(gsl_matrix * m1, gsl_matrix * m2);
// 交换矩阵的行与列
int gsl_matrix_swap_rows(gsl_matrix * m, const size_t i, const size_t j);
int gsl_matrix_swap_columns(gsl_matrix * m, const size_t i, const size_t j);
int gsl_matrix_swap_rowcol(gsl_matrix * m, const size_t i, const size_t j);
// 将矩阵 src 转置复制给目标矩阵 dest 
int gsl_matrix_transpose_memcpy(gsl_matrix * dest, const gsl_matrix * src);
```

## 10 矩阵运算

```cpp
int gsl_matrix_transpose(gsl_matrix * m); // 矩阵转置
// 以下计算结果保存到 a
int gsl_matrix_add(gsl_matrix * a, const gsl_matrix * b); // a+b
int gsl_matrix_sub(gsl_matrix * a, const gsl_matrix * b); // a-b
int gsl_matrix_mul_elements(gsl_matrix * a, const gsl_matrix * b); // a*b
int gsl_matrix_div_elements(gsl_matrix * a, const gsl_matrix * b); // a/b
int gsl_matrix_scale(gsl_matrix * a, const double x); // xa
int gsl_matrix_add_constant(gsl_matrix * a, const double x); // a+x, x 加到 a 每一个元素
int gsl_matrix_add_diagonal(gsl_matrix * a, const double x); // a+x, x 加到 a 的对角元素 
```

## 11 找寻矩阵最大、最小元素

```cpp
double gsl_matrix_max(const gsl_matrix * m);
double gsl_matrix_min(const gsl_matrix * m);
void gsl_matrix_minmax(const gsl_matrix * m, double * min_out, double * max_out);

void gsl_matrix_max_index(const gsl_matrix * m, size_t * imax, size_t *jmax);
void gsl_matrix_min_index(const gsl_matrix * m, size_t * imin, size_t *jmin);
void gsl_matrix_minmax_index(const gsl_matrix * m, size_t * imin, size_t * jmin, size_t * imax, size_t * jmax);
```

## 12 矩阵判断

```cpp
// 两个矩阵是否相等
int gsl_matrix_equal (const gsl_matrix * a, const gsl_matrix * b);
// 以下判断是对矩阵的所有元素
int gsl_matrix_isnull (const gsl_matrix * m); // 是否为 0
int gsl_matrix_ispos (const gsl_matrix * m); // 是否为 正
int gsl_matrix_isneg (const gsl_matrix * m); // 是否为 负
int gsl_matrix_isnonneg (const gsl_matrix * m); // 是否为 非负
```
