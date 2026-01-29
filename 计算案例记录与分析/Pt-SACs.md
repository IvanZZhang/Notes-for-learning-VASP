路军岭Nature后续工作
# In2O3
## 建模
- 晶面切胞(110)
- 20真空层，并且移动位置到2

后续实验侧表示配位数无法测定，晶面数据尚未给出，因此先做全套晶面(101) (011) (111)

文献常用晶面(110) (111)
表征晶面(222)  (321) (411)

师姐发的晶面有缺陷，于是去Material Projects上重新找晶体数据
## 计算

| 序号  | 结构                       | 收敛  |
| --- | ------------------------ | --- |
| 101 | In2O3(110)-slab          | n   |
| 102 | In2O3(111)-slab          | y   |
| 103 | In2O3(321)-slab          | n   |
| 104 | In2O3(411)-slab          | n   |
| 105 | In2O3(111)-slab-Osurface | y   |
| 106 | Pt-In2O3(111)-1-吸收       |     |
| 107 | Pt-In2O3(111)-2-吸收       |     |
| 108 | Pt-In2O3(111)-3-负载       |     |
| 109 | Pt-In2O3(111)-4-负载       |     |

根据文献描述，截断能520eV，kpoints 3 3 1。*update:* 改为gamma点111，加速计算。电子自洽标准设为1E-4，设太小难收敛。

102中间有几步快速收敛。调试后INCAR如下：
```INCAR
Electronic relaxation:
ENCUT   = 520.0
ALGO    = FAST
NELM    = 300
EDIFF   = 1E-6   # 1E-4
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
#ICHARG = 1
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

#ISIF = 3

VDW:
IVDW = 12

Solution:
#LSOL = .TRUE.

NCORE = 8
```

101、103、104一百步未收敛。测试300步电子步能否收敛。*update:* 不能。继续改`ALGO = N`尝试收敛。
报错 ERROR FEXCP: supplied Exchange-correletion table is too small, maximal index :  7257，在提交脚本里加入以下三行貌似解决了。
```sub.sh
ulimit -s unlimited
export I_MPI_ADJUST_REDUCE=3
export MPIR_CVAR_COLL_ALIAS_CHECK=0
```
但仍然不收敛。

目前只有结构102，即(111)晶面顺利收敛。

修改111晶面，使O原子处于表层，重新运行计算。

结构运行时间很长。能收敛但一百步没结束。

**重做**：先优化bulk，再切面加真空层，再锁底层优化表面。

| 序号  | 结构                 | 收敛      |
| --- | ------------------ | ------- |
| 110 | In2O3-bulk         | y       |
| 111 | Pt-In2O3(111)-1-吸收 | 表面O原子散开 |
| 112 | Pt-In2O3(111)-2-吸收 | 表面O原子散开 |
| 113 | Pt-In2O3(111)-3-负载 | update  |
| 114 | Pt-In2O3(111)-4-负载 | update  |
| 115 | In-bulk            |         |
| 116 | In2O3-slab         |         |
**再重做**：111到底是哪个面？
![[Pasted image 20251120215951.png]]
![[Pasted image 20251120220058.png]]
![[Pasted image 20251120220121.png]]
![[Pasted image 20251120224017.png]]
![[Pasted image 20251120230035.png]]
![[Pasted image 20251120230155.png]]
[[Discuss 251121]]

| 序号  | 结构                 | 收敛      |
| --- | ------------------ | ------- |
| 110 | In2O3-bulk         | y       |
| 111 | Pt-In2O3(111)-1-吸收 | 表面O原子散开 |
| 112 | Pt-In2O3(111)-2-吸收 | 表面O原子散开 |
| 113 | Pt-In2O3(111)-3-负载 |         |
| 114 | Pt-In2O3(111)-4-负载 |         |
| 115 | In-bulk            |         |
| 116 | In2O3-slab         |         |


**INCAR** *update* 讨论后修正INCAR，修正后能很快收敛，应力残余消除。
```Opt
Electronic relaxation:
ENCUT   = 450     
ALGO    = F
NELM    = 300             
EDIFF   = 1E-5
AMIX    = 0.20      
BMIX    = 0.0010    
#AMIX_MAG    = 0.80
#BMIX_MAG    = 0.0010

Calculation mode:
PREC    = Normal
ISPIN   = 1        
#ADDGRID = .TRUE.    
#LASPH   = .TRUE.    
ISYM    = 0         
#ICHARG = 1
LREAL = AUTO

Integration over the Brillouin zone (BZ):
ISMEAR  = 0        
SIGMA   = 0.05       

Ionic relaxation:
NSW     = 100  
EDIFFG  = -0.05     
IBRION  = 2              
POTIM   = 0.5        

DOS calculation:
LORBIT  = 11        

for dipol correction:
#LDIPOL = .TRUE.
#IDIPOL = 3    
LWAVE = .TRUE.

ISIF = 8 # or 3?
#MAXMIX = 50

VDW: 
IVDW = 12

Solution:
#LSOL = .TRUE.

NCORE = 4

```
# WO2.72
## 建模
MP里没搜到WO2.72，用了师姐发的数据。实际上是有的，搜W18O49可得。

