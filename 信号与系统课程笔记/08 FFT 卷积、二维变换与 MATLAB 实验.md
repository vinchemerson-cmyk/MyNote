---
aliases:
  - MATLAB 信号处理实验
  - FFT Convolution
  - 二维傅里叶变换
tags:
  - 信号与系统
  - MATLAB
  - FFT
  - 二维傅里叶变换
source_lessons:
  - P8
  - P14
  - P28
  - P29
  - P36
  - P37
  - P42
status: 已修订
---

# FFT 卷积、二维变换与 MATLAB 实验

> [!summary] 实践主线
> 数值程序处理的是有限采样数据。写代码时必须同时记录采样率、时间起点、序列长度和 DFT 的周期模型。

## 1. 离散线性卷积

~~~matlab
x = [1 2 3];
h = [4 5 6];
y = conv(x, h);
~~~

若长度分别为 $L_x,L_h$，线性卷积长度为

$$
L_y=L_x+L_h-1.
$$

## 2. 用 FFT 计算线性卷积

DFT 频域相乘天然产生循环卷积。要得到线性卷积，必须选择

$$
N\ge L_x+L_h-1
$$

并把两个序列都补零到 $N$ 点：

~~~matlab
x = [1 2 3];
h = [4 5 6];

N = length(x) + length(h) - 1;
y_fft = ifft(fft(x, N) .* fft(h, N));
y_ref = conv(x, h);

max_error = max(abs(y_fft - y_ref));
~~~

浮点误差可能使实数结果带极小虚部，可在确认输入、理论输出均为实数后使用 **real(y_fft)**。

> [!warning] 只补到较长序列长度不够
> 若 $N<L_x+L_h-1$，线性卷积尾部会绕回前部，产生时域混叠。

## 3. 连续卷积的数值近似

若连续信号以时间步长 $\Delta t$ 采样，则积分要带 $\Delta t$：

~~~matlab
dt = 1e-3;
t = 0:dt:2;
x = exp(-t);
h = exp(-2*t);

y = conv(x, h) * dt;
ty = (0:length(y)-1) * dt;
~~~

缺少 $\Delta t$ 缩放会导致幅值随采样率变化。

## 4. 音频频谱

~~~matlab
[x, fs] = audioread("voice.wav");
x = mean(x, 2);                 % 转单声道

N = length(x);
w = hann(N);
X = fft(x .* w);

K = floor(N/2) + 1;
f = (0:K-1) * fs / N;
mag = abs(X(1:K)) / sum(w);     % 按窗的相干增益归一化

plot(f, 20*log10(mag + eps));
xlabel("Frequency (Hz)");
ylabel("Magnitude (dB)");
grid on;
~~~

读图时注意：

- 频率分辨率 $\Delta f=f_s/N$；
- 最高无混叠频率是 $f_s/2$；
- 加窗降低旁瓣，但会加宽主瓣；
- dB 图必须避免直接计算 $\log 0$。

## 5. 调制、解调与低通

~~~matlab
t = (0:length(x)-1)' / fs;
fc = 8000;

s = x .* cos(2*pi*fc*t);       % DSB-SC 调制
r = 2*s .* cos(2*pi*fc*t);     % 相干解调

Wn = 3500 / (fs/2);            % 归一化截止频率
[b, a] = butter(6, Wn);
y = filtfilt(b, a, r);         % 离线零相位处理
~~~

**filtfilt** 使用前后向滤波，非实时、非因果；实时系统应使用 **filter** 并考虑相位延迟和状态。

## 6. 二维连续傅里叶变换

二维信号 $f(x,y)$ 的变换为

$$
F(\omega_x,\omega_y)
=\iint_{-\infty}^{\infty}
f(x,y)e^{-j(\omega_x x+\omega_y y)}\,dx\,dy.
$$

反变换：

$$
f(x,y)=\frac1{(2\pi)^2}
\iint_{-\infty}^{\infty}
F(\omega_x,\omega_y)
e^{j(\omega_x x+\omega_y y)}
\,d\omega_xd\omega_y.
$$

二维频率表示图像沿不同空间方向的变化速度：

- 中心附近：低空间频率，缓慢变化区域；
- 远离中心：高空间频率，边缘和细节；
- 频谱方向与图像纹理方向存在几何对应。

## 7. 二维 DFT 实验

~~~matlab
I = im2double(imread("image.png"));
if size(I, 3) == 3
    I = rgb2gray(I);
end

F = fftshift(fft2(I));
S = log1p(abs(F));

imagesc(S);
axis image off;
colormap gray;
~~~

反变换：

~~~matlab
I_rec = real(ifft2(ifftshift(F)));
~~~

### 频域滤波

二维频域滤波满足

$$
G[k,\ell]=H[k,\ell]F[k,\ell].
$$

硬截断的理想低通会产生明显振铃；Gaussian、Butterworth 等平滑过渡滤波器通常更适合图像。

## 8. 实验检查清单

- [ ] 是否记录采样率和单位？
- [ ] FFT 长度与频率轴是否一致？
- [ ] 是否区分线性卷积和循环卷积？
- [ ] 单边谱是否正确处理直流和奈奎斯特点？
- [ ] 是否说明使用的窗与归一化方式？
- [ ] 滤波是实时因果还是离线零相位？
- [ ] 二维频谱显示前是否使用 **fftshift** 和对数压缩？

相关笔记：[[信号与系统课程笔记/07 DTFT、DFS、DFT 与 FFT|DTFT、DFS、DFT 与 FFT]]、[[信号与系统课程笔记/06 傅里叶变换性质、调制与滤波|调制与滤波]]。
