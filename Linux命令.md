



[toc]



## 面试遇到的

##### 怎么看本机的外网 ip

curl ipconfig.me





## 系统资源

#### top

* VIRT 进程可访问的虚拟内存总量
  * 包括进程使用的库、代码、数据
  * mmap 映射
  * 通常比实际使用的物理内存要大，因为包括了内存和文件系统缓存
* RES 进程真正占用的内存
  * 只有用到的库文件会包含在 RES 里。
* SHR 表示 VIRT 中有多少是共享部分
  * 包括库文件使用的内存
  * 与其他进程共享的内存





### vmstat

* procs 进程信息
  * r 运行和等待 cpu 时间片的进程数
  * b block 的进程数
* memory
  * swpd 使用的虚拟内存大小
  * free 可用的物理内存大小
  * buff 物理内存用来缓存读写操作的buffer大小
  * cache 物理内存用来缓存进程地址空间的 cache 大小
* swap
  * si 每秒从 swap 分区读入到 ram 的大小
  * so 每秒从 ram 写到 swap 的大小
* io
  * bi
  * bo
* ststem
  * in 每秒的中断数
  * cs 每秒进行的上下文切换数
* cpu
  * us 用户进程使用 CPU 时间的百分比
  * sy 内核进程占用 CPU 的百分比
  * id idle cpu 空闲的百分比
  * wa wait cpu等待 IO 的百分比

## 内存

#### free

![Screenshot 2024-03-25 at 18.35.33](Linux命令.assets/Screenshot 2024-03-25 at 18.35.33.png)

total : Memtotal + SwapTotal 

used physical memory : total - free - buffers - cache

shared 

| 字段       | 描述                                  | 关系                           |
| ---------- | ------------------------------------- | ------------------------------ |
| total      | memtotal and swap total               |                                |
| used       | 占用的内存                            | total - free - cache - buffers |
| free       | 没使用的内存                          | Memfree + swapfree             |
| shared     | Memory used (mostly) by **tmpfs***    |                                |
| buffers    | used by kernel buffer                 |                                |
| cache      | page cache and slabs                  |                                |
| available  | 估计有多少内存可以给新应用 不包括swap | 大概的 free + cache + buff     |
| buff/cache |                                       |                                |

tmpfs 是内存种的虚拟文件系统 类似于 proc，具有临时性、快速读写、动态收缩的特点，适合文件缓存和临时数据

swap memory

buffers/caches



查看系统资源

free

df

du

VMstat

![image-20240413170438126](C:\Users\19183\AppData\Roaming\Typora\typora-user-images\image-20240413170438126.png)





## 网络

tracert

#### netstat -nlp

#### Ss -ltnp

当 socket 状态处于 `Established`时：

- *Recv-Q* 表示 socket 缓冲区中还没有被应用程序读取的字节数；
- *Send-Q* 表示 socket 缓冲区中还没有被远端主机确认的字节数；

而当 socket 状态处于 `Listen` 时：

- *Recv-Q* 表示**全连接队列**的长度；
- *Send-Q* 表示全连接队列的**最大长度**；



#### sar 查看网络吞吐

- sar -n DEV，显示**网口**的统计数据；
- sar -n EDEV，显示关于**网络错误**的统计数据；
- sar -n TCP，显示 **TCP** 的统计数据



ping 判断连通性





内存泄漏 ： Valgrind

死锁检查：pstack <pid>





### Kill

信号

进程对信号的响应：

1. 忽略
2. 终止
3. 终止并且 coredump
4. 暂停
5. 恢复

| 信号编号 | 名称        | 描述                                                  | 处理            | 退出 |
| -------- | ----------- | ----------------------------------------------------- | --------------- | ---- |
| 1        | SIGHUP      | 挂起                                                  | 终止            |      |
| 2        | SIGINT      | Ctrl C 终端中断                                       | 终止            |      |
| **3**    | **SIGQUIT** | Ctrl \ 产生 Coredump                                  | 终止、core dump |      |
| 4        | SIGILL      |                                                       |                 |      |
| 5        |             |                                                       |                 |      |
| 18       | SIGCONT     | 继续（与STOP相反，fg/bg命令）                         |                 |      |
| 15       | SIGTERM     | 终止                                                  |                 |      |
| 19       | SIGSTOP     | 暂停（同 Ctrl + Z）                                   |                 |      |
| 9        | SIGKILL     | 不能忽略和自定义处理，必杀（除非权限不足、D状态进程） | 终止            |      |

进程退出：移除资源（清除进程控制块，打开的文件、分配的内存）通知父进程，移除进程描述符。

SIGSEGV 空指针、野指针，堆越界、栈越界



## 进程与线程

### PS

#### $ps -aux 

 ```
 USER｜PID| %CPU |%MEM| VSZ ｜ RSS | TTY | STAT | START | TIME | COMMAND
 用户｜id｜ CPU| MEM ｜完全驻留内存|实际资源|终端｜状态｜启动时间|CPU时间|启动进程的命令
 ```

用户


STAT 进程当前的状态

*  S sleeping 等待
* D uninterruptible 不接收异步信号
* R runnable 运行
* T 停止。SIGSTOP 信号
* Z Zombie **TASK_DEAD – EXIT_ZOMBIE** 执行完毕没有收尸



aux 更详细一点，主要是有资源信息。



#### $ps -ef  

UID        PID  PPID  C STIME TTY          TIME CMD

区别是 ps 可以看





#### 查看线程

1. ps -T

```bash
# ps -T -p <pid>

  PID  SPID TTY          TIME CMD
 1234  1235 pts/1    00:00:00 java
 1234  1236 pts/1    00:00:00 java
 1234  1237 pts/1    00:00:00 java
 1234  1238 pts/1    00:00:00 java

// SPID 是线程 ID
```



2. top -H 

   1. 能看到实时性能
      1. 用 PID 代表进程号
      2. RES 非交换内存
      3. VIRT 总虚拟内存
      4. 共享内存
      5. 

   ```bash
     PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
    1235 user1     20   0 1625908  88900  21744 R 12.5  1.1   0:00.38 java
    1236 user1     20   0 1625908  88900  21744 S  6.2  1.1   0:00.19 java
    1237 user1     20   0 1625908  88900  21744 S  6.2  1.1   0:00.19 java
    1238 user1     20   0 1625908  88900  21744 S  6.2  1.1   0:00.19 java
   ```

   

#### 





## CPU

#### uptime 查看负载

// 1 min, 5 min, 15 min 负载

**用uptime得到的3个负载值除以逻辑CPU数，如果3个结果值均>1，则表示CPU过载。**





```
12:33:49 up  1:28,  1 user,  load average: 0.31, 0.25, 0.20
```

