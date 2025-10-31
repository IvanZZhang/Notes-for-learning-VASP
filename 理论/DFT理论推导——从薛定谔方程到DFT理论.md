# 为什么要推导？
功利地来看，DFT理论推导是每篇计算方向论文不可或缺的背景知识的一部分。即使不能深刻理解DFT理论的原理，过一遍推导对写作也是大有裨益的。

让我们从薛定谔方程开始，经过Hohenberg-Kohn定理，引出Kohn-Sham方程。
# 推导
## 第一性原理计算
### Schrodinger方程
处理微观多粒子体系相关问题时，基本出发点就是求解该体系的Schrodinger方程：$$\hat H\Psi(r,R)=E\Psi(r,R)$$其中$\Psi(r,R)$为体系的波函数，$\hat H$为分子体系的Hamilton算符。Hamilton算符的一般表示为$$\hat H(r,R)=\hat T_e(r_i)+\hat V_{ee}(r_i)+\hat T_N(R_I)+\hat V_{NN}(R_I)+\hat V_{Ne}(r_i,R_I)$$式中依次为电子动能项、电子间库仑作用项、原子核动能项、原子核势能项和原子核-电子相互作用项。$r_i$表示电子坐标，$R_I$表示原子坐标。展开每一项：
$$\left\{
\begin{aligned}
\hat T_e(r_i)&=-\sum_i\frac{\hbar^2}{2m_e}\nabla_{r_i}^2 \\
\hat V_{ee}(r_i)&=\frac{1}{2}\sum_{i,j}\frac{e^2}{|r_i-r_j|} \\
\hat T_N(r_i)&=-\sum_j\frac{\hbar^2}{2M_I}\nabla_{R_I}^2 \\
\hat V_{NN}(R_I)&=\frac{1}{2}\sum_{I,J}\frac{Z_IZ_Je^2}{|R_I-R_J|} \\
\hat V_{Ne}(r_i,R_I)&=-\sum_{I,i}\frac{Z_Ie^2}{|r_i-R_I|}
\end{aligned}
\right.
$$其中，$m_e$为电子的质量，$M_I$为原子核质量，$Z_I$为原子核电荷。
### Born-Oppenheimer近似
由于核的质量比电子质量大很多，所以相对于电子运动，核的运动几乎可以忽略不计。因此将核的运动和电子的运动分开处理：当处理电子运动时，认为核固定不动；当处理核运动时，认为核在快速运动的电子形成的一个平均负电荷分布中运动。

Born-Oppenheimer近似下，
1. 电子运动方程表述为$$\hat H_e\Psi_e(r,R)=E_e\Psi_e(r,R)$$其中$\Psi_e(r,R)$为电子波函数，$\hat H_e$为电子的Hamilton算符，表示为$$\hat H_e=\hat T_e(r_i)+\hat V_{ee}(r_i)+\hat V_{Ne}(r_i,R_I)$$
2. 核运动方程表述为$$\hat H_N\Psi_N(r,R)=E_N\Psi_N(r,R)$$其中$\Psi_N$为原子核的波函数，$\hat H_N$为原子核的Hamilton算符，表示为$$\hat H_N=\hat T_N(R_I)+\hat V_{NN}(R_I)+E_e$$
Born-Oppenheimer近似是较为精确的，但对原子核-电子相互作用较强的体系误差较大。
### Hartree-Fock近似
基于Born-Oppenheimer近似，Hatree-Fock方法提供了一种近似求解体系多电子波函数$\Psi(x_1,x_2,\dots,x_N)$的方法。其中$x_i=r_i\sigma_i$，分别表示电子的空间坐标和自旋态。

Hatree考虑单电子位于其他电子所建立的平均势场中运动，这样每个电子都可以由一个单电子的波函数来描述。于是体系的波函数就可以用单电子波函数的乘积表示：$$\Psi(r)=\prod_i\psi_i(r_i)$$此时体系的总能量表示为$$E=\bra\Psi\hat H\ket\Psi=\sum_i\bra{\psi_i(r_i)}\hat H\ket{\psi_i(r_i)}=\sum_iE_i$$
为了纳入Pauli不相容原理，保持波函数反对称性，波函数写作$$\Psi=\frac{1}{\sqrt{N!}}\left(
\begin{matrix}
\psi_1(x_1) & \psi_2(x_1) & \cdots & \psi_N(x_1) \\
\psi_1(x_2) & \psi_2(x_2) & \cdots & \psi_N(x_2) \\
\vdots & \vdots & \ddots & \vdots \\
\psi_1(x_N) & \psi_2(x_N) & \cdots & \psi_N(x_N) \\
\end{matrix}
\right)$$显而易见，这个波函数表达形式是反对称的。$$\Psi(\dots,i,\dots,j,\dots)=-\Psi(\dots,j,\dots,i\dots)$$

此时，体系总能量表示为$$E=\bra\Psi\hat H\ket\Psi=\sum_i\int\psi_{i,\sigma}^*(x_i)[-\frac1 2\nabla^2+V_\text{ext}]\psi_{i,\sigma}(x_i)\text{d}r+\frac1 2\sum_{i,j}\int\frac{\psi_i^*(x_i)\psi_j^*(x_j)\psi_j(x_j)\psi_i(x_i)}{|r_i-r_j|}\text{d}r_i\text{d}r_j+\frac1 2\sum_{i,j}\int\frac{\psi_i^*(x_i)\psi_j^*(x_j)\psi_j(x_i)\psi_i(x_j)}{|r_i-r_j|}\text{d}r_i\text{d}r_j$$
该能量同时对应一个Schrodinger方程。1928年，Hatree提出了自洽势场的概念：由于已知单个粒子的波函数，可以得出体系波函数，计算出体系中各电子对应的平均势场，进而再次求得单个粒子波函数。如此构成一次迭代，直到单个粒子的波函数与各电子对应平均势场自洽。然而在实际计算过程中，这个迭代过程依赖于对初始状态的猜测，并且在一些情况下可能是不收敛的。
## 密度泛函理论
基于Hatree-Fock方法的自洽计算时间复杂度是很高的，在大体系下需要的计算量指数级增长。因此，密度泛函理论（DFT）应运而生。DFT方法采用体系密度矩阵作为变量，可以在很大程度上减少计算的复杂度，使得大体系的计算具有可能性。
### Thomas-Fermi-Dirac模型

### Hohenberg-Kohn定理
1964年，P. Hohenberg和W. Kohn提出了Hohenberg-Kohn定理，证明了电子密度可以唯一地决定体系能量。该定理成为了密度泛函理论的基础。
### Kohn-Sham方程

## 交换关联能泛函
### LDA
### GGA

### 杂化泛函