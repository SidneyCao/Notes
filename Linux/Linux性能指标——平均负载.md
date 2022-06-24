# 一、平均负载（Load average）
## 1. 概念    
uptime命令返回的结果中，后三个数字表示系统1分钟，5分钟，15分钟内的平均负载。  
```
#uptime
15:07:36 up 8 days, 25 min,  1 user,  load average: 0.00, 0.01, 0.05
```
平均负载是指系统在一个特定时间内，处于`可运行状态`和`不可中断状态`的平均进程数（实际上使用的是统计学中的某种算法）

- 可运行状态的进程，是指正在使用或正在等待CPU的进程，也就是top命令中处于R状态（Running）的进程
- 不可中断状态的进程，是指正处于内核关键状态的进程，通常是不可被打断的，比如正在等待硬件的I/O的进程。也就是top命令中处于D状态（Disk Sleep）的进程。  

需要注意一下平均负载和CPU使用率的区别：  
- CPU密集型的进程使用了大量CPU时，平均负载升高，CPU使用率升高。  
- 大量I/O密集型进程在等待I/O时，平均负载会升高，但CPU使用率不一定会升高。  
- CPU在大量进程之间进行调度/切换上下文，也会导致平均负载的升高。  

## 2. 合理范围
平均负载在什么范围内相对合理？  
  
首先确认下系统的cpu数量，这里使用的是一台2C的机器。 
```
#cat /proc/cpuinfo | grep processor | wc -l
2
```
此时如果系统的平均负载为1，则表示有CPU有50%的空闲；  
平均负载为2，则表示CPU已经全部被占用；  
平均负载大于2，则会有部分进程竞争不到CPU。  
  
一般来说，当平均负载的值开始接近CPU总数时，就需要加以关注了。  
但还是要根据应用的类型和以往监控数据来灵活地进行排查和调整。  

# 二、 模拟和测试
## 1. 工具  
CentOS上安装所需的工具，stress是Linux平台上的一款压力测试软件，用来模拟各项指标升高的场景；sysstat包含一系列监控和分析系统性能的工具。  
```
#yum -y install stress sysstat
```
## 2. CPU密集型进程  
使用stress模拟单个CPU使用率100%的场景  
```
#stress -c 1 --timeout 600
stress: info: [12007] dispatching hogs: 1 cpu, 0 io, 0 vm, 0 hdd
```
查看uptime，会发现平均负载逐渐上升到1  
```
#watch -d uptime  
```
![uptime1](https://github.com/SidneyCao/Notes/blob/main/img/uptime1.png)






