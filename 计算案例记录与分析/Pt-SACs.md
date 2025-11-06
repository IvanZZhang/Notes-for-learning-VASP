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

| 序号  | 结构              | 收敛  |
| --- | --------------- | --- |
| 101 | In2O3(110)-slab | n   |
| 102 | In2O3(111)-slab | y   |
| 103 | In2O3(321)-slab | n   |
| 104 | In2O3(411)-slab | n   |

根据文献描述，截断能520eV，kpoints 3 3 1。

102中间有几步快速收敛。调试后INCAR如下：
```INCAR
Electronic relaxation:
ENCUT   = 520.0
ALGO    = FAST
NELM    = 300
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
| 125 | WO-020          | ？   |
PAW应选用W_pv，但是没找到，默认的是W_sv。

收敛正常，精度太高导致离子步100步后没有收敛，降低精度继续计算。

结构121可能晶胞太小，因此扩胞一倍后再计算。结构121、122收敛。

复制晶体并放入30\*30\*30盒子。

感觉晶面实际上应该是020，切胞切错了，理解有误。需要进一步确认。

结构124能自洽，但计算量太大。降低精度后继续。结构125的ncg大到离谱。解决方案是KPOINTS改成gamma点111.
