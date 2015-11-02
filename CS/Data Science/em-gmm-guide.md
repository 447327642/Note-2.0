# EM, GMM 指南

## Jensen 不等式

若 f 是凸函数，基本 Jensen 不等式

$$
f(\theta x+(1-\theta)y \le \theta f(x)+(1-\theta)f(y)
$$

若 $\theta_1,\dots,\theta_k \ge 0\;,\;\theta_1+\dots+\theta_k=1$，则

$$
f(\theta_1 x_1+\dots+\theta_k x_k) \le \theta_1 f(x_1)+\dots+\theta_k f(x_k)
$$

且

$$
f(\mathbf{E}x) \le \mathbf{E}f(x)
$$

## 考察高斯分布

若给定一组样本 $x_1,x_2,\dots,x_n$，已知它们来自于高斯分布 $N(\mu,\sigma)$，试估计参数 $\mu,\sigma$

高斯分布的概率密度函数

$$
f(x)=\frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{(x-\mu)^2}{2\sigma^2}}
$$

将 $x_1,x_2,\dots,x_n$ 带入，得到样本的似然函数

$$
L(x)=\prod_i \frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{(x_i-\mu)^2}{2\sigma^2}}
$$

化简对数似然函数

$$
\begin{align*} 
l(x)&=log\prod_i \frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{(x_i-\mu)^2}{2\sigma^2}} \\
&=\sum_ilog \frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{(x_i-\mu)^2}{2\sigma^2}} \\
&=\left(\sum_i log\frac{1}{\sqrt{2\pi}\sigma}\right)+\left( \sum_i-\frac{(x_i-\mu)^2}{2\sigma^2}\right) \\
&=-\frac{n}{2}log(2\pi\sigma^2)-\frac{1}{2\sigma^2}\sum_i(x_i-\mu)^2
\end{align*} 
$$

可得目标函数

$$
l(x)= -\frac{n}{2}log(2\pi\sigma^2)-\frac{1}{2\sigma^2}\sum_i(x_i-\mu)^2
$$

将目标函数对参数 $\mu,\sigma$分别求偏导，可得

$$
\mu=\frac{1}{n}\sum_ix_i \\
\sigma^2=\frac{1}{n}\sum_i(x_i-\mu)^2
$$

## 直观理解 GMM 参数估计

随机挑选 100 位顾客，测量身高，若样本中存在男性和女性顾客，它们服从 $N(\mu_1,\sigma_1)$ 和 $N(\mu_2,\sigma_2)$ 的分布，试估计 $\mu_1,\sigma_1,\mu_2,\sigma_2$，这里我们不知道性别，对于存在一个隐变量的分布(这里就是不知道性别)，用 EM 算法就非常合适

随机变量 X 是有 K 个高斯分布混合而成，取各个高斯分布的概率为 $\pi_1,\pi_2,\dots,\pi_K$，第 i 个高斯分布的均值为 $\mu_1$，方差为 $\sum_i$。若观测到随机变量 X 的一系列样本 $x_1,x_2,\dots,x_n$，试估计参数 $\pi,\mu,\Sigma$

建立目标函数，产生这个样本的对数似然函数为

$$
l(x)=\sum_{i=1}^{N}log\left(\sum_{k=1}^{K}\pi_kN(x_i\;|\;\mu_k,\Sigma_k)\right)
$$

由于在对数函数里面又有求和，没办法用直接求导解方程的办法直接求得极大值。为此，我们分成两步解决这个问题。

### 第一步 估算数据来自哪个组份

对于每个数据 $x_i$ 来说，它由第 k 个组份生成的概率为

$$
\gamma(i,k)=\frac{\pi_kN(x_i\;|\;\mu_k,\Sigma_k)}{\sum_{j=1}^K\pi_jN(x_i\;|\;\mu_j,\Sigma_j)}
$$

由于式子里 $\mu,\Sigma$ 也是需要我们估计的值，我们采用迭代发，在计算 $\gamma(i,k)$ 的时候假定 $\mu,\Sigma$ 均已知。第一次计算时，需要先验知识给定 $\mu,\Sigma$。

### 第二步 估算每个组份的参数

假设上一步中得到的 $\gamma(i,k)$ 就是正确的『数据 $x_i$ 由组份 k 生成的概率』，或者，我们可以看做 $x_i$ 其中有 $\gamma(i,k)\times x_i$ 部分是由组份 k 所生成的。

