# unix信号（signal）
## 信号概念
信号是**软中断**，信号提供了一种异步事件的处理方式

## 信号的处理
信号的处理有3种方式：**忽略、捕捉、执行系统默认动作**

**忽略**：两种信号不能忽略，**SIGKILL**和**SIGSTOP**，原因是这两个信号向内核或超级用户提供了使进程停止或终止的一种可靠的方法

**捕捉**：通知内核在某种信号发生时，调用一个用户设置的函数（信号处理函数）

**执行系统默认动作**：系统对每一种信号都提供了一种默认处理动作，大多数信号的默认动作是**终止进程**

## 信号分析

### 常用信号

| 信号名 | 产生条件 | 默认动作 | 对应信号数 | 其他注意事项 |
| --- | --- | --- | --- | --- |
| SIGABRT | 调用**abort**()函数产生此信号 | 进程异常终止 | 6 | 无 |
| SIGALAM | 用**alarm**函数设置的定时器超时 | 终止进程 | 14 | 对于这一类信号，一般采用**捕捉**处理|
| SIGINT | 当用户按中断键（Ctrl + C), 产生此信号发送到前台进程组的每一个进程 | 终止进程 | 2 |
| SIGKILL | kill()函数默认发送的信号 | 终止进程 | 9 | 该信号不可捕捉和忽略，kill()函数可以发送任意的信号，形式为 kill(pid, signum) |
| SIGSTOP | kill()函数发送的信号 | 终止进程 | 17，19，23 | 该信号不可捕捉和忽略 | 
| SIGPIPE | 在管道的读进程已终止时写管道，SOCK_STREAM的套接字(TCP连接)对端shutdown(SHUT_RD)关闭读时，本端写套接字，产生此信号 | 终止进程 | 13 |
| SIGSEGV | 指示进程进行无效的内存访问 | 终止进程 | core + 进程异常终止 | 11 |
| SIGTERM | kill命令默认发出的信号 | 终止进程 | 15 | 
| SIGUSR1 SIGUSR2 | 用户定义的信号 | 终止进程 | 30, 31 | 用于进程间的通信控制 |
| SIGSYS | 一个无效的系统调用 | 终止进程 | 12 |
| SIGQUIT | 当用户按退出键（一般采用Ctrl + \），产生此信号 | 终止进程 + core | 3 |
| SIGIO | 指示一个异步IO事件 | 终止或忽略 | 23 | 
| SIGURG | 接收到TCP的带外数据时，产生此信号 | 忽略 | 16 |
| SIGHUP | 连接断开 | 终止进程 | 1 |


### 硬件错误产生的信号

| 信号名 | 产生条件 | 默认动作 | 对应信号数 | 其他注意事项 |
| :---- | :--------| :-------| :---------- | :-------- |
| SIGBUS | 总线错误，内存故障产生此信号 | 进程异常终止 | 10 | 无 |
| SIGEMT | 硬件错误产生此信号 | 进程异常终止 |  | 无 |
| SIGILL | 非法硬件指令 | 
| SIGIOT | 硬件故障 |

## signal()函数
```
SigFunc * signal(int signum, SigFunc *func);
正确返回上一个信号处理动作，错误返回SIG_ERR
```
SIG_IGN表示忽略信号，SIG_DFL表示采用系统默认动作

signal()函数只对信号进行一次捕捉，一般不采用signal()函数进行信号处理，而采用sigaction函数





