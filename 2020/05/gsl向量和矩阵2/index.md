# GSL 系列 5 — 向量和矩阵 2 — 向量 (vector)


<!--more-->

## 1 写在前面
因为向量是构建于块之上，请先理解块，参见：[块 (block)]({{< relref "posts/GSL向量和矩阵1.md#block" >}})

如果没有特殊说明，本篇代码均来自头文件 `gsl_vector_double.h`，即以下的类型，函数等都是基于`double` 的。对于其他数据类型的类型、函数等，都是与之类似。


## 2 向量 (vector) {#vec}
`向量`建构于`块`之上，添加了对`块`的`切片`描述，向量`切片`必须是内存空间中一组`等间隔`的元素，**不同的向量可以创建于一个块之上**，定义如下：

```cpp
typedef struct 
{
  size_t size; // 向量元素个数
  size_t stride; // 相邻元素的物理空间步长
  double *data; // 向量的指针，地址为向量的第一个元素
  gsl_block *block; // 向量的数据基础快的指针
  int owner; // 向量对块的所有权
} 
gsl_vector;
```
上述注释中，向量对块的所有权是指，当向量对块有所有权 (`owner=1`) 时，向量内存在释放时，块内存也会得到释放

## 3 向量内存分配和释放

```cpp
gsl_vector *gsl_vector_alloc(const size_t n); // 分配一个长度为 n 的向量，无初始化
gsl_vector *gsl_vector_calloc(const size_t n); // 分配一个长度为 n 的向量，初始化为 0

// 根据块分配向量内存
gsl_vector *gsl_vector_alloc_from_block(gsl_block * b,
                                                     const size_t offset, 
                                                     const size_t n, 
                                                     const size_t stride);
// 根据向量分配向量内存
gsl_vector *gsl_vector_alloc_from_vector(gsl_vector * v,
                                                      const size_t offset, 
                                                      const size_t n, 
                                                      const size_t stride);

void gsl_vector_free(gsl_vector * v); // 向量内存释放
```

## 4 获取向量元素
包含一个向量元素设定函数
```cpp
// HAVE_INLINE 定义时下列函数使用 内联
// 获取向量第 i 个元素
INLINE_DECL double gsl_vector_get(const gsl_vector * v, const size_t i);
// 设定向量第 i 个元素
INLINE_DECL void gsl_vector_set(gsl_vector * v, const size_t i, double x);
// 返回第 i 个元素的指针
INLINE_DECL double * gsl_vector_ptr(gsl_vector * v, const size_t i);
INLINE_DECL const double * gsl_vector_const_ptr(const gsl_vector * v, const size_t i);
```

## 5 向量初始化

```cpp
// 初始化为 0
void gsl_vector_set_zero(gsl_vector * v);
// 初始化为 x
void gsl_vector_set_all(gsl_vector * v, double x);
// 第 i 个元素设为 1， 其他元素设为 0
int gsl_vector_set_basis(gsl_vector * v, size_t i);
```
## 6 向量读取和写入 {#rw}

```cpp
// 以下函数，成功返回 0，否则 错误 GSL_EFAILED
// 以二进制 从 流 读到 向量 中，向量需分配好合适的长度
int gsl_vector_fread(FILE * stream, gsl_vector * v);
// 以二进制 从 向量 写到 流 中
int gsl_vector_fwrite(FILE * stream, const gsl_vector * v);
// 以格式化的数据 从 流 读到 向量 中，向量需分配好合适的长度
int gsl_vector_fscanf(FILE * stream, gsl_vector * v);
// 以格式化的方式 从 向量 写到 流 中
// %g, %e, %f 用于浮点数 %d 用于整数
int gsl_vector_fprintf(FILE * stream, const gsl_vector * v,
                              const char *format);
```

## 7 向量查看 (view)
### 7.1 向量查看对象
`向量查看对象`是放在`栈区`的，有`非 const` 和 `const` 两种，定义如下