对于所有的数据点，现在实际上可以看作组份 k 生成了 $\{\gamma(i,k)\times x_i \;|\;i=1,2,\dots,N\}$ 这些点。组份 k 是一个标准的高斯分布，所以

$$
\mu_k=\frac{1}{N_k}\sum_{i=1}^{N}\gamma(i,k)x_i \\
\Sigma_k=\frac{1}{N_k}\sum_{i=1}^{N}\gamma(i,k)(x_i-\mu_k)(x_i-\mu_k)^T
$$

## EM 算法的提出

假定有训练集

$$
\{x^{(1)},\dots,x^{(m)}\}
$$

包含 m 个独立样本，希望从众找到该组数据的模型 p(x,z) 的参数，这里 x 就是可以观测到的数据，z 是隐变量。

取对数似然函数

$$
\ell(\theta)=\sum_{i=1}^{m}log\;p(x;\theta)=\sum_{i=1}^{m}log\sum_zp(x,z;\theta)
$$

这里，z 是隐随机变量，直接找到参数的估计是很困难的，我们的策略是建立 $\ell(\theta)$ 的下界，并且求该下界的最大值；重复这个过程，直到收敛到局部最大值

### Jensen 不等式

令 $Q_i$ 是 z 的某一个分布，$Q_i \ge 0$，有

$$
\begin{align*}
\sum_ilog\;p(x^{(i)};\theta) &= \sum_ilog\sum_{z^{(i)}}p(x^{(i)},z^{(i)};\theta) \\
&= \sum_ilog\sum_{z^{(i)}}Q_i(z^{(i)})\frac{p(x^{(i)},z^{(i)};\theta)}{Q_i(z^{(i)})} \\
&\ge \sum_i\sum_{z^{(i)}}Q_i(z^{(i)})log\frac{p(x^{(i)},z^{(i)};\theta)}{Q_i(z^{(i)})}
\end{align*}
$$

把 $Q_i(z^{(i)})$ 看成是一个概率，相当于就是求一个期望，于是上面的推导实际上可以看做下面的形式

$$
f(\mathbf{E}x) \le \mathbf{E}f(x)
$$

经过变化之后的式子会稍微好求解一些，上面这个不等式，在满足以下条件时取等号，c 是一个常数

$$
\frac{p(x^{(i)},z^{(i)};\theta)}{Q_i(z^{(i)})}=c
$$

就可以通过这个条件寻找尽量紧的下界，要求是一个常数，也就是说分子分母成比例，有

$$
Q_i(z^{(i)}) \propto p(x^{(i)},z^{(i)};\theta) \;\; and \;\; \sum_z Q_i(z^{(i)})=1
$$

因此可得

$$
\begin{align*}
Q_i(z^{(i)}) &= \frac{p(x^{(i)},z^{(i)};\theta)}{\sum_zp(x^{(i)},z;\theta)} \\
&=\frac{p(x^{(i)},z^{(i)};\theta)}{p(x^{(i)};\theta)} \\
&=p(z^{(i)}\;|\;x^{(i)};\theta)
\end{align*} 
$$

### EM 算法整体框架

实际上这里的 $Q_i(z^{(i)})$ 就是上面例子的 $\pi_k$

Repeat until convergence {

(E-step) For each $i$, set

$$
Q_i(z^{(i)}) := p(z^{(i)}\;|\;x^{(i)};\theta)
$$

(M-step) Set

$$
\theta := arg max_\theta \sum_i\sum_{z^{(i)}}Q_i(z^{(i)})log\frac{p(x^{(i)},z^{(i)};\theta)}{Q_i(z^{(i)})}
$$

}

## 理论框架推导 GMM

随机变量 $X$ 是有 K 个高斯分布混合而成，取各个高斯分布的概率为 $\phi_1,\phi_2,\dots,\phi_K$，第 i 个高斯分布的均值为 $\mu_1$，方差为 $\sum_i$。若观测到随机变量 $X$ 的一系列样本 $x_1,x_2,\dots,x_n$，试估计参数 $\phi,\mu,\Sigma$

### E-step

$$
\omega_j^{(i)}=Q_i(z^{(i)}=j)=P(z^{(i)}=j\;|\;x^{(i)};\phi,\mu,\Sigma)
$$

