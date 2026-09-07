---
aliases:
  - 离散傅里叶分析
  - DTFT DFS DFT FFT
tags:
  - 信号与系统
  - DTFT
  - DFT
  - FFT
source_lessons:
  - P32
  - P33
  - P35
  - P37
status: 已修订
---

# DTFT、DFS、DFT 与 FFT

> [!summary] 最重要的区分
> DTFT、DFS、DFT 是针对不同信号模型定义的变换；FFT 只是快速计算 DFT 的算法。四者不能当作同义词。

## 1. 四种连续/离散组合

| 名称 | 时域 | 频域 | 典型用途 |
|---|---|---|---|
| 连续时间傅里叶级数 CTFS | 连续、周期 | 离散 | 连续周期信号 |
| 连续时间傅里叶变换 CTFT | 连续、非周期 | 连续 | 连续非周期信号 |
| 离散时间傅里叶变换 DTFT | 离散、通常非周期 | 连续且 $2\pi$ 周期 | 无限离散序列 |
| 离散傅里叶级数 DFS | 离散、周期 | 离散且周期 | 离散周期序列 |
| 离散傅里叶变换 DFT | 有限 $N$ 点 | 有限 $N$ 点 | 计算机数值处理 |

> [!warning] 对字幕标题的修订
> P32 标题中的“离散傅里叶变换”实际主要讲的是 **DTFT**；P35 同时涉及 DFS、DFT/FFT。中文口语里常省略“时间”二字，但整理公式时必须分清。

## 2. DTFT

### 正变换

$$
\boxed{
X(e^{j\omega})
=\sum_{n=-\infty}^{\infty}
x[n]e^{-j\omega n}}
$$

### 反变换

$$
\boxed{
x[n]=\frac1{2\pi}
\int_{-\pi}^{\pi}
X(e^{j\omega})e^{j\omega n}\,d\omega}
$$

积分区间可以换成任意长度为 $2\pi$ 的区间。

### 周期性

$$
X(e^{j(\omega+2\pi k)})
=X(e^{j\omega}),
\qquad k\in\mathbb Z.
$$

因为对整数 $n$，

$$
e^{-j2\pi kn}=1.
$$

## 3. 常用 DTFT 变换对

| 序列 | DTFT |
|---|---|
| $\delta[n]$ | $1$ |
| $\delta[n-n_0]$ | $e^{-j\omega n_0}$ |
| $a^nu[n],\ |a|<1$ | $\dfrac1{1-ae^{-j\omega}}$ |
| 长度 $N$ 的矩形 $u[n]-u[n-N]$ | $\dfrac{1-e^{-j\omega N}}{1-e^{-j\omega}}$ |

矩形序列也可写成线性相位形式：

$$
\sum_{n=0}^{N-1}e^{-j\omega n}
=e^{-j\omega(N-1)/2}
\frac{\sin(N\omega/2)}{\sin(\omega/2)}.
$$

## 4. DTFT 的常用性质

时移：

$$
x[n-n_0]
\xleftrightarrow{\mathrm{DTFT}}
e^{-j\omega n_0}X(e^{j\omega}).
$$

频移：

$$
x[n]e^{j\omega_0n}
\xleftrightarrow{\mathrm{DTFT}}
X(e^{j(\omega-\omega_0)}).
$$

卷积：

$$
x[n]*h[n]
\xleftrightarrow{\mathrm{DTFT}}
X(e^{j\omega})H(e^{j\omega}).
$$

序列乘积：

$$
x[n]h[n]
\xleftrightarrow{\mathrm{DTFT}}
\frac1{2\pi}
\int_{-\pi}^{\pi}
X(e^{j\theta})H(e^{j(\omega-\theta)})\,d\theta.
$$

## 5. DFS：离散周期序列

设 $x_p[n]$ 以 $N$ 为周期。DFS 分析式：

$$
\boxed{
a[k]=\frac1N
\sum_{n=0}^{N-1}
x_p[n]e^{-j2\pi kn/N}}
$$

综合式：

$$
\boxed{
x_p[n]=
\sum_{k=0}^{N-1}
a[k]e^{j2\pi kn/N}}
$$

$x_p[n]$ 与 $a[k]$ 都以 $N$ 为周期。

## 6. DFT：有限长度数据

给定 $N$ 点序列 $x[0],\ldots,x[N-1]$，DFT 定义为

$$
\boxed{
X[k]=\sum_{n=0}^{N-1}
x[n]e^{-j2\pi kn/N}},
\qquad k=0,\ldots,N-1.
$$

IDFT：

$$
\boxed{
x[n]=\frac1N
\sum_{k=0}^{N-1}
X[k]e^{j2\pi kn/N}}.
$$

DFT 与 DFS 的关系是

$$
X[k]=N\,a[k].
$$

DFT 也等于 DTFT 在

$$
\omega_k=\frac{2\pi k}{N}
$$

处的 $N$ 个等间隔样本。

## 7. DFT 隐含的周期延拓

虽然输入只给出 $N$ 点，DFT 运算把它视为一个周期的样本。因此：

- 时域移位默认是循环移位；
- 频域相乘对应 $N$ 点循环卷积；
- 序列端点不连续会造成频谱泄漏。

这不是算法错误，而是 DFT 的数学模型。

## 8. 频率刻度

采样率为 $f_s$ 时：

$$
f_k=\frac{k}{N}f_s.
$$

未移位 DFT 中：

- $k=0$ 是直流；
- $0<k<N/2$ 是正频率；
- $N/2<k<N$ 对应负频率；
- 偶数 $N$ 的 $k=N/2$ 是奈奎斯特频点。

经 **fftshift** 后可使用

$$
f_k=\left(k-\frac N2\right)\frac{f_s}{N}
$$

附近的对称刻度。

## 9. FFT

直接计算 DFT 约需 $O(N^2)$ 次运算。基 2 FFT 把序列拆成偶、奇下标：

$$
X[k]=E[k]+W_N^kO[k],
$$

$$
X[k+N/2]=E[k]-W_N^kO[k],
$$

其中

$$
W_N=e^{-j2\pi/N}.
$$

递归分解把复杂度降为

$$
O(N\log_2N).
$$

> [!important] FFT 的输出
> FFT 算出的就是 DFT。它不会改变 DFT 的周期延拓、频率栅格或循环卷积性质。

## 10. 频谱泄漏与加窗

截取有限数据相当于原信号乘时间窗，频域中会与窗函数频谱卷积。若观测区间没有包含整数个周期，能量会泄漏到相邻频点。

常用改进：

- 增加观测时长，提高频率分辨率；
- 选择 Hann、Hamming 等窗以降低旁瓣；
- 区分“补零让曲线更密”和“增加数据真正提高分辨率”。

下一篇：[[信号与系统课程笔记/08 FFT 卷积、二维变换与 MATLAB 实验|FFT 卷积、二维变换与 MATLAB 实验]]。

