
# virtual-box安装ubuntu18.04 问题汇总

## 屏幕小的问题
环境版本：

virtual-box 6.0.24

ubuntu 18.04

解决方案：

1、安装增强功能 设备->安装增强功能

2、apt install virtulbox-guest-additions-iso

3、cd /media/yuyaojiang/VBox_GAs_6.0.24 && ./VBoxLinuxAdditions.run

4、reboot


## ubuntu屏幕字体小
解决办法：
在show application里面搜索display，将scale调整为200%

![](picture/virtual-box-ubuntu/ubuntu2.1.png)

![](picture/virtual-box-ubuntu/ubuntu2.2.png)

## 共享文件夹无权限访问
解决办法：将用户加入到vboxsf组里
```
sudo usermod -a -G vboxsf yuyaojiang
reboot
```
## 普通用户无法使用docker命令
解决办法：将用户加入到docker组里
```
sudo usermod -a -G docker yuyaojiang
reboot
```

## apt install xxx

当apt install xxx出现下图错误时：

![](picture/virtual-box-ubuntu/apt-install-error.png)

E: Could not get lock /var/lib/dpkg/lock – open (11: Resource temporarily unavailable)

E: Unable to lock the administration directory (/var/lib/dpkg/), is another process using it?

解决办法：

1、查看使用apt命令的进程：
```
ps aux | grep -i apt
```
产生如下输出：

![](picture/virtual-box-ubuntu/apt-install-ps.png)

表示当前有进程在使用apt程序，可以等一会在使用该命令，原因时ubuntu18.04版本会每天定时更新和安全有关的deb包

## ubuntu开启telnet服务

1、查看/usr/sbin/目录下是否有tcpd程序(一般刚安装的ubuntu没有)，没有则安装tcpd

```
apt install tcpd
```

2、安装telnetd和xinetd

```
apt install telnetd xinetd
```

3、修改配置文件

+ ```vi /etc/inetd.conf``` 该文件为新增 添加以下内容

telnet stream tcp nowait telnetd /usr/sbin/tcpd /usr/sbin/in.telnetd

+ ```vi /etc/xinetd.conf``` 该文件已有，修改内容，添加以下内容

```
instances = 60
log_type = SYSLOG authpriv
log_on_success = HOST PID
log_on_failure = HOST
cps = 25 30
```
修改后的文件为：

![](./picture/virtual-box-ubuntu/telnet-xinetd.conf.png)

+ ```vi /etc/xinetd.d/telnet``` 该文件为新增 添加以下内容

```
 # default: on

 # description: The telnet server serves telnet sessions; it uses

 # unencrypted username/password pairs for authentication.

 service telnet

 {

     disable = no

     flags = REUSE

 　　socket_type = stream

 　　wait = no

 　　user = root

 　　server = /usr/sbin/in.telnetd

 　　log_on_failure += USERID

 }
```

4、重启xinetd服务

```
/etc/init.d/xinetd restart
```

通过远程连接工具即可登录

## ubuntu虚拟机开启ngnix服务

1、安装nginx

```
sudo apt install nginx
```
2、调整防火墙

允许nignx通过防火墙：

```
sudo ufw allow 'Nginx HTTP'
```

查看结果：

```
sudo ufw app list
```
![](./picture/virtual-box-ubuntu/nginx-ufw-app-list.png)

3、查看nginx是否开启：

```
systemctl status nginx
```
![](./picture/virtual-box-ubuntu/nginx-systemctl-status-nginx.png)

4、浏览器输入```http://ip```,结果为：

![](./picture/virtual-box-ubuntu/nginx-httpip-result.png)

5、nignx 配置多个站点

```
mkdir -p /var/www/yuyaojiang.com
cp index.html /var/www/yuyaojiang/
chmod +r /var/www/yuyaojiang/index.html

```

接着
```
vim /etc/nginx/site-available/default

```
做如下修改：

![](./picture/virtual-box-ubuntu/nginx-multi-point.png)





## ubuntu虚拟机ping不通window10主机

原因：window10主机的防火墙规则禁止了ICMP报文

解决办法：开启window10的ICMP报文的防火墙规则

操作步骤：控制面板->系统和安全->Windows Defender 防火墙->高级设置->入站规则->文件和打印机共享(回显请求-ICMPv4-In) 专用公用->开启

## 查看errno

安装moreutils包

```
sudo apt install moreutils
```

查看errno命令``` errno -l ```