表征晶面(200)

## 计算
| 序号  | 结构              | 收敛  |
| --- | --------------- | --- |
| 121 | WO(200)-slab-33 | y   |
| 122 | WO(200)-slab-66 | y   |
| 123 | WO-66           |     |
| 124 | WO-020-slab     | y   |
| 125 | WO-020          | n   |
| 126 | WO              |     |
| 127 | W-bulk          |     |
| 128 | Pt-WO-1         | y   |
| 129 | Pt-WO-2         | y   |
| 130 | WO-slab         |     |
| 131 | Pt-WO-3         |     |
| 132 | Pt-WO-4         |     |
| 133 | Pt-WO-5（有氧空位）   |     |
| 134 | Pt-WO-6（有氧空位）   |     |
PAW应选用W_pv，但是没找到，默认的是W_sv。

收敛正常，精度太高导致离子步100步后没有收敛，降低精度继续计算。

结构121可能晶胞太小，因此扩胞一倍后再计算。结构121、122收敛。

复制晶体并放入30\*30\*30盒子。

感觉晶面实际上应该是020，切胞切错了，理解有误。需要进一步确认。

结构124能自洽，但计算量太大。降低精度后继续。结构125的ncg大到离谱。解决方案是KPOINTS改成gamma点111.

*update:* 经讨论，不需要做团簇体系了，统一做晶面。需要注意的是，不同体系的晶面之间，负载单原子的化学环境基本相同，即配位数尽量相同。以此原则筛选位点。另外还需要计算表面的功函数。

先计算WO的DOS验证结果。
### SCF
**KPOINTS**
M-P 3 3 1

**INCAR**
```SCF-INCAR
Electronic relaxation:
ENCUT   = 450.0      
ALGO    = F       
NELM    = 60            
EDIFF   = 1E-5
AMIX    = 0.20      
BMIX    = 0.0010  
#AMIX_MAG    = 0.80
#BMIX_MAG    = 0.0010

Calculation mode:
PREC    = N
ISPIN   = 1        
#ADDGRID = .TRUE.    
#LASPH   = .TRUE.    
ISYM    = 0         
#ICHARG = 1
ISTART = 0
LREAL = AUTO

Integration over the Brillouin zone (BZ):
ISMEAR  = 0         
SIGMA   = 0.05        

Ionic relaxation:
NSW     = 0  
EDIFFG  = -0.05     
IBRION  = -1              
POTIM   = 0.50        

DOS calculation:
LORBIT  = 11        

for dipol correction:
#LDIPOL = .TRUE.
#IDIPOL = 3    
LWAVE = .FALSE.
LCHARG = .TRUE.

#ISIF = 3
#MAXMIX = 50

VDW: 
IVDW = 12

Solution:
#LSOL = .TRUE.

NCORE = 4
```

### DOS
**KPOINTS**
M-P 6 6 1

**INCAR**
```DOS-INCAR
Electronic relaxation:
ENCUT   = 450.0      
ALGO    = F       
NELM    = 60            
EDIFF   = 1E-5
AMIX    = 0.20      
BMIX    = 0.0010  
#AMIX_MAG    = 0.80
#BMIX_MAG    = 0.0010

Calculation mode:
PREC    = N
ISPIN   = 1        
#ADDGRID = .TRUE.    
#LASPH   = .TRUE.    
ISYM    = 0         
ICHARG = 11
ISTART = 1
LREAL = AUTO

Integration over the Brillouin zone (BZ):
ISMEAR  = 0     # 正常用0高斯展宽就行了
SIGMA   = 0.05        

Ionic relaxation:
NSW     = 0  
EDIFFG  = -0.05     
IBRION  = -1              
POTIM   = 0.50        

DOS calculation:
#EMIN = -10
#EMAX = 10
NEDOS = 1001
LORBIT  = 11        

for dipol correction:
#LDIPOL = .TRUE.
#IDIPOL = 3    
LVHAR = .TRUE.
LWAVE = .FALSE.
LCHARG = .FALSE.

#ISIF = 3
#MAXMIX = 50

VDW: 
IVDW = 12

Solution:
#LSOL = .TRUE.

NCORE = 4
```
重启DOS计算前需要重新复制一份CHGCAR

