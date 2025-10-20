# 建模对象

| ~   | 无吸附 | \*O2 | \*OOH | \*O | \*OH |
| --- | --- | ---- | ----- | --- | ---- |
| 无配位 |     | /    |       |     |      |
| -F  |     | /    |       |     |      |
| -Cl |     | /    |       |     |      |
| -Br |     | /    |       |     |      |
| -I  | /   | /    | /     | /   | /    |
\*O2是否值得继续建模研究存疑。根据Norskov的催化理论，热力学上只需要研究催化反应的中间体，而不需要研究过渡态。\*O2分子的吸附应该不涉及电子转移步骤，因此可以不用考虑这个中间体。

碘元素研究时仍遇到先前同样问题，溶剂化条件下无法电子自洽，因此推测碘元素不适合目前使用的隐式溶剂化模型，可能由于体系电子数较多导致。

*update1:* 审稿人意见要求考虑位点间相互作用，因此计算裸位点与-Cl的6x6晶胞。
*update2:* Cl配位吸附构型报错，原因不明。先搁置这个问题，确认裸位点不受影响。
# 计算内容
## 磁性物质计算
MAGMOM设定Fe磁矩3, O磁矩2（实验磁矩1.2~1.5倍），分别设定NUPDOWN为1/2/3，算4步离子步，检查OSZICAR再关闭NUPDOWN，计算直到收敛，选择最低能量的构型及其匹配的自旋。
## 结构优化
### KPOINTS
M-P
3 3 1
### INCAR
*step 1*
打开NUPDOWN，按照POSCAR填写MAGMOM。*update:* 如果不同NUPDOWN收敛到同样mag，说明结构只有一个自旋态。
```
Electronic relaxation:
ENCUT   = 400.0      
ALGO    = FAST        
NELM    = 100         
EDIFF   = 1E-4      
AMIX    = 0.20      
BMIX    = 0.0010    
AMIX_MAG    = 0.80
BMIX_MAG    = 0.0010

Calculation mode:
PREC    = Accurate  
ISPIN   = 2         
MAGMOM = 26*0 4*0 1*3        #Fe-N4基础构型
NUPDOWN = 3                  #设定初始上下自旋电子数差
LMAXMIX = 4         
ADDGRID = .TRUE.    
LASPH   = .TRUE.    
ISYM    = 0         

LREAL = AUTO

Integration over the Brillouin zone (BZ):
ISMEAR  = 1         
SIGMA   = 0.20        

Ionic relaxation:
NSW     = 4     #简单跑四步
EDIFFG  = -0.05     
IBRION  = 2              
POTIM   = 0.10        

DOS calculation:
LORBIT  = 11        

for dipol correction:   
LWAVE = .TRUE.

VDW: 
IVDW = 12

NCORE = 16

#LSOL = .TRUE.
```

*step 2*
打开参数`ICHARG = 1`，读取电荷密度继续计算。
可以在这步就打开溶剂化，也可以在无溶剂条件下收敛后再打开。*update* 收敛速度并不快，不要这样做。
```
Electronic relaxation:
ENCUT   = 400.0      
ALGO    = FAST        
NELM    = 100         
EDIFF   = 1E-4      
AMIX    = 0.20      
BMIX    = 0.0010    
AMIX_MAG    = 0.80
BMIX_MAG    = 0.0010

Calculation mode:
ICHARG = 1      #读取CHGCAR继续计算

PREC    = Accurate  
ISPIN   = 2         
MAGMOM = 26*0 4*0 1*3         #Fe-N4基础构型
#NUPDOWN = 3                  #关闭参数，收敛磁矩
LMAXMIX = 4         
ADDGRID = .TRUE.    
LASPH   = .TRUE.    
ISYM    = 0         

LREAL = AUTO

Integration over the Brillouin zone (BZ):
ISMEAR  = 1         
SIGMA   = 0.20        

Ionic relaxation:
NSW     = 40     #开始收敛构型，不要忘了调
EDIFFG  = -0.05     
IBRION  = 2              
POTIM   = 0.10        

DOS calculation:
LORBIT  = 11        

for dipol correction:   
LWAVE = .TRUE.

VDW: 
IVDW = 12

NCORE = 16

#LSOL = .TRUE.      
```

## Pourbaix图计算验证稳定性
[[Pourbaix图的计算]]