这里的 $\omega_j^{(i)}$ 表示第 i 个样本来自第 j 个分布的概率

### M-step

将多项分布和高斯分布的参数代入，可得

$$
\begin{align*}
&\sum_{i=1}^m\sum_{z^{(i)}}Q_i(z^{(i)})log\frac{p(x^{(i)},z^{(i)};\phi,\mu,\Sigma)}{Q_i(z^{(i)})} \\
=&\sum_{i=1}^m\sum_{j=1}^kQ_i(z^{(i)}=j)log \frac{p(x^{(i)}\;|\;z^{(i)}=j;\mu,\Sigma)p(z^{(i)}=j}{Q_i(z^{(i)}=j)} \\
=&\sum_{i=1}^m\sum_{j=1}^k\omega_j^{(i)}log\frac{\frac{1}{(2\pi)^{m/2}|\Sigma_j^{1/2}|}exp\left(-\frac{1}{2}(x^{(i)}-\mu_j)^T\Sigma_j^{-1}(x^{(i)}-\mu_j)\right)·\phi_j}{\omega_j^{(i)}}
\end{align*}
$$

第一次转换是把联合概率转换为条件概率，其中 $z^{(i)}$ 只跟 $\phi$ 有关，$x^{(i)}$ 只跟 $\mu,\Sigma$ 有关，所以可以分开写。于是前面的一个 p 就是一个高斯模型（后面直接代入得到最终公式），后面的一个 p 就是 $\phi_j$ 本身，因此得到这个公式

对均值求偏导

$$
\begin{align*}
&\nabla_{u_l} \sum_{i=1}^m\sum_{j=1}^k\omega_j^{(i)}log\frac{\frac{1}{(2\pi)^{m/2}|\Sigma_j^{1/2}|}exp\left(-\frac{1}{2}(x^{(i)}-\mu_j)^T\Sigma_j^{-1}(x^{(i)}-\mu_j)\right)·\phi_j}{\omega_j^{(i)}} \\
=&-\nabla_{u_l} \sum_{i=1}^m\sum_{j=1}^k\omega_j^{(i)}\frac{1}{2}(x^{(i)}-\mu_j)^T\Sigma_j^{-1}(x^{(i)}-\mu_j) \\
=&\frac{1}{2}\sum_{i=1}^{m}\omega_j^{(i)}\nabla_{u_l}2u_l^T\Sigma_j^{-1}x^{(i)}-u_l^T\Sigma_j^{-1}u_l \\
=&\sum_{i=1}^m\omega_j^{(i)}\left(\Sigma_j^{-1}x^{(i)}-\Sigma_j^{-1}u_l\right)
\end{align*}
$$

令上式等于 0，解得均值

$$
u_l := \frac{\sum_{i=1}^{m}\omega_l^{(i)}x^{(i)}}{\sum_{i=1}^{m}\omega_l^{(i)}}
$$

同样的方法对 $\Sigma$ 求偏导，并令其等于 0，解得

$$
\Sigma_j=\frac{\sum_{i=1}^{m}\omega_l^{(i)}(x^{(i)}-\mu_j)(x^{(i)}-\mu_j)^T}{\sum_{i=1}^{m}\omega_l^{(i)}}
$$

关于 $\phi$ ，在目标函数与它相关的是

$$
\sum_{i=1}^m\sum_{j=1}^k\omega_j^{(i)}log\phi_j
$$

由于多项分布的概率和为 1，建立拉格朗日方程

$$
L(\phi)=\sum_{i=1}^m\sum_{j=1}^k\omega_j^{(i)}log\phi_j+\beta\left(\sum_{j=1}^k\phi_j-1\right)
$$

这样求解的 $\phi_i$ 一定非负，所以不用考虑这个条件，然后对 $\phi,\beta$ 分别求偏导，令其为 0，可得

$$
\frac{\partial}{\partial\phi_j}L(\phi)=\sum_{i=1}^{m}\frac{\omega_j^{(i)}}{\phi_j}+\beta \\
-\beta = \sum_{i=1}^m\sum_{j=1}^k\omega_j^{(i)}=\sum_{i=1}^m1=m \\
\phi_j:=\frac{1}{m}\sum_{i=1}^{m}\omega_j^{(i)}
$$

估算出来这三个参数，再带入到 E-step，就可以循环进行计算了

