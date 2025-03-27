**示例**（苏亚琼老师，Cu111）
```R title:INCAR
Electronic relaxation:
ENCUT   = 400.0      
ALGO    = FAST      
#NELMIN  = 8         
NELM    = 100       
#NELMDL  = -12       
EDIFF   = 1E-4      
AMIX    = 0.20      
BMIX    = 0.0010    
AMIX_MAG    = 0.80
BMIX_MAG    = 0.0010

Calculation mode:
PREC    = Accurate  
ISPIN   = 2         
#MAGMOM = 18*3 18*0 36*1 36*0 1*2 
#NUPDOWN = 2
#LMAXMIX = 4         
ADDGRID = .TRUE.    
LASPH   = .TRUE.    
ISYM    = 0         

LREAL = AUTO

#PBE+U calculation:
#LDAU      = .TRUE.    
#LDAUTYPE  = 2         
#LDAUL     = -1 -1 -1 -1 -1 2   
#LDAUU     = 0.00 0.00 0.00  0.00  0.00 4.00
#LDAUJ     = 0.00 0.00 0.00  0.00  0.00 0.00
#LDAUPRINT = 0         
#
Integration over the Brillouin zone (BZ):
ISMEAR  = 1         
SIGMA   = 0.20        

Ionic relaxation:
NSW     = 100   
EDIFFG  = -0.05     
IBRION  = 2              
POTIM   = 0.10        

DOS calculation:
LORBIT  = 11        

for dipol correction:
#IDIPOL = 3          
#LDIPOL = .TRUE.     
LWAVE = .TRUE.

VDW: 
IVDW = 12

#ISIF = 3

#UV Adsorption
#NBANDS = 1760  # twice of BANDS
#LOPTICS = .TRUE.

NCORE =16

#LSOL = .TRUE.

```

## Electronic relaxation 电子弛豫部分
### [ENCUT](https://www.vasp.at/wiki/index.php/ENCUT)
默认值：**ENCUT**	= POTCAR 文件中的最大 ENMAX	
描述： **ENCUT** 指定了以 eV 为单位设置的平面波基的能量截止值。

`ENCUT = 400.0`

