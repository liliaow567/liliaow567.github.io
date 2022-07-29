# GSL 系列 4 — 特殊函数 3 — 贝塞尔 (Bessel) 函数


<!--more-->

## 1 写在前面
对以下变量参见：[变量定义]({{< relref "posts/GSL特殊函数1.md#变量定义" >}})

`gsl_sf_result * result`

`gsl_mode_t mode`

`gsl_sf_result_e10 * result`


## 2 头文件
若无特别说明，本篇代码均来自头文件 `gsl_sf_bessel.h`

## 3 贝塞尔 (Bessel) 函数

### 基本介绍[^1]

#### 符号说明

$w^{\prime \prime} =  \frac{d^{2} w}{d z^{2}},w^{\prime}=\frac{d w}{d z}$

$z$ 为复数自变量

$\nu$ 为复数常量

$n$ 为整数

#### 3.1.2 贝塞尔函数

微分方程：

$$z^{2} \frac{d^{2} w}{d z^{2}}+z \frac{d w}{d z}+\left(z^{2}-\nu^{2}\right) {w}=0$$

通解构成：

**第一类贝塞尔函数：$J_{\pm \nu}(z)$**

**第二类贝塞尔函数（韦伯函数）：$Y_{\nu}(z)$**

**第三类贝塞尔函数（汉克尔函数）：$H_{\nu}^{(1)}(z)、H_{\nu}^{(2)}(z)$**

关系式：
$$J_{\nu}(z)=\left(\frac{1}{2} z\right)^{\nu} \sum_{k=0}^{\infty} \frac{\left(-\frac{1}{4} z^{2}\right)^{k}}{k ! \Gamma(\nu+k+1)}$$

$$Y_{\nu}(z)=\frac{J_{{\nu}}(z) \cos ({\nu} \pi)-{J}_{-{\nu}}({z})}{\sin ({\nu} \pi)}$$

{{<raw>}}
$$H_{\nu}^{(1)}(z)  \doteq J_{\nu}(z)+i Y_{\nu}(z) =i \csc (\nu \pi)\left\{e^{-\nu \pi i} J_{\nu}(z)-J_{-\nu}(z)\right\}$$

$$H_{\nu}^{(2)}(z)  \doteq J_{\nu}(z)-i Y_{\nu}(z) =i \csc (\nu \pi)\left\{J_{-\nu}(z)-e^{\nu \pi i} J_{\nu}(z)\right\}$$
{{</raw>}}

**阶数：**$\nu$

#### 修正贝塞尔函数

微分方程：

$$z^{2} \frac{d^{2} w}{d z^{2}}+z \frac{d w}{d z}-\left(z^{2}+\nu^{2}\right) w=0$$

通解构成：

**第一类修正贝塞尔函数：$I_{\pm \nu}(z)$**

**第二类修正贝塞尔函数：$K_{\nu}(z)$**

关系式：

$$I_{\nu}(z)=i^{-\nu} J_{\nu}(i z)$$

$$K_{\nu}(z)=\frac{1}{2} \pi \frac{I_{-\nu}(z)-I_{\nu}(z)}{\sin (\nu \pi)}$$

#### 球贝塞尔函数
微分方程：

$$z^{2} w^{\prime \prime}+2 z w^{\prime}+\left[z^{2}-n(n+1)\right] w=0$$

通解构成：

**第一类球贝塞尔函数：$j_n(z)$**

**第二类球贝塞尔函数：$y_n(z)$**

**第三类球贝塞尔函数：$h_n^{(1)}(z),h_n^{(2)}(z)$**

关系式：

$$j_{n}(z)=\sqrt{\frac{1}{2} \pi / z} J_{n+\frac{1}{2}}(z)$$

$$y_{n}(z)=\sqrt{\frac{1}{2} \pi / z} Y_{n+\frac{1}{2}}(z)$$

$$h_n^{(1)}(z) = j_n(z)+iy_n(z)$$

$$h_n^{(2)}(z) = j_n(z)-iy_n(z)$$

#### 修正球贝塞尔函数
微分方程：

$$z^{2} w^{\prime \prime}+2 z w^{\prime}-\left[z^{2}+n(n+1)\right] w=0$$

通解构成：

**第一类修正球贝塞尔函数：$\sqrt{\frac{1}{2} \pi / z} I_{n+\frac{1}{2}}(z)$**

**第二类修正球贝塞尔函数：$\sqrt{\frac{1}{2} \pi / z} I_{-n-\frac{1}{2}}(z)$**

**第三类修正球贝塞尔函数：$\sqrt{\frac{1}{2} \pi / z} K_{n+\frac{1}{2}}(z)$**

关系式：

$$\sqrt{\frac{1}{2} \pi / z} I_{n+\frac{1}{2}}(z) = i^{-n}j_n(iz)$$

$$\sqrt{\frac{1}{2} \pi / z} I_{-n-\frac{1}{2}}(z)=i^{-(n+1)}y_n(iz)$$

