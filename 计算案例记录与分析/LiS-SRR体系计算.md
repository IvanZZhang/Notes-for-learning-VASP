Wu-group集群使用PBS作业管理，详见[[Wu-group集群使用手册]]

# 基底优化
## 建模

| 序号  | 结构          |
| --- | ----------- |
| 1   | C2N vacancy |
| 2   | Li1@C2N     |
| 3   | Cu1Li1@C2N  |
| 4   | 4Cu1@C2N    |
| 5   | 4Li1@C2N    |
| 6   | 4Cu1Li1@C2N |
作为测试，分别在有溶剂化和无溶剂化下各自跑了一遍。注意设置提交脚本中使用**vasp.5.4.4_std-sol-fix-C**版本

适配Wu-group集群运行环境，`NCORE` 参数设为8。

无溶剂化条件下结构均收敛。
有溶剂化条件下结构6长期不能收敛。推断可能需要弛豫晶格，尝试设置`ISIF=3`，允许晶格完全弛豫。*result:* POSCAR中晶格参数并没有发生变化，十步后仍不收敛。
再尝试手动修改Cu原子与Li原子位置，稍微偏离平面。同时改`IBRION=1`，使用RMM-DIIS方法。*result:* 仍然不能收敛。问题先搁置。*update:* 文献中结构Cu原子和Li原子是在平面两侧的。可以再试一下。

| 序号  | 结构        |
| --- | --------- |
| 7   | Li2-o@C2N |
| 8   | Li2-n@C2N |
| 9   | Li3@C2N   |
| 10  | Cu2-o@C2N |
| 11  | Cu2-n@C2N |
| 12  | Cu3@C2N   |
直接跑溶剂化。修改参数`ALGO=Normal`，`LDIPOL=.TRUE.` `IDIPOL=3`，加快计算速度。

- 任务7运行结束后报警告fatal error，建议`cp CONTCAR POSCAR`后重启计算。
- 任务8还没完成。
- 任务9运行时长过长并报警告，并且最后构型明显不对。手动重置POSCAR后重做。重做后没有收敛，但结构看起来还算可以接受。![[Pasted image 20251023204211.png]]
- 任务10运行满100个离子步。震荡明显，尝试缩小`AMIX`和`BMIX`处理。一百步没结束。结果还算可以。
- 任务11同样运行满100个离子步。发现优化构型和任务10一样，因此不再做额外计算。
- 任务12顺利完成。但是优化后构型同任务9，有点奇怪，手动重置POSCAR后重启计算。重启计算后顺利收敛。

决定全部重做这部分内容。需要修改的部分包括结构优化任务k点需要改为221，溶剂化处理改为单点计算。重做结构列表：1、2、3、4、5、7、9、10、12.
- 任务2中Li原子移动到位点正中间了，和周围原子距离过远可能不成键。
**INCAR**
```INCAR
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
ISPIN   = 2                # update: ISPIN = 1
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
EDIFFG  = -0.05     
IBRION  = 2              
POTIM   = 0.10        

DOS calculation:
LORBIT  = 11        

for dipol correction:
#LDIPOL = .TRUE.
#IDIPOL = 3    
LWAVE = .TRUE.

ISIF = 3

VDW: 
IVDW = 12

Solution:
#LSOL = .TRUE.

NCORE = 8

```
单点计算结果部分正常，部分一百电子步没能电子自洽，表现为非常高的自旋态。结构4、5、7、9、12自旋态异常。实际上这个体系应该是无磁性的，应该关闭自旋极化计算：`ISPIN = 1`重启计算。
- 结构1、2、3、4、5、12运行满一百步离子步，但后面大多数离子步很快收敛，也没有能量变化。
- 结构9报错 LAPACK: Routine ZPOTRF failed! 检查CONTCAR发现晶格结构完全崩溃，因此修改`ISIF = 2`重启计算。*update:* 解决方案正确。晶胞弛豫**慎开**。
- 结构7、10正常收敛。
添加溶剂化后结构12还是不收敛。尝试直接做scf。结构10验证可以直接溶剂化下SCF。

**最终策略**：无溶剂化结构优化至收敛，再开溶剂化SCF。