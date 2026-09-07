---
aliases:
  - Simulink Transfer Function
  - 传递函数框图实现
tags:
  - 工程数学
  - Simulink
  - 传递函数
  - 控制理论
source: "【工程数学基础】8_如何在Matlab Simulink 搭建传递函数？？Transfer Function—【DR_CAN】—【2019-08-27】-中文"
status: 已修订
---

# 用 Simulink 搭建传递函数

> [!summary] 核心方法
> 先把传递函数还原为微分方程，再用积分器、增益和求和模块复现方程。积分器的输出作为状态，可以避免直接对输入做数值微分。

## 1. 示例传递函数

字幕中的目标模型可整理为

$$
H(s)=\frac{Y(s)}{U(s)}
=\frac{B_0+B_1s}{s+A_0}.
$$

交叉相乘：

$$
(s+A_0)Y(s)=(B_0+B_1s)U(s).
$$

在零初始条件下做拉普拉斯逆变换：

$$
\boxed{\dot y+A_0y=B_0u+B_1\dot u}.
$$

> [!note] 零初始条件
> 直接用 $sY(s)\leftrightarrow\dot y(t)$ 时默认初始条件为零。若初始状态不为零，拉普拉斯变换中还会出现初值项。

## 2. 避免直接微分输入

直接用 Derivative 模块计算 $\dot u$ 往往会放大噪声。重排方程：

$$
\dot y-B_1\dot u=B_0u-A_0y.
$$

令

$$
x=y-B_1u,
$$

则

$$
\dot x=B_0u-A_0y,
$$

并且

$$
y=x+B_1u.
$$

继续消去 $y$，可得标准状态形式：

$$
\boxed{
\begin{aligned}
\dot x&=-A_0x+(B_0-A_0B_1)u,\\
y&=x+B_1u.
\end{aligned}}
$$

其状态空间矩阵为

$$
A=-A_0,
\qquad
B=B_0-A_0B_1,
\qquad
C=1,
\qquad
D=B_1.
$$

## 3. 用基础模块搭建

需要的 Simulink 模块：

- **Integrator**：实现 $1/s$；
- **Gain**：实现 $A_0$、$B_0$、$B_1$ 等系数；
- **Sum**：实现加减法；
- 输入源与 Scope。

按 $x=y-B_1u$ 的形式搭建：

1. 用求和模块计算 $B_0u-A_0y$；
2. 将其送入 Integrator，输出状态 $x$；
3. 计算直通项 $B_1u$；
4. 相加得到 $y=x+B_1u$；
5. 把 $y$ 反馈到第一步的 $-A_0y$ 支路。

对应的信息流是：

```text
u ──×B0──┐
         ├─ Σ ─ Integrator ─ x ─┐
y ──×A0──┘  (+,−)              ├─ Σ ─ y
u ─────────────×B1──────────────┘
```

## 4. 验证模型

建议同时放置一个 **Transfer Fcn** 模块作为基准：

$$
\text{Numerator}=[B_1,\ B_0],
\qquad
\text{Denominator}=[1,\ A_0].
$$

向手工模型和 Transfer Fcn 模块输入同一个信号，比较两者输出。若参数、初值和求解器设置一致，输出应重合。

## 5. 推广到高阶系统

一般 $n$ 阶微分方程可使用积分器链：

$$
y^{(n)}\xrightarrow{1/s}y^{(n-1)}\xrightarrow{1/s}\cdots\xrightarrow{1/s}\dot y\xrightarrow{1/s}y.
$$

把方程整理成最高阶导数的显式形式，再将各状态乘以对应系数反馈到求和点。这实际上是在构造一种状态空间实现。

## 6. 常见问题

- **分子、分母系数顺序**：Simulink 按 $s$ 的降幂排列；
- **代数环**：若反馈路径中完全没有动态元件，可能形成 algebraic loop；
- **输入微分**：会放大阶跃不连续与测量噪声，应优先改写状态；
- **初始条件**：应在 Integrator 的 Initial condition 中设置；
- **非真有理传递函数**：若分子阶数高于分母，不能只靠普通因果积分器实现。

相关笔记：[[信号与系统课程笔记/11 拉普拉斯变换与收敛域#卷积定理的积分证明|卷积定理与拉普拉斯变换]]、[[工程数学与动态系统笔记/10 动态系统建模与分析导论|动态系统建模与分析导论]]。
