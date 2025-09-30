## linux常用命令
**以centos为例（这篇文章是佛系记录，不会全部写的。见谅）**

> 如果用的是ubuntu 请在命令前面加**sudo**

```linux

# 查看防火墙状态 
systemctl status firewalld.service
# 关闭防火墙
systemctl stop firewalld.service
# 禁止防火墙服务器（一旦重启操作系统，防火墙就自动开启了，该怎么设置才能永久关闭防火墙呢？）
systemctl disable firewalld.service

# 创建文件
mkdir

# 查询端口
netstat -tln|grep 80(端口)

# 查看端口是否被占用
lsof -i :80(端口)

# 查看80端口是否被监听
ss -tlnp | grep ':80'

# 查看当前所在地址
pwd

# ps系列的命令
ps 
参数：
-A ：所有的进程均显示出来，与 -e 具有同样的效用；
-a ： 显示现行终端机下的所有进程，包括其他用户的进程；
-u ：以用户为主的进程状态 ；
x ：通常与 a 这个参数一起使用，可列出较完整信息。
输出格式规划：
l ：较长、较详细的将该PID 的的信息列出；
j ：工作的格式 (jobs format)
-f ：做一个更为完整的输出。
# 例子查询nginx的路径及信息
ps -ef|grep nginx

# 查看系统磁盘数据
df -h

# 查看文件夹占用数据磁盘占比
du -sh <文件夹名字>

# 很详细的，数据查看数据磁盘占比
du -h <文件夹名字>

# linux 打印的日志，是可以生成到文件上的。可以通过 >> 这个符号生成文件，单个 > 是覆盖文件。
# 比如运行一个命令有日志生成
某个命令 >> 日志文件.txt
某个命令 > 日志文件.txt

```
## 查看linux基本信息

```java
//查看信息
top

//然后按 m 可以直观的看到运行的图
```
**运行结果：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210531155133904.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

**信息描述**

| 字段名              | 描述                                                                                                              |
| ---------------- | --------------------------------------------------------------------------------------------------------------- |
| **top**          | 系统当前时间                                                                                                          |
| **up**           | 系统已开机多长时间                                                                                                       |
| **user**         | 当前用户数                                                                                                           |
| **load average** | CPU平均负载（1分钟、5分钟、15分钟）                                                                                           |
| **Tasks**        | 系统当前进程数：<br>• `total`：总进程数<br>• `running`：运行中进程数<br>• `sleeping`：睡眠进程数<br>• `stopped`：停止进程数<br>• `zombie`：僵尸进程数 |
| **%Cpu(s)**      | CPU使用率：<br>• `us`：用户空间占用百分比<br>• `sy`：内核空间占用百分比<br>• `id`：空闲百分比                                                 |
| **Mem**          | 内存使用信息：<br>• `total`：总内存大小<br>• `free`：空闲内存<br>• `used`：已用内存<br>• `buff/cache`：缓存/缓冲区大小                         |
| **Swap**         | 虚拟内存交换分区信息                                                                                                      |
| **PID**          | 进程ID                                                                                                            |
| **USER**         | 进程所有者                                                                                                           |
| **PR**           | 进程优先级                                                                                                           |
| **NI**           | Nice值（负值=高优先级，正值=低优先级）                                                                                          |
| **VIRT**         | 进程使用的虚拟内存总量                                                                                                     |
| **RES**          | 进程使用的物理内存大小                                                                                                     |
| **SHR**          | 进程使用的共享内存大小                                                                                                     |
| **S**            | 进程状态：<br>• `D`：不可中断睡眠<br>• `R`：运行中<br>• `S`：睡眠<br>• `T`：停止/跟踪<br>• `Z`：僵尸进程                                     |
| **%CPU**         | 进程占用的CPU百分比                                                                                                     |
| **%MEM**         | 进程占用的物理内存百分比                                                                                                    |
| **TIME+**        | 进程累计使用的CPU时间（精确到百分之一秒）                                                                                          |
| **COMMAND**      | 进程对应的命令名/启动命令                                                                                                   |

**如何让top更改内存单位显示**

> 进入top页面后
> 按shift+e 就可以更改内存单位显示
> 按 c 会显示 **COMMAND**  的详细数据

**效果图如下：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210531155512317.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

## 查看运行端口

```linux

netstat -nlp|grep 8089

# 运行jar包
nohup java -jar xxx.jar &

#temp.out的意思是将日志输出重定向到temp.out文件，使用ctrl+c中断或者关闭窗口都不会中断程序的执行
nohup java -jar XXX.jar >temp.out &

# 查看日志（循实时查看尾部200行的日志）
tail -fn 200 nohup.out

# 日志10000行中持续的数据
tail -10000f nohup.out

# 查看日志（从头部查看200行日志，不怎么使用，日志输出短可以使用）
head -n 200 nohup.out

# 查看jar是否已经启动
ps -ef | grep -i

#强制关闭
kill 9 进程号

```