$$\sqrt{\frac{1}{2} \pi / z} K_{n+\frac{1}{2}}(z)=\frac{1}{2} \pi(-1)^{n+1} \sqrt{\frac{1}{2} \pi / z}\left[I_{n+\frac{1}{2}}(z)-I_{-n-\frac{1}{2}}(z)\right]$$

### 贝塞尔函数计算

```cpp
// 第一类 0 阶
int gsl_sf_bessel_J0_e(const double x,  gsl_sf_result * result);
double gsl_sf_bessel_J0(const double x);
// 第一类 1 阶
int gsl_sf_bessel_J1_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_J1(const double x);
// 第一类 n 阶
int gsl_sf_bessel_Jn_e(int n, double x, gsl_sf_result * result);
double gsl_sf_bessel_Jn(const int n, const double x);
// 第一类 计算 nmin 到 nmax 阶，结果存在 result_array 中
int gsl_sf_bessel_Jn_array(int nmin, int nmax, double x, double * result_array);
// 第一类 分数阶 nu
int gsl_sf_bessel_Jnu_e(const double nu, const double x, gsl_sf_result * result);
double gsl_sf_bessel_Jnu(const double nu, const double x);
// 第一类 分数阶 nu , 计算贝塞尔函数在一系列 x 的值（数组 v，有序和正的）
// 计算出来的结果放在数组 v, 数组 v 得到重写
int gsl_sf_bessel_sequence_Jnu_e(double nu, gsl_mode_t mode, size_t size, double * v);
// 第二类，和第一类类似
int gsl_sf_bessel_Y0_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_Y0(const double x);
int gsl_sf_bessel_Y1_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_Y1(const double x);
int gsl_sf_bessel_Yn_e(int n,const double x, gsl_sf_result * result);
double gsl_sf_bessel_Yn(const int n,const double x);
int gsl_sf_bessel_Yn_array(const int nmin, const int nmax, const double x, double * result_array);
int gsl_sf_bessel_Ynu_e(double nu, double x, gsl_sf_result * result);
double gsl_sf_bessel_Ynu(const double nu, const double x);
```
### 修正贝塞尔函数计算

```cpp
// 第一类修正
int gsl_sf_bessel_I0_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_I0(const double x);
int gsl_sf_bessel_I1_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_I1(const double x);
int gsl_sf_bessel_In_e(const int n, const double x, gsl_sf_result * result);
double gsl_sf_bessel_In(const int n, const double x);
int gsl_sf_bessel_In_array(const int nmin, const int nmax, const double x, double * result_array);
// 分数阶
int gsl_sf_bessel_Inu_e(double nu, double x, gsl_sf_result * result);
double gsl_sf_bessel_Inu(double nu, double x);

// 第一类修正 缩放
// exp(-|x|) I_0(x)
int gsl_sf_bessel_I0_scaled_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_I0_scaled(const double x);
// exp(-|x|) I_1(x)
int gsl_sf_bessel_I1_scaled_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_I1_scaled(const double x);
// exp(-|x|) I_n(x)
int gsl_sf_bessel_In_scaled_e(int n, const double x, gsl_sf_result * result);
double gsl_sf_bessel_In_scaled(const int n, const double x);
int gsl_sf_bessel_In_scaled_array(const int nmin, const int nmax, const double x, double * result_array);
// 分数阶 Exp[-|x|] BesselI[nu, x]  x >= 0, nu >= 0
int gsl_sf_bessel_Inu_scaled_e(double nu, double x, gsl_sf_result * result);
double gsl_sf_bessel_Inu_scaled(double nu, double x);

// 第二类修正
// x > 0.0
int gsl_sf_bessel_K0_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_K0(const double x);
int gsl_sf_bessel_K1_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_K1(const double x);
int gsl_sf_bessel_Kn_e(const int n, const double x, gsl_sf_result * result);
double gsl_sf_bessel_Kn(const int n, const double x);
int gsl_sf_bessel_Kn_array(const int nmin, const int nmax, const double x, double * result_array);
// 分数阶 BesselK[nu, x]  x > 0, nu >= 0
int gsl_sf_bessel_Knu_e(const double nu, const double x, gsl_sf_result * result);
double gsl_sf_bessel_Knu(const double nu, const double x);
// 自然对数，分数阶 ln[BesselK[nu, x]]  x > 0, nu >= 0
int gsl_sf_bessel_lnKnu_e(const double nu, const double x, gsl_sf_result * result);
double gsl_sf_bessel_lnKnu(const double nu, const double x);

// 第二类修正 缩放
// exp(x) K_0(x)    x > 0.0
int gsl_sf_bessel_K0_scaled_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_K0_scaled(const double x);
// exp(x) K_1(x)    x > 0.0
int gsl_sf_bessel_K1_scaled_e(const double x, gsl_sf_result * result); 
double gsl_sf_bessel_K1_scaled(const double x);
// exp(x) K_n(x)    x > 0.0
int gsl_sf_bessel_Kn_scaled_e(int n, const double x, gsl_sf_result * result);
double gsl_sf_bessel_Kn_scaled(const int n, const double x);
int gsl_sf_bessel_Kn_scaled_array(const int nmin, const int nmax, const double x, double * result_array);
// 分数阶  Exp[+|x|] BesselK[nu, x]  x > 0, nu >= 0
int gsl_sf_bessel_Knu_scaled_e(const double nu, const double x, gsl_sf_result * result);
double gsl_sf_bessel_Knu_scaled(const double nu, const double x);
int gsl_sf_bessel_Knu_scaled_e10_e(const double nu, const double x, gsl_sf_result_e10 * result);
```
### 球贝塞尔函数计算

