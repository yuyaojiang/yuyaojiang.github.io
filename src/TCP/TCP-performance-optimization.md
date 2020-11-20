# TCP性能优化

linux 内核提供了方法来调节TCP的参数，如果想**永久的保存**参数的设置，可以将**参数加入到/etc/sysctl.conf**中。
如果想**临时的更改**参数的配置，可以**修改/proc/sys/net/ipv4/**下的参数，机器重启后更改失效。

我们先来整理一下/proc/sys/net/ipv4/目录下和TCP相关的参数：

| 名称 | 默认值（基于ubuntu18.04版本    ） | 描述 |
| :--- | :--- | :--- |
| tcp_syn_retries | 6 | 对于一个新建连接，内核要发送多少个 SYN 连接请求才决定放弃。不应该大于255，默认值6，第一次超时重传是在 1 秒后，第二次时重传是在 2 秒，第三次超时重传是在 4 秒后，第四次超时重传是在 8 秒后，第五次是在超时重传 16 秒后。每次超时的时间是上一次的 2 倍，对应于63秒左右时间 |
| tcp_synack_retries | 5 | 对于远端的连接请求SYN，内核会发送SYN ＋ ACK数据报，以确认收到上一个 SYN连接请求包。这是所谓的三次握手( threeway handshake)机制的第二个步骤。这里决定内核在放弃连接之前所送出的 SYN+ACK 数目。不应该大于255，默认值是5，对应于31秒左右时间。|
| tcp_keepalive_time | 7200 | TCP发送keepalive探测消息的间隔时间（秒），用于确认TCP连接是否有效，多长时间没有报文开始发送心跳包。|
| tcp_keepalive_probes | 9 | TCP发送keepalive探测消息的间隔时间（秒）|
| tcp_keepalive_intvl | 75 | 探测消息未获得响应时，重发该消息的间隔时间（秒）|
| tcp_retries1 | 3 | 放弃回应一个TCP连接请求前﹐需要进行多少次重试。RFC 规定最低的数值是3 |
| tcp_retries2 | 15 | 在丢弃激活(已建立通讯状况)的TCP连接之前﹐需要进行多少次重试。默认值为15，根据RTO的值来决定，相当于13-30分钟 |
| tcp_orphan_retries | 0 | TCP连接关闭时，主动方发送 FIN 报文后，连接就处于 FIN_WAIT1 状态，正常情况下，如果能及时收到被动方的 ACK，则会很快变为 FIN_WAIT2 状态。但是当迟迟收不到对方返回的 ACK 时，连接就会一直处于 FIN_WAIT1 状态。此时，内核会定时重发 FIN 报文，其中重发次数由tcp_orphan_retries 参数控制，默认值是 0，实际上当为 0 时，特指 8 次 |
| tcp_fin_timeout | 60 | TCP连接关闭时，当主动方收到 ACK 报文后，会处于 FIN_WAIT2 状态，就表示主动方的发送通道已经关闭，接下来将等待对方发送 FIN 报文，关闭对方的发送通道，如果连接是用 shutdown 函数关闭的，连接可以一直处于 FIN_WAIT2 状态，因为它可能还可以发送或接收数据。但对于 close 函数关闭的孤儿连接，由于无法再发送和接收数据，所以这个状态不可以持续太久，而 tcp_fin_timeout 控制了这个状态下连接的持续时长，默认值是 60 秒 | 
| tcp_max_tw_buckets | 8192 | TCP连接关闭时，当主动方接受对方的FIN报文后，发送ACK给对方，主动方便处于TIME_WAIT状态，TIME_WAIT 和 FIN_WAIT2状态的最大时长都是 2 MSL，由于在 Linux 系统中，MSL 的值固定为 30 秒，所以它们都是 60 秒，如果发起连接一方的 TIME_WAIT 状态过多，占满了所有端口资源，则会导致无法创建新连接，Linux 提供了 tcp_max_tw_buckets 参数，当 TIME_WAIT 的连接数量超过该参数时，新关闭的连接就不再经历 TIME_WAIT 而直接关闭 |
| tcp_tw_recycle | 
##