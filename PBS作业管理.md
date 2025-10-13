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