下面是ubuntu18.04的errno：
```
EPERM 1 Operation not permitted
ENOENT 2 No such file or directory
ESRCH 3 No such process
EINTR 4 Interrupted system call
EIO 5 Input/output error
ENXIO 6 No such device or address
E2BIG 7 Argument list too long
ENOEXEC 8 Exec format error
EBADF 9 Bad file descriptor
ECHILD 10 No child processes
EAGAIN 11 Resource temporarily unavailable
ENOMEM 12 Cannot allocate memory
EACCES 13 Permission denied
EFAULT 14 Bad address
ENOTBLK 15 Block device required
EBUSY 16 Device or resource busy
EEXIST 17 File exists
EXDEV 18 Invalid cross-device link
ENODEV 19 No such device
ENOTDIR 20 Not a directory
EISDIR 21 Is a directory
EINVAL 22 Invalid argument
ENFILE 23 Too many open files in system
EMFILE 24 Too many open files
ENOTTY 25 Inappropriate ioctl for device
ETXTBSY 26 Text file busy
EFBIG 27 File too large
ENOSPC 28 No space left on device
ESPIPE 29 Illegal seek
EROFS 30 Read-only file system
EMLINK 31 Too many links
EPIPE 32 Broken pipe
EDOM 33 Numerical argument out of domain
ERANGE 34 Numerical result out of range
EDEADLK 35 Resource deadlock avoided
ENAMETOOLONG 36 File name too long
ENOLCK 37 No locks available
ENOSYS 38 Function not implemented
ENOTEMPTY 39 Directory not empty
ELOOP 40 Too many levels of symbolic links
EWOULDBLOCK 11 Resource temporarily unavailable
ENOMSG 42 No message of desired type
EIDRM 43 Identifier removed
ECHRNG 44 Channel number out of range
EL2NSYNC 45 Level 2 not synchronized
EL3HLT 46 Level 3 halted
EL3RST 47 Level 3 reset
ELNRNG 48 Link number out of range
EUNATCH 49 Protocol driver not attached
ENOCSI 50 No CSI structure available
EL2HLT 51 Level 2 halted
EBADE 52 Invalid exchange
EBADR 53 Invalid request descriptor
EXFULL 54 Exchange full
ENOANO 55 No anode
EBADRQC 56 Invalid request code
EBADSLT 57 Invalid slot
EDEADLOCK 35 Resource deadlock avoided
EBFONT 59 Bad font file format
ENOSTR 60 Device not a stream
ENODATA 61 No data available
ETIME 62 Timer expired
ENOSR 63 Out of streams resources
ENONET 64 Machine is not on the network
ENOPKG 65 Package not installed
EREMOTE 66 Object is remote
ENOLINK 67 Link has been severed
EADV 68 Advertise error
ESRMNT 69 Srmount error
ECOMM 70 Communication error on send
EPROTO 71 Protocol error
EMULTIHOP 72 Multihop attempted
EDOTDOT 73 RFS specific error
EBADMSG 74 Bad message
EOVERFLOW 75 Value too large for defined data type
ENOTUNIQ 76 Name not unique on network
EBADFD 77 File descriptor in bad state
EREMCHG 78 Remote address changed
ELIBACC 79 Can not access a needed shared library
ELIBBAD 80 Accessing a corrupted shared library
ELIBSCN 81 .lib section in a.out corrupted
ELIBMAX 82 Attempting to link in too many shared libraries
ELIBEXEC 83 Cannot exec a shared library directly
EILSEQ 84 Invalid or incomplete multibyte or wide character
ERESTART 85 Interrupted system call should be restarted
ESTRPIPE 86 Streams pipe error
EUSERS 87 Too many users
ENOTSOCK 88 Socket operation on non-socket
EDESTADDRREQ 89 Destination address required
EMSGSIZE 90 Message too long
EPROTOTYPE 91 Protocol wrong type for socket
ENOPROTOOPT 92 Protocol not available
EPROTONOSUPPORT 93 Protocol not supported
ESOCKTNOSUPPORT 94 Socket type not supported
EOPNOTSUPP 95 Operation not supported
EPFNOSUPPORT 96 Protocol family not supported
EAFNOSUPPORT 97 Address family not supported by protocol
EADDRINUSE 98 Address already in use
EADDRNOTAVAIL 99 Cannot assign requested address
ENETDOWN 100 Network is down
ENETUNREACH 101 Network is unreachable
ENETRESET 102 Network dropped connection on reset
ECONNABORTED 103 Software caused connection abort
ECONNRESET 104 Connection reset by peer
ENOBUFS 105 No buffer space available
EISCONN 106 Transport endpoint is already connected
ENOTCONN 107 Transport endpoint is not connected
ESHUTDOWN 108 Cannot send after transport endpoint shutdown
ETOOMANYREFS 109 Too many references: cannot splice
ETIMEDOUT 110 Connection timed out
ECONNREFUSED 111 Connection refused
EHOSTDOWN 112 Host is down
EHOSTUNREACH 113 No route to host
EALREADY 114 Operation already in progress
EINPROGRESS 115 Operation now in progress
ESTALE 116 Stale file handle
EUCLEAN 117 Structure needs cleaning
ENOTNAM 118 Not a XENIX named type file
ENAVAIL 119 No XENIX semaphores available
EISNAM 120 Is a named type file
EREMOTEIO 121 Remote I/O error
EDQUOT 122 Disk quota exceeded
ENOMEDIUM 123 No medium found
EMEDIUMTYPE 124 Wrong medium type
ECANCELED 125 Operation canceled
ENOKEY 126 Required key not available
EKEYEXPIRED 127 Key has expired
EKEYREVOKED 128 Key has been revoked
EKEYREJECTED 129 Key was rejected by service
EOWNERDEAD 130 Owner died
ENOTRECOVERABLE 131 State not recoverable
ERFKILL 132 Operation not possible due to RF-kill
EHWPOISON 133 Memory page has hardware error
ENOTSUP 95 Operation not supported
```
