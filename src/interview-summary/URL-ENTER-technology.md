# 在浏览器地址栏输入一个URL后回车，背后会进行哪些技术步骤

知乎上有一个回答个人觉得很好，记录以下，原文链接是[](https://www.zhihu.com/question/34873227)

男主角小明出场，采用拟人的手法来阐述整个过程

小明打开浏览器输入www.google.com并回车

## 1、浏览器对用户输入的网址做初步的格式化检查，通过以上检查则进入下一步

浏览器采用的是http还是https访问服务器呢？

在没有明确告知的情况下，浏览器默认采用的是http协议，除非输入的是https://www.google.com

浏览器知道，TCP/IP快递公司(TCP/IP协议栈)，需要知道收件人的IP地址，才会把快递送到收件人的地址，然而现在浏览器之知道URL地址；

## 2、浏览器联系DNS黄页公司(DNS域名服务)，让它帮忙查询 www.google.com 的ip地址是多少？

    DNS是个老实的孩子，自己能查询到的，绝不麻烦别人

    DNS查询过程：

    2.1、先查浏览器的DNS缓存，没有

    2.2、在查本地主机的host文件，也没有

    2.3、实在没辙只有求人了，于是DNS硬着头皮去联系自己的DNS服务器 8.8.8.8(主机事先知道本地DNS服务器的IP地址 /etc/resolv.conf文件)

    DNS将自己的查询打好包，收件人地址为8.8.8.8，寄件人地址为1.1.1.1(本机ip)，DNS联系TCP/IP快递公司。

    负责接洽的是UDP(DNS报文通过UDP发送，UDP报文不可靠)，UDP懒洋洋的躺在沙发上，随手在包裹上刷刷写了几笔(添加UDP报文头)：

    收件人门牌号 53(DNS服务器默认端口53)
    发件人门牌号 56002(发送端口，操作系统随机分配)

    之所以要有门牌号，是因为一个收件人地址可能会有多个门牌号，为了避免混淆。对于整天浸淫在快递行业的UDP，太了解这个行业了。

    UDP给货车司机IP打电话：老四啊，有件快递需要你捎带一下。。。(将UDP报文发送到IP层)

    IP司机来了，把包裹扔上车，坐上驾驶座，准备开车。

    IP司机查询了导航(IP路由表)，发现要出关(Gateway，DNS服务器不在本地局域网内，需要通过网关将报文发送)，这个关口有点怪癖，需要司机知道其MAC地址，导航信息里竟然没有(本机ARP缓存没有网关IP对应的MAC地址)。

    于是IP司机找到了当地向导ARP，老师傅，麻烦您带带路啊！

    ARP没有废话，声音洪亮地喊了一嗓子，网关你MAC地址多少啊，告诉老夫一声！(ARP协议在局域网内发送广播ARP报文，查找网关IP对应的MAC地址)

    很快传来了网关的回答：我的MAC地址是xx.xx.xx.xx.xx.xx(网关接收到主机发送的ARP报文，发现查询的是自己的MAC地址，发送ARP回应报文，告知自己的MAC地址)

    有了关口的MAC地址，IP司机终于可以开车上路了。(该数据包的协议头部源MAC为主机MAC，目的MAC为网关MAC，源IP为主机IP，目的IP为DNS服务器IP，源端口为56002，目的端口为53)

    很快就到达了关口，关口放行，IP司机载着快递，上了Internet高速公路，一路狂奔不表。。。(网关为该报文查找路由表，找到下一跳，将报文发往下一跳，中间经过无数跳的路由器)

    到达目的地8.8.8.8，服务器根据门牌号码53，知道这是DNS Server大叔家的快递。(最后一条路由器，查找路由表，精确命中目的IP，将报文发往目的IP主机，目的IP主机也就是DNS服务器经过内核协议栈，层层去掉头部，根据端口号53，发往DNS应用程序)

    喊大叔来收快递，大叔打开包裹一看，这个好回答啊，http：//www.google.com 对应的IP地址正好在缓存里还热着呢，于是将其回复回去。

    这个DNS大叔有一个特点，打破沙锅问到底的学习精神，俗称的一根筋，如果DNS大叔的本地缓存里查询没有，怎么办呢？

    DNS大叔会去联系DNS域名系统的根服务器“.”(根服务器全球共有13台，使用多台根服务器，可以提供物理冗余，分摊全球的域名查询任务，DNS服务器存有根服务器的IP地址)

    DNS大叔就会去联系13台根服务器的一员，查询自己想要的结果。

    根服务器一看“www.google.com.” ，知道是自己的孙子，却不知道其IP地址，但根服务器相信孙子的爸爸“com”会知道，于是告诉DNS大叔，请去联系我孙子的爸爸，他的IP地址是x.x.x.x。

    DNS大叔锲而不舍地去联系孙子的爸爸，毫无疑问，爸爸肯定知道儿子的IP地址，儿子的名字都是自己起的，能不知道吗？

    将结果告诉了DNS大叔，大叔如获至宝，立马将结果告诉了远在千里之外等待的DNS老实孩子，

    累死了，鼓捣了半天才算拿到服务器的IP地址，DNS把结果返回给浏览器。

## 3、浏览器再次联系TCP/IP快递公司，这次与其接洽的是TCP阿姨，TCP做事非常认真(http采用TCP，可靠连接)

    知道浏览器想要去拜访“118.89.204.100”，先和对方取得联系，看看对方在不在，这通常由三次握手实现的。(TCP建联三次握手)

    老阿姨：在家吗？想去拜访您。(SYN报文)

    对方：在的，欢迎啊。(SYN-ACK报文)

    老阿姨：马上到。(ACK报文)

    这一来二回的三次消息，也都需要IP司机来来回回运输三次，具体过程和上文IP司机运输DNS报文非常类似，就不再重复。

    三次握手完成，TCP阿姨与对方建立了一个可靠的虚拟通道，浏览器很快知道了这个消息。

    浏览器将http请求消息，打包好扔给TCP阿姨，阿姨在包裹上填上关键信息：

    收件人门牌号 80(http端口号为80)

    发件人门牌号 51235(发送端端口号随机分配)

    然后也是联系IP司机来运输，过程不表。

    包裹到达了目的地，服务器根据门牌号80，联系到了http server小姐姐。

    小姐姐返回了一个消息：HTTP Redirect 消息，大意是本公司服务器整体搬迁到https://www.google.com 上去了，请重新访问本司的新网址。

    浏览器收到这个消息，立即前往https://www.google.com 上述流程重新来一遍；

    TCP三次握手成功之后，浏览器将自己的打包好的包裹，不是直接给TCP阿姨，而是委托TLS安保大叔全权负责。(https服务要通过TLS加密)

    TLS加密过程沟通过程待续

    TLS沟通过程完成后，TLS大叔把浏览器扔给自己的包裹，外面加了一层保险箱，密码锁（session key）只有TLS大叔、TLS服务器知道。

    然后把保险箱再扔给TCP阿姨，TCP阿姨一点也不在乎，运输一个保险箱与一个普通包裹没有任何区别，唯一的区别是收件人的门牌号变了：

    收件人门牌号 443(https服务的默认端口为443)

    然后保险箱就被运走了，很快就到达了目的地，服务器老大爷一看门牌号443，知道这是TLS服务器的快递包裹。

    TLS服务器用密码打开了保险箱，取出了快递。

    在保险箱里还有一个小纸条写着“Application Data =http”, TLS服务器知道这是HTTP Server高富帅的包裹。

    然后把包裹转交给高富帅，高富帅将https://www.google.com 主页返回，并最终到达浏览器。