```cpp
typedef struct
{
  gsl_vector vector;
} _gsl_vector_view;

typedef _gsl_vector_view gsl_vector_view;

typedef struct
{
  gsl_vector vector;
} _gsl_vector_const_view;

typedef const _gsl_vector_const_view gsl_vector_const_view;
```
### 7.2 返回向量查看对象
除了根据以下方式返回向量查看对象，还可以根据矩阵返回向量查看对象，具体参见：{% post_link GSL向量和矩阵3 GSL 系列 5 — 向量和矩阵 3 — 矩阵 (matrix) false %}（根据矩阵返回向量查看对象）节


```cpp
// 根据数组，返回向量查看对象，长度为 n
_gsl_vector_view 
gsl_vector_view_array(double *v, size_t n);
_gsl_vector_view 
gsl_vector_view_array_with_stride(double *base,
                                         size_t stride,
                                         size_t n);
_gsl_vector_const_view 
gsl_vector_const_view_array(const double *v, size_t n);
_gsl_vector_const_view 
gsl_vector_const_view_array_with_stride(const double *base,
                                               size_t stride,
                                               size_t n);
// 根据 向量 返回一个向量查看对象, i 是原向量的偏置，n 是 新向量查看对象对应的长度
_gsl_vector_view 
gsl_vector_subvector(gsl_vector *v, 
                            size_t i, 
                            size_t n);
_gsl_vector_view 
gsl_vector_subvector_with_stride(gsl_vector *v, 
                                        size_t i,
                                        size_t stride,
                                        size_t n);
_gsl_vector_const_view 
gsl_vector_const_subvector(const gsl_vector *v, 
                                  size_t i, 
                                  size_t n);
_gsl_vector_const_view 
gsl_vector_const_subvector_with_stride(const gsl_vector *v, 
                                              size_t i, 
                                              size_t stride,
                                              size_t n);
```
## 8 向量复制和交换

```cpp
// 将向量 src 复制到 向量 dest 中
int gsl_vector_memcpy(gsl_vector * dest, const gsl_vector * src);
// 将向量 v 反序
int gsl_vector_reverse(gsl_vector * v);
// 交换向量 v 和 w
int gsl_vector_swap(gsl_vector * v, gsl_vector * w);
// 交换向量 v 的第 i 和 j 个元素
int gsl_vector_swap_elements(gsl_vector * v, const size_t i, const size_t j);
```

## 9 向量运算

```cpp
// 以下运算结果保存在 a 中
int gsl_vector_add(gsl_vector * a, const gsl_vector * b); // a+b
int gsl_vector_sub(gsl_vector * a, const gsl_vector * b); // a-b
int gsl_vector_mul(gsl_vector * a, const gsl_vector * b); // a*b, 元素乘
int gsl_vector_div(gsl_vector * a, const gsl_vector * b); // a/b，元素除
int gsl_vector_scale(gsl_vector * a, const double x); // xa
int gsl_vector_add_constant(gsl_vector * a, const double x); // a+[x,..,x]
// 以下运算将结果保存在 y 中
int gsl_vector_axpby(const double alpha, const gsl_vector * x, const double beta, gsl_vector * y); // alpha x + beta y
```

## 10 找寻向量的最大最小元素

```cpp
double gsl_vector_max (const gsl_vector * v); // 返回最大值
double gsl_vector_min (const gsl_vector * v); // 返回最小值
// 返回最大值，最小值，存在 max_out, min_out 中
void gsl_vector_minmax (const gsl_vector * v, double * min_out, double * max_out);

size_t gsl_vector_max_index (const gsl_vector * v); // 返回最大值索引
size_t gsl_vector_min_index (const gsl_vector * v); // 返回最小值索引
// 返回最大，最小值索引，存放在 imax, imin 中
void gsl_vector_minmax_index (const gsl_vector * v, size_t * imin, size_t * imax);
```

## 11 向量判断
```cpp
// 满足条件返回 1 ，否则 0
int gsl_vector_equal(const gsl_vector * u, 
                            const gsl_vector * v); // 两向量是否相等
int gsl_vector_isnull(const gsl_vector * v); // 向量所有元素为 0
int gsl_vector_ispos(const gsl_vector * v); // 向量所有元素为 正
int gsl_vector_isneg(const gsl_vector * v); // 向量所有元素为 负
int gsl_vector_isnonneg(const gsl_vector * v); // 向量所有元素为 非负
```
