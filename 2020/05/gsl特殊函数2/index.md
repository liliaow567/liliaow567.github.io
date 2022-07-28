# GSL 系列 4 — 特殊函数 2 — 艾里 (Airy) 函数


<!--more-->

## 1 写在前面
对以下变量参见：[变量定义]({{< relref "posts/GSL特殊函数1.md#变量定义" >}})

`gsl_sf_result * result`

`gsl_mode_t mode`

## 2 头文件
若无特别说明，本篇代码均来自头文件 `gsl_sf_airy.h`

## 3 艾里 (Airy) 函数和导数

### 基本介绍[^1]

#### 艾里方程（斯托克斯方程）

$$y^{\prime \prime}-x y=0$$

#### 艾里函数
此方程有两个线性独立解：

$$\operatorname{Ai}(x)=\frac{1}{\pi} \int_{0}^{\infty} \cos \left(\frac{t^{3}}{3}+x t\right) d t$$
$$\mathrm{Bi}(x)=\frac{1}{\pi} \int_{0}^{\infty} e^{\left(-\frac{t^{3}}{3}+x t\right)}+\sin \left(\frac{t^{3}}{3}+x t\right) d t$$

#### 艾里函数正极限
当 $x$ 趋于正无穷，

$$\operatorname{Ai}(x) \sim \frac{e^{-\frac{2}{3} x^{3 / 2}}}{2 \sqrt{\pi} x^{1 / 4}} $$

$$\operatorname{Bi}(x) \sim \frac{e^{\frac{2}{3} x^{3 / 2}}}{\sqrt{\pi} x^{1 / 4}}$$

### 计算函数

```cpp
int gsl_sf_airy_Ai_e(const double x, const gsl_mode_t mode, gsl_sf_result * result);
double gsl_sf_airy_Ai(const double x, gsl_mode_t mode);

int gsl_sf_airy_Bi_e(const double x, gsl_mode_t mode, gsl_sf_result * result);
double gsl_sf_airy_Bi(const double x, gsl_mode_t mode);

/* scaled Ai(x):
 *                     Ai(x)   x < 0
 *   exp(+2/3 x^{3/2}) Ai(x)   x > 0
 *
 * exceptions: none
 */
int gsl_sf_airy_Ai_scaled_e(const double x, gsl_mode_t mode, gsl_sf_result * result);
double gsl_sf_airy_Ai_scaled(const double x, gsl_mode_t mode);


/* scaled Bi(x):
 *                     Bi(x)   x < 0
 *   exp(-2/3 x^{3/2}) Bi(x)   x > 0
 *
 * exceptions: none
 */
int gsl_sf_airy_Bi_scaled_e(const double x, gsl_mode_t mode, gsl_sf_result * result);
double gsl_sf_airy_Bi_scaled(const double x, gsl_mode_t mode);
```

### 计算导数

```cpp
int gsl_sf_airy_Ai_deriv_e(const double x, gsl_mode_t mode, gsl_sf_result * result);
double gsl_sf_airy_Ai_deriv(const double x, gsl_mode_t mode);

int gsl_sf_airy_Bi_deriv_e(const double x, gsl_mode_t mode, gsl_sf_result * result);
double gsl_sf_airy_Bi_deriv(const double x, gsl_mode_t mode);

/* scaled derivative Ai'(x):
 *                     Ai'(x)   x < 0
 *   exp(+2/3 x^{3/2}) Ai'(x)   x > 0
 *
 * exceptions: none
 */
int gsl_sf_airy_Ai_deriv_scaled_e(const double x, gsl_mode_t mode, gsl_sf_result * result);
double gsl_sf_airy_Ai_deriv_scaled(const double x, gsl_mode_t mode);


/* scaled derivative:
 *                     Bi'(x)   x < 0
 *   exp(-2/3 x^{3/2}) Bi'(x)   x > 0
 *
 * exceptions: none
 */
int gsl_sf_airy_Bi_deriv_scaled_e(const double x, gsl_mode_t mode, gsl_sf_result * result);
double gsl_sf_airy_Bi_deriv_scaled(const double x, gsl_mode_t mode);
```

### 计算零点
计算艾里函数的第 $s$ 个零点
```cpp
/* Zeros of Ai(x)
 */
int gsl_sf_airy_zero_Ai_e(unsigned int s, gsl_sf_result * result);
double gsl_sf_airy_zero_Ai(unsigned int s);


/* Zeros of Bi(x)
 */
int gsl_sf_airy_zero_Bi_e(unsigned int s, gsl_sf_result * result);
double gsl_sf_airy_zero_Bi(unsigned int s);
```
计算艾里函数导数的第 $s$ 个零点

```cpp
/* Zeros of Ai'(x)
 */
int gsl_sf_airy_zero_Ai_deriv_e(unsigned int s, gsl_sf_result * result);
double gsl_sf_airy_zero_Ai_deriv(unsigned int s);


/* Zeros of Bi'(x)
 */
int gsl_sf_airy_zero_Bi_deriv_e(unsigned int s, gsl_sf_result * result);
double gsl_sf_airy_zero_Bi_deriv(unsigned int s);
```

## 4 参考
[^1]: [https://zh.wikipedia.org/wiki/%E8%89%BE%E9%87%8C%E5%87%BD%E6%95%B0](https://zh.wikipedia.org/wiki/%E8%89%BE%E9%87%8C%E5%87%BD%E6%95%B0)
