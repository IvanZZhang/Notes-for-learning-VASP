# 建模对象
吸附体：CO2、COOH、CO、无吸附
基底：无轴向配位，-O。

*Update 1* 
-O表现性能一般，故用同样方法计算-S, -X (X = F, Cl, Br)。-I因溶剂化条件下始终无法收敛暂时放弃。
# 计算内容
## 几何构型优化
dmol3预优化，离子步约50步收敛。
### INCAR
*Version 1*
未使用**隐式溶剂模型**，必须修改。
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
ISPIN   = 2                
ADDGRID = .TRUE.    
LASPH   = .TRUE.    
ISYM    = 0         

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
LWAVE = .TRUE.

VDW: 
IVDW = 12

NCORE =16

```

*Version 2*
已加入**隐式溶剂模型** `LSOL = .TRUE.`
如果报错就`dos2unix INCAR`操作一下

可以在去除隐式溶剂化模型的情况下运行至收敛，随后再加入隐式溶剂化模型继续运行，收敛速度会快很多。
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
ISPIN   = 2                
ADDGRID = .TRUE.    
LASPH   = .TRUE.    
ISYM    = 0         

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
LWAVE = .TRUE.

VDW: 
IVDW = 12

NCORE = 16

Solution:
LSOL = .TRUE.     #隐式溶剂模型
```

### KPOINTS
*Version 1*
gamma采样，2 2 2。不合适表面体系。已在引入隐式溶剂模型时废除。
```
COOH                         
0                         
G                        
2   2   2                    
0.0 0.0 0.0                 
EOF
```

*Version 2*
M-P采样，3 3 1。
```
Cu-porphyrin                   
0                         
M-P                        
3   3   1                    
0.0 0.0 0.0                 
EOF
```

### POSCAR
使用ms脚本xsd2pos_v2.1.pl转换

或者拷贝CONTCAR继续计算`cp CONTCAR POSCAR`

### POTCAR
命令拼接，按POSCAR顺序
`cat ~/newpbepseudo/H/POTCAR ~/newpbepseudo/C/POTCAR ~/newpbepseudo/N/POTCAR ~/newpbepseudo/O/POTCAR ~/newpbepseudo/Cu/POTCAR > POTCAR`

### job_submit
```
#!/bin/bash
#SBATCH -J ProjectName    
#SBATCH -N 1
#SBATCH -t 100:00:00
#SBATCH --ntasks-per-node=32
#SBATCH -p xahcnormal

module purge
module load compiler/intel/2017.5.239
module load mpi/intelmpi/2017.4.239

export MKL_DEBUG_CPU_TYPE=5 #加速代码
export MKL_CBWR=AVX2 #使cpu默认支持avx2
export I_MPI_PIN_DOMAIN=numa #内存位置与cpu位置绑定，加速内存读取。对于内存带宽要求高的计算提速明显

srun --mpi=pmi2 /work/share/aco00ctiul/vasp630/vasp.6.3.0/bin/vasp_std >> output 2 >&1

```
### **几何优化不收敛解决方案**
[[几何优化不收敛解决方案]]
几何优化不收敛问题主要见于-Br、-I配位。

- TEST1：移除隐式溶剂化模型，运行后迅速收敛。随后`cp CONTCAR POSCAR`替换输入文件。
- TEST2：令`EDIFF = 1E-6`重新运行，发现振荡。
- TEST3：令`IBRION = 1`，并`rm CHGCAR WAVECAR`重新计算。
- TEST4：真空算一步WAVECAR，随后写入并继续计算。（在-Br上成功，在-I上失败）
- TEST5：1、ALGO换成N。  2、LDIPOL=T和IDIPOL=3。

折中方案：去除溶剂模型运行至收敛，随后加入溶剂模型进行一次`NSW = 1`的单步运算。
## 态密度DOS
对初始构型和第一吸附中间体构型做态密度DOS计算。