**重要**
- 应始终检查相关量相对于能量截止 ENCUT 的收敛性。
- 我们强烈建议始终在 [INCAR](https://www.vasp.at/wiki/index.php/INCAR "INCAR") 文件中手动指定能量截止 ENCUT，以确保计算之间的精度相同。否则，默认的 ENCUT 在不同的计算中可能会有所不同（例如，用于内聚能的计算），结果是总能量无法比较。

### [ALGO](https://www.vasp.at/wiki/index.php/ALGO)

ALGO 标签是指定电子最小化算法（截至 VASP.4.5）和/或选择 GW 计算类型的便捷选项。

`ALGO = FAST`
选择相当稳健的 block-Davidson 和 RMM-DIIS 算法的混合。在这种情况下，初始阶段使用block-Davidson （IALGO=38），然后 VASP 切换到 RMM-DIIS （IALGO=48）。随后，对于每个离子更新，对每个离子步骤（第一个步骤除外）执行一次 IALGO=38 扫描。此算法已针对 vasp.6 进行了更新，以提高稳健性。

### [NELM](https://www.vasp.at/wiki/index.php/NELM)
默认值：**NELM** = 60

描述：**NELM** 设置电子 SC（自洽）步骤的最大数量。
 
`NELM = 100`

通常，不需要更改默认值：如果自洽循环在 40 步内没有收敛，它可能根本不会收敛。在这种情况下，您应该重新考虑标签 [IALGO](https://www.vasp.at/wiki/index.php/IALGO "IALGO") 或 [ALGO](https://www.vasp.at/wiki/index.php/ALGO "ALGO")、[LSUBROT](https://www.vasp.at/wiki/index.php/LSUBROT "LSUBROT") 以及[混合参数](https://www.vasp.at/wiki/index.php/Category:Density_mixing "Category:Density mixing")。

这同样代表 [ALGO](https://www.vasp.at/wiki/index.php/ALGO "ALGO") = TIMEEV，因为该值被设置为足以确保沿时间传播时的数值稳定性。如果您希望自行设置，请注意输入值必须大于 100，否则 VASP 将忽略它并降至默认设置。

### [EDIFF](https://www.vasp.at/wiki/index.php/EDIFF)
默认值：**EDIFF** = $10^{-4}$

描述：**EDIFF** 指定电子 SC 循环的全局中断条件。**EDIFF** 以 eV 为单位指定。

如果两个步骤之间的总（自由）能量变化和能带结构能量变化（“特征值的变化”）都小于 EDIFF（以 eV 为单位），则电子自由度的松弛停止。对于 EDIFF=0，将执行严格的 NELM 电子自洽步骤。

在大多数情况下，收敛速度是二次的，因此额外迭代的成本通常很小。因此，对于收敛良好的计算，我们强烈建议将 EDIFF 降低到 1E-6。对于有限差分计算（例如声子），为了获得精确的结果，甚至可能需要 EDIFF = 1E-7。另一方面，对于具有许多原子的大型系统和/或使用 meta-GGA 泛函时，实现 1E-8 甚至 1E-7 的能量收敛可能很困难。因此，总体 EDIFF= 1E-6 可能是最好的折衷方案。

### [AMIX](https://www.vasp.at/wiki/index.php/AMIX)
默认值：**AMIX**  = 0.8  如果 [ISPIN](https://www.vasp.at/wiki/index.php/ISPIN "ISPIN")=1 且一个使用 US-PPs  
= 0.4  如果 [ISPIN](https://www.vasp.at/wiki/index.php/ISPIN "ISPIN")=2，并且一个使用 US-PPs 
= 0.4  如果使用 PAW 数据集 

说明：**AMIX** 指定线性混合参数。

`AMIX = 0.20`

在 VASP 中，计算电荷介电矩阵的特征值谱，并在每个电子步骤写入 [OUTCAR](https://www.vasp.at/wiki/index.php/OUTCAR "OUTCAR") 文件。如果需要，这允许相当容易地优化混合参数。在 [OUTCAR](https://www.vasp.at/wiki/index.php/OUTCAR "OUTCAR") 文件中搜索

eigenvalues of (default mixing * dielectric matrix)

如果平均特征值$Γ_{mean}=1$，并且特征值谱的宽度最小，则混合参数是最优的。对于初始线性混合 （[BMIX](https://www.vasp.at/wiki/index.php/BMIX "BMIX")≈0），可以通过设置 $AMIX_{optimal}=AMIX_{current} * Γ_{mean}$ 来轻松找到 AMIX 的最佳设置。对于 Kerker 方案 （[IMIX](https://www.vasp.at/wiki/index.php/IMIX "IMIX")=1），可以优化 AMIX 或 [BMIX](https://www.vasp.at/wiki/index.php/BMIX "BMIX")，但我们建议只更改 [BMIX](https://www.vasp.at/wiki/index.php/BMIX "BMIX") 并保持 AMIX 固定（如果平均特征值大于 1，则必须减小 [BMIX](https://www.vasp.at/wiki/index.php/BMIX "BMIX")，如果平均特征值Γ均值<1），则必须增加 [BMIX](https://www.vasp.at/wiki/index.php/BMIX "BMIX")）。 然而，最佳 AMIX 在很大程度上取决于系统，对于金属，这个参数通常必须相当小，例如 AMIX= 0.02。

### [BMIX](https://www.vasp.at/wiki/index.php/BMIX)
默认值：**BMIX** = 1.0

描述： BMIX 设置 Kerker 混合方案的截止波向量（[IMIX](https://www.vasp.at/wiki/index.php/IMIX "IMIX")=1 和/或 [INIMIX](https://www.vasp.at/wiki/index.php/INIMIX "INIMIX")=1）。

`BMIX = 0.0010`

混合密度由下式给出
$$\rho_{mix}(G)=\rho_{in}(G)+A \frac{G^2}{G^2+B^2}(\rho_{out}(G)-\rho_{in}(G))$$

其中$A$=**AMIX**，$B$=**BMIX**

在 VASP 中，计算电荷介电矩阵的特征值谱，并在每个电子步骤写入 [OUTCAR](https://www.vasp.at/wiki/index.php/OUTCAR "OUTCAR") 文件。如果需要，这允许相当容易地优化混合参数。在 [OUTCAR](https://www.vasp.at/wiki/index.php/OUTCAR "OUTCAR") 文件中搜索

eigenvalues of (default mixing * dielectric matrix)

如果平均特征值$Γ_{mean}=1$，并且特征值谱的宽度最小，则混合参数是最优的。对于初始线性混合 （[BMIX](https://www.vasp.at/wiki/index.php/BMIX "BMIX")≈0），可以通过设置 $AMIX_{optimal}=AMIX_{current} * Γ_{mean}$ 来轻松找到 AMIX 的最佳设置。对于 Kerker 方案 （[IMIX](https://www.vasp.at/wiki/index.php/IMIX "IMIX")=1），可以优化 AMIX 或 [BMIX](https://www.vasp.at/wiki/index.php/BMIX "BMIX")，但我们建议只更改 [BMIX](https://www.vasp.at/wiki/index.php/BMIX "BMIX") 并保持 AMIX 固定（如果平均特征值大于 1，则必须减小 [BMIX](https://www.vasp.at/wiki/index.php/BMIX "BMIX")，如果平均特征值Γ均值<1），则必须增加 [BMIX](https://www.vasp.at/wiki/index.php/BMIX "BMIX")）。 然而，最佳 AMIX 在很大程度上取决于系统，对于金属，这个参数通常必须相当小，例如 AMIX= 0.02。
### [AMIX_MAG](https://www.vasp.at/wiki/index.php/AMIX_MAG)
默认值：**AMIX_MAG** = 1.6

描述：磁化密度的线性混合参数。

`AMIX_MAG = 0.80`

自旋极化计算的默认混合参数为：

[IMIX](https://www.vasp.at/wiki/index.php/IMIX "IMIX")=4、[AMIX](https://www.vasp.at/wiki/index.php/AMIX "AMIX")=0.4、[AMIN](https://www.vasp.at/wiki/index.php/AMIN "AMIN")=min（0.1、[AMIX](https://www.vasp.at/wiki/index.php/AMIX "AMIX") AMIX_MAG）、[BMIX](https://www.vasp.at/wiki/index.php/BMIX "BMIX")=1.0、AMIX_MAG=1.6 和 [BMIX_MAG](https://www.vasp.at/wiki/index.php/BMIX_MAG "BMIX MAG")=1.0。

这些设置与（初始）自旋增强因子 4 一致，这通常是一个合理的近似值。

如果收敛速度非常慢，则只能尝试几种其他参数组合。特别是，对于板、磁性系统和绝缘系统（例如分子和团簇），初始的“线性混合”可以导致比 Kerker 模型函数更快的收敛。因此，可以尝试使用以下设置

[AMIX](https://www.vasp.at/wiki/index.php/AMIX "AMIX")     = 0.2
[BMIX](https://www.vasp.at/wiki/index.php/BMIX "BMIX")     = 0.0001 ! almost zero, but 0 will crash some versions
AMIX_MAG = 0.8
[BMIX_MAG](https://www.vasp.at/wiki/index.php/BMIX_MAG "BMIX MAG") = 0.0001 ! almost zero, but 0 will crash some versions

**注意：** 对于自旋极化计算，混合参数 [AMIX](https://www.vasp.at/wiki/index.php/AMIX "AMIX") 和 [BMIX](https://www.vasp.at/wiki/index.php/BMIX "BMIX") 的默认值与非自旋极化情况的默认值不同。
### [BMIX_MAG](https://www.vasp.at/wiki/index.php/BMIX_MAG)
默认值：**BMIX_MAG** = 1.0

描述：设置磁化密度的 Kerker 混合方案（[IMIX](https://www.vasp.at/wiki/index.php/IMIX "IMIX")=1 和/或 [INIMIX](https://www.vasp.at/wiki/index.php/INIMIX "INIMIX")=1）的截止波矢量。

`BMIX_MAG = 0.0010`

自旋极化计算的默认混合参数为：

[IMIX](https://www.vasp.at/wiki/index.php/IMIX "IMIX")=4、[AMIX](https://www.vasp.at/wiki/index.php/AMIX "AMIX")=0.4、[AMIN](https://www.vasp.at/wiki/index.php/AMIN "AMIN")=min（0.1、[AMIX](https://www.vasp.at/wiki/index.php/AMIX "AMIX") [AMIX_MAG](https://www.vasp.at/wiki/index.php/AMIX_MAG "AMIX MAG")）、[BMIX](https://www.vasp.at/wiki/index.php/BMIX "BMIX")=1.0、[AMIX_MAG](https://www.vasp.at/wiki/index.php/AMIX_MAG "AMIX MAG")=1.6 和 BMIX_MAG=1.0。

这些设置与（初始）自旋增强因子 4 一致，这通常是一个合理的近似值。

如果收敛速度非常慢，则只能尝试少数其他参数组合。特别是，对于板、磁性系统和绝缘系统（例如分子和团簇），初始的“线性混合”可以导致比 Kerker 模型函数更快的收敛。因此，可以尝试使用以下设置

[AMIX](https://www.vasp.at/wiki/index.php/AMIX "AMIX")     = 0.2
[BMIX](https://www.vasp.at/wiki/index.php/BMIX "BMIX")     = 0.0001 ! almost zero, but 0 will crash some versions
[AMIX_MAG](https://www.vasp.at/wiki/index.php/AMIX_MAG "AMIX MAG") = 0.8
BMIX_MAG = 0.0001 ! almost zero, but 0 will crash some versions

**注意**：对于自旋极化计算，混合参数 [AMIX](https://www.vasp.at/wiki/index.php/AMIX "AMIX") 和 [BMIX](https://www.vasp.at/wiki/index.php/BMIX "BMIX") 的默认值与非自旋极化情况的默认值不同。

## Calculation mode 计算模式
### [PREC](https://www.vasp.at/wiki/index.php/PREC)
**PREC** = Normal | Single | SingleN | Accurate | Low | Medium | High

**PREC** 指定 “precision” 模式。

`PREC = Accurate`

我们建议使用 PREC=Normal 或 PREC=Accurate。PREC=Normal 可用于大多数常规计算。PREC=准确导致更密集的网格 （NGX，NGY，NGZ）。因此，它减少了蛋盒效应并严格避免了任何[混叠/环绕错误](https://www.vasp.at/wiki/index.php/Wrap-around_errors "Wrap-around errors")。PREC=Normal和 PREC=Accurate使用比用于表示伪轨道的网格 （NGX，NGY，NGZ） 大两倍的增强精细网格 （[NGXF，NGYF，NGZF](https://www.vasp.at/wiki/index.php/NGXF "NGXF")）。PREC=Accurate 在一定程度上增加了内存要求，但当需要非常好的精度时，例如，为了精确的力，对于声子和应力张量，或者通常在计算二阶导数时，应该使用它（与 [ENCUT](https://www.vasp.at/wiki/index.php/ENCUT "ENCUT")的增加值结合使用）。有时也可以通过指定 [ADDGRID](https://www.vasp.at/wiki/index.php/ADDGRID "ADDGRID")= 来进一步提高力的精度。TRUE.，但是，来自用户的报告对于这是否真的有帮助有些矛盾。
### [ISPIN](https://www.vasp.at/wiki/index.php/ISPIN)
**ISPIN** = 1 | 2  
默认值：**ISPIN** = 1

描述： ISPIN 指定自旋极化。

`ISPIN = 2`

- ISPIN=1：执行非自旋极化计算。
- ISPIN=2：执行自旋极化计算（共线）。

通过将 ISPIN 与 [MAGMOM](https://www.vasp.at/wiki/index.php/MAGMOM "MAGMOM") 相结合，可以研究共线磁性
### [ADDGRID](https://www.vasp.at/wiki/index.php/ADDGRID)
**ADDGRID** = .TRUE. | .FALSE.  
Default: **ADDGRID** = .FALSE.
描述： **ADDGRID** 确定是否使用额外的支持网格来评估增强费用。
### [LASPH](https://www.vasp.at/wiki/index.php/LASPH)
**LASPH** = .TRUE. | .FALSE.  
Default: **LASPH** = .FALSE.
描述：包括与 PAW 球体中密度梯度相关的非球形贡献。

通常，VASP 仅计算对 PAW 球体内梯度校正的球形贡献（始终包括电位的 LDA 部分和 Hartree 电位的非球形贡献）。

对于 LASPH = .TRUE. 时，PAW 球体内的梯度校正的非球面贡献也将被包括在内。对于 VASP.4.6，这些贡献仅包含在达到自洽后，忽略梯度校正中的非球面贡献。
### [ISYM](https://www.vasp.at/wiki/index.php/ISYM)
**ISYM** = -1 | 0 | 1 | 2 | 3

| Default: **ISYM** | = 1 | if VASP runs with USPPs                                                   |
| ----------------- | --- | ------------------------------------------------------------------------- |
|                   | = 3 | if [LHFCALC](https://www.vasp.at/wiki/index.php/LHFCALC "LHFCALC")=.TRUE. |
|                   | = 2 | else                                                                      |
描述：ISYM 确定 VASP 处理对称性的方式。

`ISYM = 0`

当 ISYM=0 时，VASP 不使用对称性，但它会假设 $\Psi_k = \Psi^{*}_{-k}$，并相应地减少布里渊区的采样。应为分子动力学设置此值，即 [IBRION](https://www.vasp.at/wiki/index.php/IBRION "IBRION")=0。
### [LREAL](https://www.vasp.at/wiki/index.php/LREAL)
Default: **LREAL** = .FALSE.
描述：**LREAL** 确定投影运算符是在实空间还是在倒数空间中计算。

## Integration over the Brillouin zone (BZ) 布里渊区积分
### [ISMEAR](https://www.vasp.at/wiki/index.php/ISMEAR)
ISMEAR = -5 | -4 | -3 | -2 | -1 | 0 | [integer]>0  
Default: **ISMEAR** = 1

描述：ISMEAR 确定如何为每个轨道设置partial occupancies $f_{nk}$ 。[SIGMA](https://www.vasp.at/wiki/index.php/SIGMA "SIGMA") 确定拖尾的宽度（以 eV 为单位）。

`ISMEAR = 1`

### [SIGMA](https://www.vasp.at/wiki/index.php/SIGMA)
SIGMA = [real]  
Default: **SIGMA** = 0.2 

Description: SIGMA specifies the width of the smearing in eV.

`SIGMA = 0.20`

## Ionic relaxation
### [NSW](https://www.vasp.at/wiki/index.php/NSW)
默认值：**NSW** = 0
描述：NSW 设置最大离子步数。

`NSW = 100`

### [EDIFFG](https://www.vasp.at/wiki/index.php/EDIFFG)
默认值： **EDIFFG** = [EDIFF](https://www.vasp.at/wiki/index.php/EDIFF "EDIFF")×10
描述： EDIFFG 定义了离子弛豫环的中断条件。

`EDIFFG = -0.05`

当 EDIFFG 为正时，当两个离子步骤之间的总能量变化小于 EDIFFG 时，弛豫停止。

当 EDIFFG 为负时，当所有力的范数都小于 |EDIFFG|.这通常是一个更方便的设置。

如果 EDIFFG = 0，则离子弛豫在 [NSW](https://www.vasp.at/wiki/index.php/NSW "NSW") 步骤后停止。

**警告：** EDIFFG 不适用于[分子动力学](https://www.vasp.at/wiki/index.php/Category:Molecular_dynamics "Category:Molecular dynamics")仿真。

### [IBRION](https://www.vasp.at/wiki/index.php/IBRION)
描述：确定计算过程中晶体结构如何变化

`IBRION = 2`

### [POTIM](https://www.vasp.at/wiki/index.php/POTIM)
描述： POTIM 设置分子动力学中的时间步长或离子弛豫中的步长。

`POTIM = 0.10`

## for dipol correction
### [LWAVE](https://www.vasp.at/wiki/index.php/LWAVE)
描述：**LWAVE** 确定是否在运行结束时将 wavefunctions 写入 [WAVECAR](https://www.vasp.at/wiki/index.php/WAVECAR "WAVECAR") 文件。

`LWAVE = .TRUE.`

## VDW
### [IVDW](https://www.vasp.at/wiki/index.php/IVDW)
默认值：**IVDW** = 0（无校正）
描述：**IVDW** 指定 vdW（色散）校正。

`IVDW = 12`

由于基本原因，半局部和混合交换相关泛函无法正确描述由波动电荷分布之间的动态相关性（称为伦敦色散力）引起的 vdW 相互作用。解决此问题并为 vdW 系统获得更可靠结果的近似方法是添加色散校正项$E_{disp}$，转换为传统的 KS-DFT 能量$E_{tot}^{KS−DFT}$:
$$E_{tot}^{KS−DFT−disp}=E_{tot}^{KS−DFT}+E_{disp}$$

### [NCORE](https://www.vasp.at/wiki/index.php/NCORE)
默认值：**NCORE** = 1
描述：**NCORE** 确定在单个轨道上运行的计算核心数

`NCORE =16`