# GSL 系列 4 — 特殊函数 4 — 其他特殊函数


<!--more-->


## 1 写在前面
对以下变量参见：[变量定义]({{< relref "posts/GSL特殊函数1.md#变量定义" >}})

` gsl_sf_result * result`

`gsl_mode_t mode`

`gsl_sf_result_e10 * result`


## 2 本篇包含的特殊函数
1. 克劳森 (Clausen) 函数
2. 道森 (Dawson) 函数
3. 德拜 (Debye) 函数
4. 双重对数 (Dilogarithm)
5. 误差 (Error) 函数
6. 费米—狄拉克 (Fermi-Dirac) 函数

## 3 克劳森 (Clausen) 函数
### 基本公式

{{<raw>}}
$$\mathrm{Cl}_{2}(x)=-\int_{0}^{x} \log \left|2 \sin \frac{t}{2}\right| d t$$
{{</raw>}}

### 计算
头文件：`gsl_sf_clausen.h`

```cpp
int gsl_sf_clausen_e(double x, gsl_sf_result * result);
double gsl_sf_clausen(const double x);
```

## 4 道森 (Dawson) 函数
### 基本公式

$$D_{+}(x)=e^{-x^{2}} \int_{0}^{x} e^{t^{2}} d t$$

### 计算
头文件：`gsl_sf_dawson.h`

```cpp
int     gsl_sf_dawson_e(double x, gsl_sf_result * result);
double     gsl_sf_dawson(double x);
```

## 5 德拜 (Debye) 函数
### 基本公式

$$D_{n}(x)=\frac{n}{x^{n}} \int_{0}^{x} \frac{t^{n}}{e^{t}-1} d t$$

$n$ 为阶数

### 计算
头文件：`gsl_sf_debye.h`

```cpp
// 一阶
int     gsl_sf_debye_1_e(const double x, gsl_sf_result * result);
double     gsl_sf_debye_1(const double x);

// 2 阶
int     gsl_sf_debye_2_e(const double x, gsl_sf_result * result);
double     gsl_sf_debye_2(const double x);

// 3 阶
int     gsl_sf_debye_3_e(const double x, gsl_sf_result * result);
double     gsl_sf_debye_3(const double x);

// 4 阶
int     gsl_sf_debye_4_e(const double x, gsl_sf_result * result);
double     gsl_sf_debye_4(const double x);

// 5 阶
int     gsl_sf_debye_5_e(const double x, gsl_sf_result * result);
double     gsl_sf_debye_5(const double x);

// 6 阶
int     gsl_sf_debye_6_e(const double x, gsl_sf_result * result);
double     gsl_sf_debye_6(const double x);
```
## 6 双重对数 (Dilogarithm)
### 基本公式

{{<raw>}}
$$\mathrm{Li}_{2}(z)=-\int_{0}^{z} \frac{\ln (1-u)}{u} d u, z \in \mathbb{C}$$
{{</raw>}}

### 计算
头文件：`gsl_sf_dilog.h`

```cpp
// 实变量计算
int     gsl_sf_dilog_e(const double x, gsl_sf_result * result);
double  gsl_sf_dilog(const double x);

//复变量计算
// 输入 x, y, 笛卡尔坐标
int
gsl_sf_complex_dilog_xy_e(
  const double x,
  const double y,
  gsl_sf_result * result_re,
  gsl_sf_result * result_im
  );
// 输入 r, theta, 极坐标
// 头文件中提到此函数不推荐使用
int
gsl_sf_complex_dilog_e(
  const double r,
  const double theta,
  gsl_sf_result * result_re,
  gsl_sf_result * result_im
  );
// 计算 Li_2(1-z)
int
gsl_sf_complex_spence_xy_e(
  const double x,
  const double y,
  gsl_sf_result * real_sp,
  gsl_sf_result * imag_sp
  );
```
## 7 误差 (Error) 函数
### 基本公式
1. 误差函数

$$\operatorname{erf}( x)=\frac{2}{\sqrt{\pi}} \int_{0}^{x} e^{-t^{2}} d t$$

2. 误差补函数

