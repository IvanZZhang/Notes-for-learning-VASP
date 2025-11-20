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
| 113 | Pt-In2O3(111)-3-负载 |         |
| 114 | Pt-In2O3(111)-4-负载 |         |
| 115 | In-bulk            |         |
**再重做**：111到底是哪个面？
![[Pasted image 20251120215951.png]]
![[Pasted image 20251120220058.png]]
![[Pasted image 20251120220121.png]]
![[Pasted image 20251120224017.png]]
![[Pasted image 20251120230035.png]]
![[Pasted image 20251120230155.png]]

| 序号  | 结构                 | 收敛      |
| --- | ------------------ | ------- |
| 110 | In2O3-bulk         | y       |
| 111 | Pt-In2O3(111)-1-吸收 | 表面O原子散开 |
| 112 | Pt-In2O3(111)-2-吸收 | 表面O原子散开 |
| 113 | Pt-In2O3(111)-3-负载 |         |
| 114 | Pt-In2O3(111)-4-负载 |         |
| 115 | In-bulk            |         |
# WO2.72
## 建模
MP里没搜到WO2.72，用了师姐发的数据

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
| 128 | Pt-WO-1         |     |
| 129 | Pt-WO-2         |     |
|     |                 |     |
PAW应选用W_pv，但是没找到，默认的是W_sv。

收敛正常，精度太高导致离子步100步后没有收敛，降低精度继续计算。

结构121可能晶胞太小，因此扩胞一倍后再计算。结构121、122收敛。

复制晶体并放入30\*30\*30盒子。

感觉晶面实际上应该是020，切胞切错了，理解有误。需要进一步确认。

结构124能自洽，但计算量太大。降低精度后继续。结构125的ncg大到离谱。解决方案是KPOINTS改成gamma点111.

*update:* 经讨论，不需要做团簇体系了，统一做晶面。需要注意的是，不同体系的晶面之间，负载单原子的化学环境基本相同，即配位数尽量相同。以此原则筛选位点。另外还需要计算表面的功函数。

