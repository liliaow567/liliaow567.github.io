# GSL 系列 2 — 复数


<!--more-->


## 1 头文件

`gsl_complex.h`: 定义复数类

`gsl_complex_math.h`: 定义复函数和相关运算

## 2 复数表示

`gsl_complex`

类定义：

```cpp
// gsl_complex.h
typedef struct
  {
    double dat[2];
  }
gsl_complex;
```
复数实部：`dat[0]`

复数虚部：`dat[1]`

### 复数构建函数



`gsl_complex_polar`: 从极坐标构建

`gsl_complex_rect`: 从笛卡尔坐标构建，使用内联（当 `HAVE_INLINE` 定义时）

```cpp
gsl_complex gsl_complex_polar (double r, double theta); /* r= r e^(i theta) */

INLINE_DECL gsl_complex gsl_complex_rect (double x, double y);  /* r= real+i*imag */
```

### 复数基本操作宏

`GSL_REAL(z)`: 获取复数 `z` 的实部

`GSL_IMAG(z)`: 获取复数 `z` 的虚部

`GSL_COMPLEX_P(zp)`: 获取指针 `zp` 所指的复数

`GSL_COMPLEX_P_REAL(zp)`: 获取指针 `zp` 所指的复数实部

`GSL_COMPLEX_P_IMAG(zp)`: 获取指针 `zp` 所指的复数虚部

`GSL_COMPLEX_EQ(z1,z2)`: 判断两个复数是否相等

`GSL_SET_COMPLEX(zp, x, y)`: 通过复数指针 `zp`，以笛卡尔坐标形式设定复数

`GSL_SET_REAL(zp, x)`:  通过复数指针 `zp`，设定复数实部

`GSL_SET_IMAG(zp, y)`:  通过复数指针 `zp`，设定复数虚部

宏定义：

```cpp
// gsl_complex.h
#define GSL_REAL(z)     ((z).dat[0])
#define GSL_IMAG(z)     ((z).dat[1])
#define GSL_COMPLEX_P(zp) ((zp)->dat)
#define GSL_COMPLEX_P_REAL(zp)  ((zp)->dat[0])
#define GSL_COMPLEX_P_IMAG(zp)  ((zp)->dat[1])
#define GSL_COMPLEX_EQ(z1,z2) (((z1).dat[0] == (z2).dat[0]) && ((z1).dat[1] == (z2).dat[1]))
```

## 3 复数属性获取函数

```cpp
// gsl_complex_math.h
double gsl_complex_arg(gsl_complex z); /* return arg(z), -pi< arg(z) <=+pi */
double gsl_complex_abs(gsl_complex z);   /* return |z|   */
double gsl_complex_abs2(gsl_complex z);  /* return |z|^2 */
double gsl_complex_logabs(gsl_complex z); /* return log|z| */
```

## 4 复数算术运算

```cpp
// gsl_complex_math.h
gsl_complex gsl_complex_add(gsl_complex a, gsl_complex b);  /* r=a+b */
gsl_complex gsl_complex_sub(gsl_complex a, gsl_complex b);  /* r=a-b */
gsl_complex gsl_complex_mul(gsl_complex a, gsl_complex b);  /* r=a*b */
gsl_complex gsl_complex_div(gsl_complex a, gsl_complex b);  /* r=a/b */
                                                           
gsl_complex gsl_complex_add_real(gsl_complex a, double x);  /* r=a+x */
gsl_complex gsl_complex_sub_real(gsl_complex a, double x);  /* r=a-x */
gsl_complex gsl_complex_mul_real(gsl_complex a, double x);  /* r=a*x */
gsl_complex gsl_complex_div_real(gsl_complex a, double x);  /* r=a/x */

gsl_complex gsl_complex_add_imag(gsl_complex a, double y);  /* r=a+iy */
gsl_complex gsl_complex_sub_imag(gsl_complex a, double y);  /* r=a-iy */
gsl_complex gsl_complex_mul_imag(gsl_complex a, double y);  /* r=a*iy */
gsl_complex gsl_complex_div_imag(gsl_complex a, double y);  /* r=a/iy */

gsl_complex gsl_complex_conjugate(gsl_complex z);  /* r=conj(z) * 共轭/ 
gsl_complex gsl_complex_inverse(gsl_complex a);    /* r=1/a * 逆/
gsl_complex gsl_complex_negative(gsl_complex a);    /* r=-a * 相反/
```

