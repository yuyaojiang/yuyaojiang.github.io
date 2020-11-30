# redis多线程IO分析

+ 用一组单独的线程专门进行 read/write socket读写调用 （同步IO）
+ 读回调函数中不再读数据，而是将对应的连接追加到可读clients_pending_read的链表
+ 主线程在beforeSleep中将IO读任务分给IO线程组
+ 主线程自己也处理一个IO读任务，并自旋式等IO线程组处理完，再继续往下
+ 主线程在beforeSleep中将IO写任务分给IO线程组
+ 主线程自己也处理一个IO写任务，并自旋式等IO线程组处理完，再继续往下
+ IO线程组要么同时在读，要么同时在写
+ 命令的执行由主线程串行执行(保持单线程)
+ IO线程数量可配置

其具体流程如下图：

![](./picture/multi-io-thread.jpg)