$$\operatorname{erfc}( x) = 1-\operatorname{erf}( x)$$

3. 对数误差补函数

$$\operatorname{ln}(\operatorname{erfc}( x))$$

4. 标准正态分布概率密度函数

$$Z(x)=\frac{1}{\sqrt{2 \pi}} e^{-\frac{1}{2} x^{2}}$$

5. Q 函数

$$Q(x)=\frac{1}{\sqrt{2 \pi}} \int_{x}^{\infty} \exp \left(-\frac{u^{2}}{2}\right) d u=\frac{1}{2}\operatorname{erfc}(\frac{x}{\sqrt{2}})$$

6. 危险函数

$$H(x)=\frac{Z(x)}{Q(x)}=\sqrt{\frac{2}{\pi}}\frac{e^{-\frac{1}{2} x^{2}}}{\operatorname{erfc}(\frac{x}{\sqrt{2}})}$$

### 计算
头文件：`gsl_sf_erf.h`

```cpp
// erfc
int gsl_sf_erfc_e(double x, gsl_sf_result * result);
double gsl_sf_erfc(double x);
// ln erfc
int gsl_sf_log_erfc_e(double x, gsl_sf_result * result);
double gsl_sf_log_erfc(double x);
// erf
int gsl_sf_erf_e(double x, gsl_sf_result * result);
double gsl_sf_erf(double x);
// Z, Q
int gsl_sf_erf_Z_e(double x, gsl_sf_result * result);
int gsl_sf_erf_Q_e(double x, gsl_sf_result * result);
double gsl_sf_erf_Z(double x);
double gsl_sf_erf_Q(double x);
// H
int gsl_sf_hazard_e(double x, gsl_sf_result * result);
double gsl_sf_hazard(double x);
```

## 8 费米—狄拉克 (Fermi-Dirac) 函数
### 基本公式
1. 完全费米—狄拉克积分

$$F_{j}(x)=\frac{1}{\Gamma(j+1)} \int_{0}^{\infty} \frac{t^{j}}{e^{t-x}+1} d t$$

2. 非完全费米—狄拉克积分

$$F_{j}(x, b)=\frac{1}{\Gamma(j+1)} \int_{b}^{\infty} \frac{t^{j}}{\exp (t-x)+1} d t$$

### 计算
头文件：`gsl_sf_fermi_dirac.h`

```cpp
// 完全
// j = -1  F_{-1}(x) = e^x / (1 + e^x)
int     gsl_sf_fermi_dirac_m1_e(const double x, gsl_sf_result * result);
double     gsl_sf_fermi_dirac_m1(const double x);
// j = 0   F_0(x) = ln(1 + e^x)
int     gsl_sf_fermi_dirac_0_e(const double x, gsl_sf_result * result);
double     gsl_sf_fermi_dirac_0(const double x);
// j = 1  
int     gsl_sf_fermi_dirac_1_e(const double x, gsl_sf_result * result);
double     gsl_sf_fermi_dirac_1(const double x);
// j = 2
int     gsl_sf_fermi_dirac_2_e(const double x, gsl_sf_result * result);
double     gsl_sf_fermi_dirac_2(const double x);
// 整数 j
int     gsl_sf_fermi_dirac_int_e(const int j, const double x, gsl_sf_result * result);
double     gsl_sf_fermi_dirac_int(const int j, const double x);
// j = -1/2
int     gsl_sf_fermi_dirac_mhalf_e(const double x, gsl_sf_result * result);
double     gsl_sf_fermi_dirac_mhalf(const double x);
// j = 1/2
int     gsl_sf_fermi_dirac_half_e(const double x, gsl_sf_result * result);
double     gsl_sf_fermi_dirac_half(const double x);
// j = 3/2
int     gsl_sf_fermi_dirac_3half_e(const double x, gsl_sf_result * result);
double     gsl_sf_fermi_dirac_3half(const double x);
// 非完全
// j = 0
int     gsl_sf_fermi_dirac_inc_0_e(const double x, const double b, gsl_sf_result * result);
double     gsl_sf_fermi_dirac_inc_0(const double x, const double b);
```