## 5 初等复函数

```cpp
// gsl_complex_math.h
gsl_complex gsl_complex_sqrt(gsl_complex z);  /* r=sqrt(z) */
gsl_complex gsl_complex_sqrt_real(double x);  /* r=sqrt(x) (x<0 ok) */

gsl_complex gsl_complex_pow(gsl_complex a, gsl_complex b);  /* r=a^b */
gsl_complex gsl_complex_pow_real(gsl_complex a, double b);  /* r=a^b */

gsl_complex gsl_complex_exp(gsl_complex a);    /* r=exp(a) */
gsl_complex gsl_complex_log(gsl_complex a);    /* r=log(a) (base e) */
gsl_complex gsl_complex_log10(gsl_complex a);  /* r=log10(a) (base 10) */
gsl_complex gsl_complex_log_b(gsl_complex a, gsl_complex b);   /* r=log_b(a) (base=b) */
```

## 6 复三角函数

```cpp
// gsl_complex_math.h
gsl_complex gsl_complex_sin(gsl_complex a);  /* r=sin(a) */
gsl_complex gsl_complex_cos(gsl_complex a);  /* r=cos(a) */
gsl_complex gsl_complex_sec(gsl_complex a);  /* r=sec(a) */
gsl_complex gsl_complex_csc(gsl_complex a);  /* r=csc(a) */
gsl_complex gsl_complex_tan(gsl_complex a);  /* r=tan(a) */
gsl_complex gsl_complex_cot(gsl_complex a);  /* r=cot(a) */
```

## 7 复反三角函数

```cpp
// gsl_complex_math.h
gsl_complex gsl_complex_arcsin(gsl_complex a);  /* r=arcsin(a) */
gsl_complex gsl_complex_arcsin_real(double a);  /* r=arcsin(a) */
gsl_complex gsl_complex_arccos(gsl_complex a);  /* r=arccos(a) */
gsl_complex gsl_complex_arccos_real(double a);  /* r=arccos(a) */
gsl_complex gsl_complex_arcsec(gsl_complex a);  /* r=arcsec(a) */
gsl_complex gsl_complex_arcsec_real(double a);  /* r=arcsec(a) */
gsl_complex gsl_complex_arccsc(gsl_complex a);  /* r=arccsc(a) */
gsl_complex gsl_complex_arccsc_real(double a);  /* r=arccsc(a) */
gsl_complex gsl_complex_arctan(gsl_complex a);  /* r=arctan(a) */
gsl_complex gsl_complex_arccot(gsl_complex a);  /* r=arccot(a) */
```

## 8 复双曲函数

```cpp
// gsl_complex_math.h
gsl_complex gsl_complex_sinh(gsl_complex a);  /* r=sinh(a) */
gsl_complex gsl_complex_cosh(gsl_complex a);  /* r=coshh(a) */
gsl_complex gsl_complex_sech(gsl_complex a);  /* r=sech(a) */
gsl_complex gsl_complex_csch(gsl_complex a);  /* r=csch(a) */
gsl_complex gsl_complex_tanh(gsl_complex a);  /* r=tanh(a) */
gsl_complex gsl_complex_coth(gsl_complex a);  /* r=coth(a) */
```

## 9 复反双曲函数

```cpp
// gsl_complex_math.h
gsl_complex gsl_complex_arcsinh(gsl_complex a);  /* r=arcsinh(a) */
gsl_complex gsl_complex_arccosh(gsl_complex a);  /* r=arccosh(a) */
gsl_complex gsl_complex_arccosh_real(double a);  /* r=arccosh(a) */
gsl_complex gsl_complex_arcsech(gsl_complex a);  /* r=arcsech(a) */
gsl_complex gsl_complex_arccsch(gsl_complex a);  /* r=arccsch(a) */
gsl_complex gsl_complex_arctanh(gsl_complex a);  /* r=arctanh(a) */
gsl_complex gsl_complex_arctanh_real(double a);  /* r=arctanh(a) */
gsl_complex gsl_complex_arccoth(gsl_complex a);  /* r=arccoth(a) */
```

## 10 参考
[https://www.gnu.org/software/gsl/doc/html/complex.html](https://www.gnu.org/software/gsl/doc/html/complex.html)
