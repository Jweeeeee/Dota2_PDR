# Dota2_PDR
Dota2_PDR算法相关
## 1.简介

PRD是war3计算暴击时使用的伪随机算法，其意义是减少极端事件发生的概率，平衡游戏体验

## 2.随机与伪随机

生成服从均匀分布的随机数是容易的，然后通过逆采样变换可以得到服从任意分布的随机数

假设需要生成服从分布$F$的随机数，只需生成$y$ ~ $U(0,1)$，令$y = F(x)$，则$x = G(y) = F^{-1}(y)$，由此变换得到的$x$服从分布F

计算机通过简单采样以外的方法生成的非均匀分布的随机数，都可以理解为是伪随机数

## 3.PRD算法

一般暴击事件可以用二项分布去描述和建模。例如事件$\{进行n次普攻有k次暴击\}$，其概率分布函数$P(X_n=k)$为：
$$
P(X_n=k) = C_n^k \cdot p^k \cdot (1-p)^{n-k}
$$
其中$p$为单次普攻暴击的概率

在介绍PRD是如何运作之前，我们需要修改一下上述的事件描述，他仍然服从二项分布，事实上就是$k=n-1$的情形。事件$\{进行n次普攻最后1次暴击\}$，其概率分布函数$P(X_n)$为：
$$
P(X_n) = p \cdot (1-p)^{n-1}
$$
其数学期望记为$E_{bnl}$：
$$
E_{bnl} = E(P(X_n)) = \Sigma_{n=1}^{\infty} p \cdot (1-p)^{n-1} \cdot n
$$
PRD算法：直到下一次暴击之前，单次普攻暴击的概率为$n*c$，$n$为上一次暴击之后的普攻次数，暴击后$n$重置为1

PRD算法下事件$\{进行n次普攻最后1次暴击\}$的概率分布函数$P(X_n)$为：
$$
P(X_n) = n \cdot c \cdot (1 - c) \cdot (1 - 2c) \cdot (1 - 3c) ......
= n! \cdot c \cdot \Pi_{i=1}^{n-1}(\frac{1}{i} - c)
$$
其数学期望记为$E_{prd}$：
$$
E_{prd} = E(P(X_n)) = \Sigma_{n=1}^{sup} n \cdot c \cdot (1 - c) \cdot (1 - 2c) \cdot (1 - 3c) ...... \cdot n
= \Sigma_{n=1}^{sup} n! \cdot c \cdot \Pi_{i=1}^{n-1}(\frac{1}{i} - c) \cdot n
$$
其中$sup$为最大不暴击次数，可由$[\frac{1}{c}]+1$计算