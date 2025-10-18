# PBS作业管理
Wu-group集群使用PBS作业管理
[查看原文](https://zhuanlan.zhihu.com/p/605138591)
## 操作命令
**提交作业命令** 'qsub'
示例：qsub vasp-sub.sh

**查询作业状态命令** 'qstat'
-f jobid 列出指定作业的信息  
-a 列出系统所有作业  
-i 列出不在运行的作业  
-n 列出分配给此作业的结点  
-s 列出队列管理员与scheduler 所提供的建议  
-R 列出磁盘预留信息  
-Q 操作符是destination id，指明请求的是队列状态  
-q 列出队列状态，并以alternative 形式显示  
-au userid 列出指定用户的所有作业  
-B 列出PBS Server 信息  
-r 列出所有正在运行的作业  
-Qf queue 列出指定队列的信息  
-u 若操作符为作业号，则列出其状态。

**删除已提交作业** 'qdel'
示例： qdel 1399.Dawn5

## PBS作业调度系统

| 功能      | PBS                       |
| ------- | ------------------------- |
| 任务名称    | # PBS -N name             |
| 指定队列/分区 | # PBS -q cpu              |
| 指定QoS   | # PBS --qos=debug，需要调度器支持 |
| 最长运行时间  | # PBS -l walltime=5:00    |
| 指定节点数量  | # PBS -l nodes=1          |
| 指定CPU核心 | # PBS -l ppn=4            |
| 指定GPU卡  | 不支持                       |
| 作业数组    | # PBS -t 0-2              |
| 输出文件    | # PBS -o test.out         |
| 提交任务脚本  | qsub run.pbs              |
| 查看任务状态  | qstat                     |
| 取消任务    | qdel 1234                 |
| 交互式任务   | qsub -I，自动切换              |
| 指定特定节点  | qsub -l nodes=comput1     |
| 挂起任务    | qhold                     |
| 取消挂起    | qrls                      |
| 交换作业顺序  | qorder                    |

# vasp编译的不同版本
## 主版本vasp.5.4.4
包含gam / ncl / std
**gam**：仅gamma点，优化速度
**ncl**：非线性，磁性体系会用到
**std**：标准版本

后缀名
**fix-C / fix-AB**：指定消除某轴上应力。例如**fix-C**版本消除C轴应力，适用含真空层的二维体系计算。
**sol**：溶剂化模型
**wannier90**：磁性体系会用到

## 其他版本
**vasp.6.1.0**：更新MLP适配
**vasp.6.4.0**：（在其他集群）声子谱消虚频

# vasp输入设置
注意默认2 batch node是$24\times2=48$核，按照开根号原则，**NCORE参数设置为6/8时计算速度最快**。

建议按**总核数开根号**计算。