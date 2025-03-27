原文索引： https://mp.weixin.qq.com/s/Ufw7lX-Zo_1YNFr5HkGlQQ

Optimization 简称opt计算或者优化。  

- 电子步：自洽迭代时一次迭代称作一个电子步d。  
- 离子步：一次自洽计算称作一个离子步。自洽计算通常需要进行多步迭代，即一个离子步包含多个电子步。  
- 结构优化的过程：VASP 会对当前结构进行自洽迭代获得该结构的电荷密度，计算该结构的能量和原子受力，然后判断是否达到结构优化收敛判据。若没达到，则生成新结构，使用新结构进行自洽计算。如此往复，直至达到收敛判据(EDIFFG)或者达到设定的最大离子步数 (NSW)，计算退出。  

几种可进行分步优化的例子  
- 在优化带有金属原子体系时，由于自旋设置（ISPIN = 2）开启计算速度减慢，所有可以先进行不开自旋的计算，后复制CONTCAR文件作为新的POSCAR再进行计算。
    
- 在计算时也可以先进行ISIF = 2只对原子位置的优化，再进行ISIF = 3对晶胞和原子同时的优化。
    
- 需要高精度优化时，可以先进行底精度优化，再慢慢提高精度。
    

下图为VASP计算完成后的文件列表：![图片](https://mmbiz.qpic.cn/mmbiz_png/eia95zdMz6BvESHkDrokP9a3O09YZoqKl4kNtnPrqktPvCfHPpyTU9SezxJPSh6ibiarfWD7gTzhcibYo1jxibj9fyQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

主要文件CONTCAR、OSZICAR、OUTCAR、CHGCAR、WAVECAR、CHG等

# CONTCAR：用来保存优化计算完之后的结构文件

```
graphite                                  
   1.00000000000000       
     2.4600000381000000    0.0000000000000000    0.0000000000000000  
    -1.2300000191000000    2.1304225262999998    0.0000000000000000  
     0.0000000000000000    0.0000000000000000   15.0000000000000000  
   C   
     2  
Direct  
  0.0000000000000000  0.0000000000000000  0.2500000000000000  
  0.3333300050000005  0.6666700239999983  0.2500000000000000  
   
  0.00000000E+00  0.00000000E+00  0.00000000E+00  
  0.00000000E+00  0.00000000E+00  0.00000000E+00
```

与POSCAR文件相似：  

- 第一行：随便写没有实际意义。
    
- 第二行：缩放系数，一般都是1.0。
    
- 第三到五行：晶格的坐标信息，表示在直角坐标系xyz中的三条向量组成的晶胞。
    
- 第六行：元素行，有几种元素挨个写入即可。
    
- 第七行：与第六行中元素相对应的原子数目。
    
- 第八行：体系中原子的坐标系，可以为笛卡尔坐标/分数坐标。
    
- 第九行之后到空格：体系中原子的具体位置坐标。
    
- 与POSCAR的不同：但是在体系中原子的具体位置坐标之后会空一行输出0.00000000E+00的字符，此处有几个原子就会多出几行，在优化中无特别意义，在分子动力学中，代表的是分子移动的速度；在dimer方法算过渡态中，代表的是与过渡态结构相关的振动方式。

# OSZICAR：用来记录优化过程中每一步能量等信息的文件
```
       N       E                     dE             d eps       ncg     rms          rms(c)
DAV:   1     0.404093919796E+02    0.40409E+02   -0.33922E+03  5440   0.416E+02
DAV:   2    -0.159640581413E+02   -0.56373E+02   -0.52046E+02  7752   0.107E+02
DAV:   3    -0.189007510659E+02   -0.29367E+01   -0.29214E+01  7184   0.249E+01
DAV:   4    -0.189303329779E+02   -0.29582E-01   -0.29575E-01  6248   0.321E+00
DAV:   5    -0.189307698824E+02   -0.43690E-03   -0.43690E-03  7216   0.346E-01    0.860E+00
RMM:   6    -0.135295858072E+02    0.54012E+01   -0.29952E+00  5440   0.750E+00    0.456E+00
RMM:   7    -0.155081794183E+02   -0.19786E+01   -0.12792E+00  5440   0.481E+00    0.236E+00
RMM:   8    -0.164966928906E+02   -0.98851E+00   -0.64285E-01  5450   0.301E+00    0.135E+00
RMM:   9    -0.176275503457E+02   -0.11309E+01   -0.66519E-01  5440   0.286E+00    0.829E-01
RMM:  10    -0.183162382091E+02   -0.68869E+00   -0.41051E-01  5440   0.198E+00    0.310E-01
RMM:  11    -0.184122879024E+02   -0.96050E-01   -0.48812E-02  5440   0.620E-01    0.172E-01
RMM:  12    -0.184184148781E+02   -0.61270E-02   -0.52886E-03  5440   0.266E-01    0.878E-02
RMM:  13    -0.184381276039E+02   -0.19713E-01   -0.16798E-03  5440   0.154E-01    0.350E-02
RMM:  14    -0.184451357381E+02   -0.70081E-02   -0.20306E-04  5399   0.550E-02    0.163E-02
RMM:  15    -0.184462442385E+02   -0.11085E-02   -0.10719E-04  5047   0.336E-02    0.101E-02
RMM:  16    -0.184467221924E+02   -0.47795E-03   -0.52396E-05  4899   0.242E-02    0.756E-03
RMM:  17    -0.184471300223E+02   -0.40783E-03   -0.24309E-05  4884   0.148E-02    0.501E-03
RMM:  18    -0.184472381488E+02   -0.10813E-03   -0.50586E-06  4766   0.101E-02    0.382E-03
RMM:  19    -0.184473169052E+02   -0.78756E-04   -0.36126E-06  4763   0.775E-03    0.145E-03
RMM:  20    -0.184473240236E+02   -0.71184E-05   -0.14247E-07  4144   0.256E-03
   1 F= -.18555959E+02 E0= -.18555959E+02  d E =-.185560E+02  mag=    -0.0002
```
在例子中结构只进行了一个离子步的优化就达到了收敛标准。其中：  

- 第二列N: 代表电子结构的迭代步数，也就是电子步的步数。
    
- 第三列E 代表这一电子步时体系的能量。
    
- 第四列dE代表这一电子步与上一步体系能量的差值。这一差值在小于设定值EDIFF后停止电子步计算。  
    

最后一排中：

- F前面的数字代表着离子步的步数，在这一计算中只有一步。  
    
- E0后面的是体系的能量，一般读取能量时都读取这一数值。
    
- dE后面的是两离子步之间体系的能量差。
    
- mag后面的是体系的总磁矩，一般不看这一数值，而是看OUTCAR中输出的单个原子上的磁矩。

# OUTCAR：包含VASP计算的详细信息与汇总的文件
```
 reached required accuracy - stopping structural energy minimisation  
     LOOP+:  cpu time   15.7255: real time   15.9649  
    4ORBIT:  cpu time    0.0000: real time    0.0000  
  
 total amount of memory used by VASP MPI-rank0    34111. kBytes  
=======================================================================  
  
   base      :      30000. kBytes  
   nonl-proj :       1269. kBytes  
   fftplans  :        451. kBytes  
   grid      :       1080. kBytes  
   one-center:          6. kBytes  
   wavefun   :       1305. kBytes  
   
    
    
 General timing and accounting informations for this job:  
 ========================================================  
    
                  Total CPU time used (sec):       16.968  
                            User time (sec):       15.481  
                          System time (sec):        1.487  
                         Elapsed time (sec):       17.670  
    
                   Maximum memory used (kb):      100692.  
                   Average memory used (kb):           0.  
    
                          Minor page faults:        67291  
                          Major page faults:            0  
                 Voluntary context switches:         1638
```
出现类似样式的OUTCAR结尾时，说明计算的任务正常完成了。  
使用grep accuracy OUTCAR命令查看结构是否收敛，如果收敛了会出现下面这一行文字。  
![图片](https://mmbiz.qpic.cn/mmbiz_png/eia95zdMz6BvESHkDrokP9a3O09YZoqKlWoTb9FpbA4rpDwaFYWfw8ZwGKCI6R2PaG0Diac95MKm1Fibd9gwib4kgQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

使用grep E-fermi OUTCAR命令查看结构的费米能级。E-fermi后第一个数字就是费米能级。  
![图片](https://mmbiz.qpic.cn/mmbiz_png/eia95zdMz6BvESHkDrokP9a3O09YZoqKlibgSMhvDcqViaibupbtkWFjDQD0yMzI8b1kmekicn7UfqPmNibPnJzQwJDw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

使用grep without OUTCAR | tail -1命令查看结构的总能量，取energy(sigma->0)后面的能量。  
![图片](https://mmbiz.qpic.cn/mmbiz_png/eia95zdMz6BvESHkDrokP9a3O09YZoqKl5qNnIL0MgnS10ibpVTeUOP71I6ul28lf5vq5xZBHnK4sk3ET5vCXnAQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

OUTCAR中还有原子电荷磁矩等各种信息，在后面用到的时候在单独说明。

# CHGCAR：装入体系电子信息的文件

一般可以不选择输出，在要计算能带、DOS、电荷差分、Bader电荷等性质时再进行单点计算输出。

# WAVECAR：装入体系波函数信息的文件

一般可以不选择输出，在要计算隐式溶剂效应（sol）等性质时再进行单点计算输出。

# log后缀文件

每个服务器上的命名可能不同，包含部分输入信息（计算的核数，k点，vasp版本，是否读取波函数等），输出能量等信息，收敛信息或报错等。包含但不止与OSZICAR中的信息，个人感觉比OSZICAR方便，可以同时看见是否收敛与优化后的体系能量。
```
running on   96 total cores  
 distrk:  each k-point on   96 cores,    1 groups  
 distr:  one band on   12 cores,    8 groups  
 using from now: INCAR       
 vasp.5.4.4.18Apr17-6-g9f103f2a35 (build Jan 10 2023 19:59:37) complex            
    
 POSCAR found type information on POSCAR  C   
 POSCAR found :  1 types and       2 ions  
 scaLAPACK will be used  
 LDA part: xc-table for Pade appr. of Perdew  
 POSCAR found type information on POSCAR  C   
 POSCAR found :  1 types and       2 ions  
 POSCAR, INCAR and KPOINTS ok, starting setup  
 FFT: planning ...  
 WAVECAR not read  
 entering main loop  
       N       E                     dE             d eps       ncg     rms          rms(c)  
DAV:   1     0.404093919796E+02    0.40409E+02   -0.33922E+03  5440   0.416E+02  
DAV:   2    -0.159640581413E+02   -0.56373E+02   -0.52046E+02  7752   0.107E+02  
DAV:   3    -0.189007510659E+02   -0.29367E+01   -0.29214E+01  7184   0.249E+01  
DAV:   4    -0.189303329779E+02   -0.29582E-01   -0.29575E-01  6248   0.321E+00  
DAV:   5    -0.189307698824E+02   -0.43690E-03   -0.43690E-03  7216   0.346E-01    0.860E+00  
RMM:   6    -0.135295858072E+02    0.54012E+01   -0.29952E+00  5440   0.750E+00    0.456E+00  
RMM:   7    -0.155081794183E+02   -0.19786E+01   -0.12792E+00  5440   0.481E+00    0.236E+00  
RMM:   8    -0.164966928906E+02   -0.98851E+00   -0.64285E-01  5450   0.301E+00    0.135E+00  
RMM:   9    -0.176275503457E+02   -0.11309E+01   -0.66519E-01  5440   0.286E+00    0.829E-01  
RMM:  10    -0.183162382091E+02   -0.68869E+00   -0.41051E-01  5440   0.198E+00    0.310E-01  
RMM:  11    -0.184122879024E+02   -0.96050E-01   -0.48812E-02  5440   0.620E-01    0.172E-01  
RMM:  12    -0.184184148781E+02   -0.61270E-02   -0.52886E-03  5440   0.266E-01    0.878E-02  
RMM:  13    -0.184381276039E+02   -0.19713E-01   -0.16798E-03  5440   0.154E-01    0.350E-02  
RMM:  14    -0.184451357381E+02   -0.70081E-02   -0.20306E-04  5399   0.550E-02    0.163E-02  
RMM:  15    -0.184462442385E+02   -0.11085E-02   -0.10719E-04  5047   0.336E-02    0.101E-02  
RMM:  16    -0.184467221924E+02   -0.47795E-03   -0.52396E-05  4899   0.242E-02    0.756E-03  
RMM:  17    -0.184471300223E+02   -0.40783E-03   -0.24309E-05  4884   0.148E-02    0.501E-03  
RMM:  18    -0.184472381488E+02   -0.10813E-03   -0.50586E-06  4766   0.101E-02    0.382E-03  
RMM:  19    -0.184473169052E+02   -0.78756E-04   -0.36126E-06  4763   0.775E-03    0.145E-03  
RMM:  20    -0.184473240236E+02   -0.71184E-05   -0.14247E-07  4144   0.256E-03  
   1 F= -.18555959E+02 E0= -.18555959E+02  d E =-.185560E+02  mag=    -0.0002  
 curvature:   0.00 expect dE= 0.000E+00 dE for cont linesearch  0.000E+00  
 trial: gam= 0.00000 g(F)=  0.604E-07 g(S)=  0.000E+00 ort = 0.000E+00 (trialstep = 0.100E+01)  
 search vector abs. value=  0.605E-07  
 reached required accuracy - stopping structural energy minimisation
```