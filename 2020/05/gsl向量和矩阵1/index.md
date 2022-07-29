# GSL 系列 5 — 向量和矩阵 1 — 块 (block)


<!--more-->


## 1 块 (block) {#block}
`块`是`向量`，`矩阵`数据存储的基本结构，包含两个部分：

 - `块内存的size` 
 - `块内存的指针`


默认的 `gsl_block` `块`是 `double` 类型数据，定义如下：

```cpp
// gsl_block_double.h
struct gsl_block_struct
{
  size_t size;
  double *data;
};
typedef struct gsl_block_struct gsl_block;

// vcruntime.h
typedef unsigned __int64 size_t;
```
如果需要用其他数据类型的块，则在后边加上对应的类型名称，比如 `gsl_block_float`

### 1.1 块内存的分配和释放

```cpp
// gsl_block_double.h
gsl_block *gsl_block_alloc(const size_t n); // 分配不初始化，返回指针
gsl_block *gsl_block_calloc(const size_t n); // 分配并初始化为 0，返回指针
void gsl_block_free(gsl_block * b); // 通过指针，内存释放
```
在没有足够的内存分配时，块内存分配函数会调用错误句柄返回 `GSL_ENOMEM` 错误，以及`空指针`

### 1.2 块内存读写

```cpp
// gsl_block_double.h
/*
以 二进制 将 块 写入 流 中，成功返回 0， 失败则 GSL_EFAILED
*/
int gsl_block_fread(FILE * stream, gsl_block * b);
/*
以 二进制 从 流 读到 块 中，成功返回 0， 失败则 GSL_EFAILED
块 b 需要预先分配好正确的长度，因为是以 b 的内存大小读入的
*/
int gsl_block_fwrite(FILE * stream, const gsl_block * b);
/*
以 格式化的数据 从 流 读到 块 中，成功返回 0， 失败则 GSL_EFAILED
块 b 需要预先分配好正确的长度，因为是以 b 的内存大小读入的
*/
int gsl_block_fscanf(FILE * stream, gsl_block * b);
/*
以 格式化的方式 从 块 写入 流 中，成功返回 0， 失败则 GSL_EFAILED
%g, %e, %f 用于浮点数，%d 用于整数
*/
int gsl_block_fprintf(FILE * stream, const gsl_block * b, const char *format);

int gsl_block_raw_fread(FILE * stream, double * b, const size_t n, const size_t stride);
int gsl_block_raw_fwrite(FILE * stream, const double * b, const size_t n, const size_t stride);
int gsl_block_raw_fscanf(FILE * stream, double * b, const size_t n, const size_t stride);
int gsl_block_raw_fprintf(FILE * stream, const double * b, const size_t n, const size_t stride, const char *format);
```
### 1.3 通过函数获取块的成员

```cpp
// gsl_block_double.h
size_t gsl_block_size (const gsl_block * b);
double * gsl_block_data (const gsl_block * b);
```
