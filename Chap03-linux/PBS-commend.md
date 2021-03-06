# 使用PBS管理作业的几种命令

## 提交作业

用以下命令提交作业：

```shell
$ qsub [options] <control script>
```

命令执行后会返回一个作业ID。如：

```
[zjb@op back]$ qsub -N thisIsName vasp.pbs 
201848.inode202
```

作业提交后一般会先排队等待，PBS 系统会根据作业的优先级和可用的计算资源来调度和执行作业。

其中`-N thisIsName`为选项，它指定了作业的名称。`vasp.pbs`为PBS脚本，它设定了作业的属性和作业的内容。`options`与`control script`详见 [`## PBS脚本`](./PBS-script.html)。

> 文档中，命令带有`$`符号的，表示是在shell终端输入的命令，实际输入的时候不需要带有这个符号。

## 查看任务

```shell
$ qstat [option] <jobid>
```

如：

```
[zjb@op back]$ qstat
Job ID                    Name             User            Time Use S Queue
------------------------- ---------------- --------------- -------- - -----
201848.inode202            thisIsName       zjb             00:00:00 C energy         
```

其中，`S`表示任务的状态，有如下几种状态：

| 状态(S) | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| B       | 只用于任务向量，表示任务向量已经开始执行                     |
| E       | 任务在运行后退出                                             |
| H       | 任务被服务器或用户或者管理员阻塞                             |
| **Q**   | **任务正在排队中，等待被调度运行**                           |
| **R**   | **任务正在运行**                                             |
| **C**   | **任务完成**                                                 |
| S       | 任务被服务器挂起，由于一个更高优先级的任务需要当前任务的资源 |
| T       | 任务被转移到其它执行节点了                                   |
| U       | 由于服务器繁忙，任务被挂起                                   |
| W       | 任务在等待它所请求的执行时间的到来(qsub -a)                  |
| X       | 只用于子任务，表示子任务完成                                 |

### qstat 的选项

| 命令             | 说明                   |
| ---------------- | ---------------------- |
| qstat -q         | 列出所有队列           |
| qstat -a         | 列出所有作业           |
| qstat -u user_id | 列出user_id的所有作业  |
| qstat -r         | 列出所有正在运行的作业 |
| qstat -f job_id  | 列出作业job_id的信息   |
| qstat -fQ queue  | 列出队列queue的信息    |
| qstat -B         | 列出所有作业状态的汇总 |
| pbsnodes         | 列出所有节点的详细信息 |
| pestat           | 列出所有节点的状态     |

## 取消任务

```shell
$ qdel <jobid>
```

## 停止正在运行的任务

```shell
$ qsig -s KILL <job ID>
```

## checkjob

```
checkjob <job id>
```

注意这儿的`job id`不包括`.inodexxx`，比如上述的`201848.inode202`作业，如果使用`checkjob`查询，命令应为`checkjob 201848`。

```
[zjb@op testEnergy]$ checkjob 201848


checking job 201848

State: Running
Creds:  user:zjb  group:energy  account:SugonClus  class:energy  qos:preemptee
WallTime: 00:02:03 of 00:30:00
SubmitTime: Sat Oct 31 19:45:48
  (Time Queued  Total: 00:00:01  Eligible: 00:00:01)

StartTime: Sat Oct 31 19:45:49
Total Tasks: 4

Req[0]  TaskCount: 4  Partition: DEFAULT
Network: [NONE]  Memory >= 0  Disk >= 0  Swap >= 0
Opsys: [NONE]  Arch: [NONE]  Features: [NONE]
Allocated Nodes:
[inode53:4]


IWD: [NONE]  Executable:  [NONE]
Bypass: 0  StartCount: 1
PartitionMask: [ALL]
Flags:       BACKFILL RESTARTABLE PREEMPTEE
Attr:        PREEMPTEE

Reservation '201868' (-00:02:04 -> 00:27:56  Duration: 00:30:00)
PE:  4.00  StartPriority:  6000
```

如果任务无法运行，可以这样查询原因。

## pbsnodes

查看节点的空闲情况。有`free`、`busy`、`down`等状态。