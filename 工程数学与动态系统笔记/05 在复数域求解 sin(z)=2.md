---
aliases:
  - sin(z)=2
  - 复数三角方程
tags:
  - 工程数学
  - 复变函数
  - 欧拉公式
source: "【工程数学基础】6_SinX=2_ 复变函数 欧拉公式—【DR_CAN】—【2018-09-23】-中文"
status: 已修订
---

# 在复数域求解 $\sin z=2$

> [!summary] 结论
> 在实数范围内 $\sin x=2$ 无解，但在复数范围内有无穷多解：
> $$
> z=\frac\pi2+2k\pi\pm i\ln(2+\sqrt3),
> \qquad k\in\mathbb Z.
> $$

## 1. 为什么要进入复数域

对实数 $x$，

$$
-1\le \sin x\le1,
$$

所以 $\sin x=2$ 不可能成立。但复正弦函数没有这个幅值限制。

> [!warning] 字幕纠错
> 自动字幕多次把题目识别成“3X=2”。正确题目是 $\sin z=2$；虚数单位满足 $i^2=-1$。

## 2. 复正弦的实部与虚部

令

$$
z=x+iy,
\qquad x,y\in\mathbb R.
$$

由 [[工程数学与动态系统笔记/04 欧拉公式及其证明|欧拉公式]] 可得

$$
\sin z=\frac{e^{iz}-e^{-iz}}{2i}.
$$

展开后得到常用公式

$$
\boxed{\sin(x+iy)=\sin x\cosh y+i\cos x\sinh y}.
$$

## 3. 先解一般问题 $\sin z=c$

设 $c>1$ 且为实数。比较实部与虚部：

$$
\begin{cases}
\sin x\cosh y=c,\\
\cos x\sinh y=0.
\end{cases}
$$

因为 $c>1$，不可能有 $y=0$；否则第一式退化为 $\sin x=c$。因此必须有

$$
\cos x=0.
$$

又因为 $c>0$ 且 $\cosh y>0$，需要 $\sin x=1$，所以

$$
x=\frac\pi2+2k\pi,
\qquad k\in\mathbb Z.
$$

第一式变为

$$
\cosh y=c.
$$

利用

$$
\cosh y=\frac{e^y+e^{-y}}2,
$$

令 $u=e^y>0$：

$$
u^2-2cu+1=0.
$$

于是

$$
u=c\pm\sqrt{c^2-1}.
$$

两根互为倒数，对应

$$
y=\pm\ln\left(c+\sqrt{c^2-1}\right)
=\pm\operatorname{arcosh}c.
$$

因此一般解为

$$
\boxed{
z=\frac\pi2+2k\pi
\pm i\ln\left(c+\sqrt{c^2-1}\right),
\quad k\in\mathbb Z.}
$$

## 4. 代入 $c=2$

$$
\boxed{
z=\frac\pi2+2k\pi
\pm i\ln(2+\sqrt3),
\quad k\in\mathbb Z.}
$$

验证时注意：$\cosh$ 是偶函数，所以正负两个虚部给出相同的实值 $2$。

## 5. 另一种写法

因为

$$
2-\sqrt3=\frac1{2+\sqrt3},
$$

所以

$$
\ln(2-\sqrt3)=-\ln(2+\sqrt3).
$$

字幕中出现的 $\ln(2\pm\sqrt3)$ 与上面的 $\pm\ln(2+\sqrt3)$ 等价，但后一种写法更清楚地展示了解关于实轴成共轭对称。

相关笔记：[[工程数学与动态系统笔记/03 复数的三种表示与共轭|复数与共轭]]、[[工程数学与动态系统笔记/04 欧拉公式及其证明|欧拉公式]]。
