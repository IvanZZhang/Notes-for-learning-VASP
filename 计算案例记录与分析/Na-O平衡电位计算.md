参考Li-S平衡电位计算，可以计算Na-O平衡电位。

关键在于筛选计算数据，使计算结果符合实验值。

# Li-S体系
参考结构如图所示
![[Pasted image 20251107213441.png]]

# Na-O体系
![[96cc81cc6556af68e8dd6289d1f9ab1b.jpg]]

| 反应物   | 实验平衡电位 |
| ----- | ------ |
| NaO2  | 2.27 V |
| Na2O2 | 2.33 V |
##  DFT计算

| 作业序号 | 结构               | 收敛  |
| ---- | ---------------- | --- |
| 031  | Na2O2-mp-2340    | y   |
| 032  | Na2O2-mp-1061395 | n   |
| 033  | Na2O2-mp-1094115 | y   |
| 034  | Na2O2-mp-1180226 | n   |
| 035  | Na2O-mp-2352     | y   |
| 036  | NaO2-mp-614      | y   |
| 037  | NaO2-mp-1901     | y   |
| 038  | NaO2-mp-1180346  | n   |
| 039  | Na-mp-10172      | y   |
| 040  | O2               | y   |

**INCAR**
```INCAR
Electronic relaxation:
ENCUT   = 500  
ALGO    = FAST             
NELM    = 100             
EDIFF   = 1E-6      
AMIX    = 0.20      
BMIX    = 0.0010    
AMIX_MAG    = 0.80
BMIX_MAG    = 0.0010

Calculation mode:
PREC    = Accurate
ISPIN   = 1               
ADDGRID = .TRUE.    
LASPH   = .TRUE.    
ISYM    = 0         
ICHARG = 1
LREAL = AUTO

Integration over the Brillouin zone (BZ):
ISMEAR  = 1         
SIGMA   = 0.20        

Ionic relaxation:
NSW     = 100  
EDIFFG  = -0.02     
IBRION  = 2              
POTIM   = 0.10        

DOS calculation:
LORBIT  = 11        

for dipol correction:
LDIPOL = .TRUE.
IDIPOL = 3    
LWAVE = .TRUE.

#ISIF = 3  # 收敛后ISIF=3续算

VDW: 
IVDW = 12

Solution:
#LSOL = .TRUE.

NCORE = 8

```

注意到O2和O2^2- 离子有磁性，需要开启自旋极化计算。

考虑到晶胞对称性的问题，尝试使用`ISIF = 8`，只改变晶胞体积，不改变晶胞形状来计算。