先做一次**静态自洽SCF**，随后导出CHGCAR。SCF步骤中，需要注意的是要设定离子步`NSW = 0`，并且静态自洽输出CHGCAR，可以不输出WAVECAR。SCF步骤中KPOINTS与结构优化中使用相同的较小k点即可。

随后使用SCF得到的CHGCAR进行DOS计算，增大k点。
### SCF INCAR
*Version 2*
修改错误变量名，并引入隐式溶剂模型。
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
ISTART  = 0     #从头开始
ICHARG  = 2     #取原子电荷密度的叠加
PREC    = Accurate  
ISPIN   = 2           
ADDGRID = .TRUE.    
LASPH   = .TRUE.    
ISYM    = 0         

LREAL = AUTO

Integration over the Brillouin zone (BZ):
ISMEAR  = 1         
SIGMA   = 0.20        

Ionic relaxation:
NSW     = 0       #离子步0
EDIFFG  = -0.05     
IBRION  = -1      #无更新 
POTIM   = 0.10        

DOS calculation:
LORBIT  = 11        

for dipol correction:   
LCHARG  = .TRUE.   #输出CHGCAR
LWAVE   = .FALSE.  #不输出WAVECAR

VDW: 
IVDW = 12

NCORE = 16

LSOL = .TRUE.

```

### DOS INCAR
*Version 2*
引入隐式溶剂模型
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
ISTART  = 1      #以恒定能量截止重启
ICHARG  = 11     #使用给定CHGCAR计算
PREC    = Accurate  
ISPIN   = 2          
ADDGRID = .TRUE.    
LASPH   = .TRUE.    
ISYM    = 0         

LREAL = AUTO

Integration over the Brillouin zone (BZ):
ISMEAR  = 1         
SIGMA   = 0.20        

Ionic relaxation:
NSW     = 0 
EDIFFG  = -0.05     
IBRION  = -1      #无更新        
POTIM   = 0.10        

DOS calculation:
LORBIT  = 11        

for dipol correction:   
LWAVE   = .FALSE.
LCHARG  = .FALSE.

VDW: 
IVDW = 12

NCORE = 16

LSOL = .TRUE.

```

### DOS KPOINTS
```
DOS KPOINTS                
0                         
M-P                        
11   11   1                    
0.0 0.0 0.0                 
EOF
```

### 静态自洽SCF快速命令
```
mkdir SCF
cp CONTCAR SCF/POSCAR
cp KPOINTS SCF/KPOINTS
cp POTCAR SCF/POTCAR
cd SCF
```
最后上传SCF INCAR，重命名为INCAR，并执行一次`dos2unix INCAR`

### 态密度DOS快速命令
```
mkdir DOS
cp SCF/CHGCAR DOS/CHGCAR
cp SCF/POSCAR DOS/POSCAR
cp POTCAR DOS/POTCAR
cd DOS
```

**一定确保进入了SCF和DOS的目录下再`qsub job_submit`**


# 数据处理
## DOS/PDOS
作图标准：
*DOS*
```
x -26 ~ 8
y -21 ~ 21
```
*PDOS*
```
x -26 ~ 8
y -12 ~ 12
```

## 吸附能计算
### 中间体物料守恒
能量计算需要保证中间体物料守恒。第一中间体到第二中间体需要加上半个H2分子的能量，第二中间体到第三中间体需要再加上半个H2分子能量，随后扣除一个水分子能量。这意味着需要在相同条件下计算一个H2分子和一个H2O分子的能量。气体分子k点常用111。

计算H2分子和H2O分子能量时，**不使用隐式溶剂化模型**。
### 矫正自由能计算
命令行脚本命令`gtff`，这会调用环境变量`fqcCal.sh`和**本文件夹内复制的一份**`freeze`，创建一个文件夹`fqc`并配置好输入文件。随后在`POSCAR`中冻结基底原子，解冻吸附原子，提交任务计算后执行脚本`vfcals.py`获取自由能。
