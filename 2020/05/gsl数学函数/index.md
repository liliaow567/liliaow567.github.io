# GSL 系列 1 — 数学函数


<!--more-->



## 1 头文件

`gsl_math.h`


## 2 数学常量
{{<image src="https://s1.ax1x.com/2020/05/09/YM1we1.png" width="400">}}

宏定义示例：

```cpp
#ifndef M_E
#define M_E        2.71828182845904523536028747135      /* e */
#endif
```

## 3 无穷和非数

`GSL_POSINF`: 正无穷

`GSL_NEGINF`: 负无穷

`GSL_NAN`：非数

宏定义：

```cpp
// gsl_nan.h
#ifdef INFINITY
# define GSL_POSINF INFINITY
# define GSL_NEGINF (-INFINITY)
#elif defined(HUGE_VAL)
# define GSL_POSINF HUGE_VAL
# define GSL_NEGINF (-HUGE_VAL)
#else
# define GSL_POSINF (gsl_posinf())
# define GSL_NEGINF (gsl_neginf())
#endif

#ifdef NAN
# define GSL_NAN NAN
#elif defined(INFINITY)
# define GSL_NAN (INFINITY/INFINITY)
#else
# define GSL_NAN (gsl_nan())
#endif

//corecrt_math.h
#ifndef _HUGE_ENUF
    #define _HUGE_ENUF  1e+300  // _HUGE_ENUF*_HUGE_ENUF must overflow
#endif

#define INFINITY   ((float)(_HUGE_ENUF * _HUGE_ENUF))
#define HUGE_VAL   ((double)INFINITY)

#define NAN        ((float)(INFINITY * 0.0F))

//gsl_sys.h
double gsl_nan (void);
double gsl_posinf (void);
double gsl_neginf (void);
```
判断函数：

```cpp
//gsl_sys.h
int gsl_isnan (const double x); //非数返回 1
int gsl_isinf (const double x); //正无穷返回 1，负无穷返回 -1，其他返回 0
int gsl_finite (const double x); // 实数返回 1，无穷和非数返回 0
```

## 4 初等函数

```cpp
// gsl_sys.h
double gsl_log1p(const double x); // log(1+x); x 小的时候较为精确
double gsl_expm1(const double x); // exp(x)-1; x 小的时候较为精确
double gsl_hypot(const double x, const double y); // sqrt(x^2 + y^2); 避免溢出
double gsl_hypot3(const double x, const double y, const double z); // sqrt(x^2+y^2+z^2); 避免溢出
double gsl_acosh(const double x); // arccosh(x) 
double gsl_asinh(const double x); // arcsinh(x)
double gsl_atanh(const double x); // arctanh(x)
double gsl_ldexp(const double x, const int e); // x*2^e;
double gsl_frexp(const double x, int * e); // x/2^e; x为0，f,e 都设为 0
```

## 5 小整数幂

```cpp
// gsl_pow_int.h
INLINE_DECL double gsl_pow_2(const double x);
INLINE_DECL double gsl_pow_3(const double x);
INLINE_DECL double gsl_pow_4(const double x);
INLINE_DECL double gsl_pow_5(const double x);
INLINE_DECL double gsl_pow_6(const double x);
INLINE_DECL double gsl_pow_7(const double x);
INLINE_DECL double gsl_pow_8(const double x);
INLINE_DECL double gsl_pow_9(const double x);
/* HAVE_INLINE被定义时，以上函数使用内联 */

double gsl_pow_int(double x, int n);
double gsl_pow_uint(double x, unsigned int n);
/* 该函数还有另一个版本，会计算数值误差，gsl_sf_pow_int_e()
```

## 6 正负测试
`GSL_SIGN(X)`: 大于等于 0 为 1，小于 0 为 -1

宏定义：

```cpp
// gsl_math.h
#define GSL_SIGN(x)    ((x) >= 0.0 ? 1 : -1)
```

## 7 奇偶数测试
`GSL_IS_ODD(n)`: 奇数返回 1，偶数返回 0

`GSL_IS_EVEN(n)`: 偶数返回 1，奇数返回 0

宏定义：

```cpp
// gsl_math.h
#define GSL_IS_ODD(n)  ((n) & 1)
#define GSL_IS_EVEN(n) (!(GSL_IS_ODD(n)))
```

## 8 最大最小函数
`GSL_MAX(a,b)`: 返回 a, b 的最大值

`GSL_MIN(a,b)`: 返回 a, b 的最小值

宏定义：

```cpp
// gsl_minmax.h
#define GSL_MAX(a,b) ((a) > (b) ? (a) : (b))
#define GSL_MIN(a,b) ((a) < (b) ? (a) : (b))
// 以上两个宏，还有函数版本 gsl_max, gsl_min
double gsl_max (double a, double b);
double gsl_min (double a, double b);
```

内联函数版本(`HAVE_INLINE` 定义的前提下)：

```cpp
// gsl_minmax.h
INLINE_FUN int GSL_MAX_INT (int a, int b);
INLINE_FUN int GSL_MIN_INT (int a, int b);
INLINE_FUN double GSL_MAX_DBL (double a, double b);
INLINE_FUN double GSL_MIN_DBL (double a, double b);
INLINE_FUN long double GSL_MAX_LDBL (long double a, long double b);
INLINE_FUN long double GSL_MIN_LDBL (long double a, long double b);

// 内联版本实现示例
INLINE_FUN int
GSL_MAX_INT (int a, int b)
{
  return GSL_MAX (a, b);
}
//无内联版本
#define GSL_MAX_INT(a,b)   GSL_MAX(a,b)
```

## 9 浮点数的近似比较

```cpp
int gsl_fcmp (const double x1, const double x2, const double epsilon);
/* 
判断 x, y 是否近似等于一个相对精度 epsilon, 
根据 frexp() 计算出一个间隔, 判断 x, y 是否在间隔之内，
在，即返回0，认为近似相等，否则，x < y 返回 -1，x > y 返回 + 1。
注意，比较的是相对精度，而不是两者间隔接近 0 的程度。
基于 fcmp 实现。
*/
```

## 10 参考
[https://www.gnu.org/software/gsl/doc/html/math.html](https://www.gnu.org/software/gsl/doc/html/math.html#mathematical-constants)