```cpp
// 第一类 球
// j_0(x) = sin(x)/x
int gsl_sf_bessel_j0_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_j0(const double x);
// j_1(x) = (sin(x)/x - cos(x))/x
int gsl_sf_bessel_j1_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_j1(const double x);
// j_2(x) = ((3/x^2 - 1)sin(x) - 3cos(x)/x)/x
int gsl_sf_bessel_j2_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_j2(const double x);
// j_l(x)
int gsl_sf_bessel_jl_e(const int l, const double x, gsl_sf_result * result);
double gsl_sf_bessel_jl(const int l, const double x);
// j_l(x) for l=0,1,...,lmax  l >= 0, x >= 0.0
int gsl_sf_bessel_jl_array(const int lmax, const double x, double * result_array);
// j_l(x) for l=0,1,...,lmax      Uses Steed's method.
int gsl_sf_bessel_jl_steed_array(const int lmax, const double x, double * jl_x_array);

// 第二类 球
int gsl_sf_bessel_y0_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_y0(const double x);
int gsl_sf_bessel_y1_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_y1(const double x);
int gsl_sf_bessel_y2_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_y2(const double x);
int gsl_sf_bessel_yl_e(int l, const double x, gsl_sf_result * result);
double gsl_sf_bessel_yl(const int l, const double x);
int gsl_sf_bessel_yl_array(const int lmax, const double x, double * result_array);
```

### 修正球贝塞尔函数计算

```cpp
// 第一类 修正球 缩放
// Exp[-|x|] i_0(x)，这里 i 代表第一类修正球
int gsl_sf_bessel_i0_scaled_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_i0_scaled(const double x);
// Exp[-|x|] i_2(x)
int gsl_sf_bessel_i2_scaled_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_i2_scaled(const double x);
// Exp[-|x|] i_l(x) ，l >= 0
// i_l(x) = Sqrt[Pi/(2x)] BesselI[l+1/2,x] 使用 第一类修正 计算
int gsl_sf_bessel_il_scaled_e(const int l, double x, gsl_sf_result * result);
double gsl_sf_bessel_il_scaled(const int l, const double x);
// Exp[-|x|] i_l(x) for l=0,1,...,lmax
int gsl_sf_bessel_il_scaled_array(const int lmax, const double x, double * result_array);

// 第二类 修正球 缩放
// Exp[x] k_0(x)   x > 0.0
int gsl_sf_bessel_k0_scaled_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_k0_scaled(const double x);
// Exp[x] k_1(x)   x > 0.0
int gsl_sf_bessel_k1_scaled_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_k1_scaled(const double x);
// Exp[x] k_2(x)   x > 0.0
int gsl_sf_bessel_k2_scaled_e(const double x, gsl_sf_result * result);
double gsl_sf_bessel_k2_scaled(const double x);
// Exp[x] k_l[x]   k_l(x) = Sqrt[Pi/(2x)] BesselK[l+1/2,x]
int gsl_sf_bessel_kl_scaled_e(int l, const double x, gsl_sf_result * result);
double gsl_sf_bessel_kl_scaled(const int l, const double x);
// Exp[x] k_l(x)  for l=0,1,...,lmax
int gsl_sf_bessel_kl_scaled_array(const int lmax, const double x, double * result_array);
```

### 第一类贝塞尔函数的零点

```cpp
// 第一类 0 阶 贝塞尔函数 的 第 s 个 正零点
int gsl_sf_bessel_zero_J0_e(unsigned int s, gsl_sf_result * result);
double gsl_sf_bessel_zero_J0(unsigned int s);
// 第一类 1 阶 贝塞尔函数 的 第 s 个 正零点
int gsl_sf_bessel_zero_J1_e(unsigned int s, gsl_sf_result * result);
double gsl_sf_bessel_zero_J1(unsigned int s);
// 第一类 nu 阶 贝塞尔函数 的 第 s 个 正零点
int gsl_sf_bessel_zero_Jnu_e(double nu, unsigned int s, gsl_sf_result * result);
double gsl_sf_bessel_zero_Jnu(double nu, unsigned int s);
```

## 4 参考
[^1]: Milton Abramowitz and Irene A. Stegun, eds., Handbook of Mathematical Functions with Formulas, Graphs, and Mathematical Tables (Dover: New York, 1972) 