**半导体`ISMEAR = -5`**。。？这部分不确定！表面态似乎不需要这个，设为0即可。

采点数`NEDOS = 1001`

WO位点选取有问题，吸附氢气能力完全没有！吸附能为零
# 其他分子
| 序号  | 结构    | 收敛  |
| --- | ----- | --- |
| 001 | H2    |     |
| 002 | PhNO2 |     |
| 003 | PhNH2 |     |
分子也需要计算能级

# CeO2
| 序号  | 结构            | 收敛  |
| --- | ------------- | --- |
| 140 | CeO2-bulk     |     |
| 141 | Pt-CeO2-1     |     |
| 142 | Pt-CeO2-2     |     |
| 143 | CeO2-111-slab |     |
一个问题：选择哪个赝势？
Ce/    Ce_3/  Ce_GW/ Ce_h/
# 吸附
Fukui函数确定加氢位置
![[Pasted image 20251202214038.png]]
横式吸附
![[Pasted image 20251202214105.png]]

这是一个电催化反应，可以绘制电催化台阶图。不过我们先简单考虑一头一尾的吸附能。
讨论水平吸附还是垂直吸附
（别忘了固定基底）

| 基底/吸附物         | PhNO2*-parallel | PhNO2*-vertical |
| -------------- | --------------- | --------------- |
| Pt-WO2.72      | 011             | 012             |
| Pt-In2O3（无氧空位） | 021             | 022             |
| Pt-In2O3       | 031             | 032             |

| 吸附物/基底          | Pt-WO2.72 | Pt-In2O3（无氧空位） | Pt-In2O3 |
| --------------- | --------- | -------------- | -------- |
| PhNO2*-parallel | 01-01     | 02-01          | 03-01    |
| PhNO2*-vertical | 01-02     |                |          |
| PhNOOH*         | 01-03     |                |          |
| PhNO*           | 01-04     |                |          |
| PhNOHOH*        | 01-05     |                |          |
| PhNHO*          | 01-06     |                |          |
| PhNOH*          | 01-07     |                |          |
| PhNHOH*         | 01-08     |                |          |
| PhN*            | 01-09     |                |          |
| PhNH*           | 01-10     |                |          |
| PhNH2*          | 01-11     |                |          |
| H*-邻            | 01-12     | 02-12          | 03-12    |
| H*-其他           | 01-13     | 02-13          | 03-13    |

经计算吸附能和测量N-O键长，选择垂直吸附构型。

验证表面对氢原子吸附，形成羟基的倾向。邻位/其他位
## 自由能校正
手动修改参数，参考教程 [频率计算 · GitBook](https://neetsaki.github.io/QC/vasp-tut/Chapter3/3-1.html)

```
IBRION=5 #freq calc
POTIM=0.02 #一个更小的值，default:0.015
NSW=1  # any value which >1
NFREE=2 # do not set NFREE=1，添加这一个参数,表明原子在某一方向上正反两个方向移动
# NCORE=4 并行计算频率时VASP会罢工
EDIFFG=1E-6 #严格一些，需要保证准确
```
# 数据
负载Pt原子的PDOS

### 功函数
需要计算功函数，来获取能级的绝对值用来比较。功函数需要在电子自洽时添加`LVHAR = .TRUE.`，通过`vaspkit 426`命令获得静电势，DOS作图是在费米能级校正的情况下再减去静电势。

### 吸附能
计算WO、In2O3、CeO2三个体系的吸附能，需要做频率校正

### 关于解氢
和师姐讨论了一下，决速步可能是解氢，这意味着我需要评估氢气分子吸附上去后，变成两个质子的反应。

这涉及到Pt原子的给电子的问题，因此需要确定一下考虑吸附物的HOMO还是LUMO。

解氢反应

| 吸附物/基底 | Pt-WO2.72 | Pt-In2O3（无氧空位） | Pt-CeO2 |
| ------ | --------- | -------------- | ------- |
| H*     | 01-14     | 02-14          | 03-14   |
| H2*    | 01-15     | 02-15          | 03-15   |
H2直接在Pt上的吸附并不怎么好。

| 吸附物/基底   | Pt-WO2.72 | Pt-In2O3（无氧空位） | Pt-CeO2 |
| -------- | --------- | -------------- | ------- |
| H2+PHNO2 | 01-16     | 02-16          | 03-16   |
### 能带
为了验证费米能级上方的小而宽的空能级到底是什么？因此尝试绘制能带。

使用`vaspkit 302`命令创建KPATH文件，作为能带计算的KPOINTS文件。其他参数设置和DOS计算一样。

后面把静态计算的DOSCAR复制到band文件夹里，运行`vaspkit 211`命令，修正费米能级。