---
title: Ubuntu下安装vsftpd并配置iptables
date: 2015-04-07 19:54:10
tags:
---

### 前言

因为要用[codeanywhere](https://codeanywhere.com/)写代码，所以要搭建一个ftp服务器来保存项目。之前的服务器都是关闭防火墙`iptables`的。只要安装好`vsftpd`，就可以用了。最近打算了解一下`iptables`强制要求自己没有关掉防火墙。正好`vsftpd`的防火墙配置有些特殊，所以记录在这里。

### 安装

#### 参考

[Vsftpd](http://wiki.ubuntu.org.cn/Vsftpd)
官方文档写的太清楚了。我就不多说啦。这里只把配置字段的解释再贴一次

```
listen=<YES/NO> :设置为YES时vsftpd以独立运行方式启动，设置为NO时以xinetd方式启动（xinetd是管理守护进程的，将服务集中管理，可以减少大量服务的资源消耗）
listen_port=<port> :设置控制连接的监听端口号，默认为21
listen_address=<ip address> :将在绑定到指定IP地址运行，适合多网卡
connect_from_port_20=<YES/NO> :若为YES，则强迫FTP－DATA的数据传送使用port 20，默认YES
pasv_enable=<YES/NO> :是否使用被动模式的数据连接，如果客户机在防火墙后，请开启为YES
pasv_min_port=<n>
pasv_max_port=<m> :设置被动模式后的数据连接端口范围在n和m之间,建议为50000－60000端口
message_file=<filename> :设置使用者进入某个目录时显示的文件内容，默认为 .message
dirmessage_enable=<YES/NO> :设置使用者进入某个目录时是否显示由message_file指定的文件内容
ftpd_banner=<message> :设置用户连接服务器后的显示信息，就是欢迎信息
banner_file=<filename> :设置用户连接服务器后的显示信息存放在指定的filename文件中
connect_timeout=<n> :如果客户机连接服务器超过N秒，则强制断线，默认60
accept_timeout=<n> :当使用者以被动模式进行数据传输时，服务器发出passive port指令等待客户机超过N秒，则强制断线，默认60
accept_connection_timeout=<n> :设置空闲的数据连接在N秒后中断，默认120
data_connection_timeout=<n> : 设置空闲的用户会话在N秒后中断，默认300
max_clients=<n> : 在独立启动时限制服务器的连接数，0表示无限制
max_per_ip=<n> :在独立启动时限制客户机每IP的连接数，0表示无限制（不知道是否跟多线程下载有没干系）
local_enable=<YES/NO> :设置是否支持本地用户帐号访问
guest_enable=<YES/NO> :设置是否支持虚拟用户帐号访问
write_enable=<YES/NO> :是否开放本地用户的写权限
local_umask=<nnn> :设置本地用户上传的文件的生成掩码，默认为077
local_max_rate<n> :设置本地用户最大的传输速率，单位为bytes/sec，值为0表示不限制
local_root=<file> :设置本地用户登陆后的目录，默认为本地用户的主目录
chroot_local_user=<YES/NO> :当为YES时，所有本地用户可以执行chroot
chroot_list_enable=<YES/NO> 
chroot_list_file=<filename> :当chroot_local_user=NO 且 chroot_list_enable=YES时，只有filename文件指定的用户可以执行chroot
anonymous_enable=<YES/NO> :设置是否支持匿名用户访问
anon_max_rate=<n> :设置匿名用户的最大传输速率，单位为B/s，值为0表示不限制
anon_world_readable_only=<YES/NO> 是否开放匿名用户的浏览权限
anon_upload_enable=<YES/NO> 设置是否允许匿名用户上传
anon_mkdir_write_enable=<YES/NO> :设置是否允许匿名用户创建目录
anon_other_write_enable=<YES/NO> :设置是否允许匿名用户其他的写权限（注意，这个在安全上比较重要，一般不建议开，不过关闭会不支持续传）
anon_umask=<nnn> :设置匿名用户上传的文件的生成掩码，默认为077
```
_____
### FTP两种模式的区别：

>PORT（主动）模式

所谓主动模式，指的是FTP服务器“主动”去连接客户端的数据端口来传输数据，其过程具体来说就是：客户端从一个任意的非特权端口N（N>1024）连接到FTP服务器的命令端口（即tcp 21端口），紧接着客户端开始监听端口N+1，并发送FTP命令“port N+1”到FTP服务器。然后服务器会从它自己的数据端口（20）“主动”连接到客户端指定的数据端口（N+1），这样客户端就可以和ftp服务器建立数据传输通道了。

>PASV（被动）模式

所谓被动模式，指的是FTP服务器“被动”等待客户端来连接自己的数据端口，其过程具体是：当开启一个FTP连接时，客户端打开两个任意的非特权本地端口（N >1024和N+1）。第一个端口连接服务器的21端口，但与主动方式的FTP不同，客户端不会提交PORT命令并允许服务器来回连它的数据端口，而是提交PASV命令。这样做的结果是服务器会开启一个任意的非特权端口（P > 1024），并发送PORT P命令给客户端。然后客户端发起从本地端口N+1到服务器的端口P的连接用来传送数据。（注意此模式下的FTP服务器不需要开启tcp 20端口了）

#### 两种模式的比较：

PORT（主动）模式模式只要开启服务器的21和20端口，而PASV（被动）模式需要开启服务器大于1024所有tcp端口和21端口。

从网络安全的角度来看的话似乎ftp PORT模式更安全，而ftp PASV更不安全，那么为什么RFC要在ftp PORT基础再制定一个ftp PASV模式呢？其实RFC制定ftp PASV模式的主要目的是为了数据传输安全角度出发的，因为ftp port使用固定20端口进行传输数据，那么作为黑客很容使用sniffer等探嗅器抓取ftp数据，这样一来通过ftp PORT模式传输数据很容易被黑客窃取，因此使用PASV方式来架设ftp server是最安全绝佳方案。

因此：如果只是简单的为了文件共享，完全可以禁用PASV模式，解除开放大量端口的威胁，同时也为防火墙的设置带来便利。

**不幸的是**，FTP工具或者浏览器默认使用的都是PASV模式连接FTP服务器，因此，必须要使vsftpd在开启了防火墙的情况下，也能够支持PASV模式进行数据访问。
_____
### 防火墙iptables

配置`/etc/vsfptd.conf`
```
pasv_enable=YES
pasv_min_port=50000
pasv_max_port=60000
```

防火墙不仅要打开20 21 端口 还要打开 50000-60000端口
```
iptables -A INPUT -p tcp --dport 21 -j ACCEPT
iptables -A INPUT -p tcp --dport 20 -j ACCEPT
iptables -A INPUT -p tcp --dport 50000:60000 -j ACCEPT
```



