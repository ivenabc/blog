---
title: "Linux NetStat 命令"
date: 2019-11-18
draft: false
author: "Iven"
---

Netstat 是一款命令行工具，可用于列出系统上所有的网络套接字连接情况，包括 tcp, udp 以及 unix 套接字，另外它还能列出处于监听状态（即等待接入请求）的套接字。
<!--more-->

## 标题描述
- Proto: 协议 例如tcp,udp 
- Recv-Q: 网络接收队列，收到的数据已经在本地接受缓冲中.
- Send-Q: 网络发送队列，对方没有收到的数据或者说没有Ack的,还是本地缓冲区.
- Local Address: 显示本地端口和地址
- Foreign Address : 外部端口和地址
- State ： 状态
    1. LISTEN 表示服务正在监听，等待连接
    2. ESTABLISHED 表示服务已经正在连接中 
    3. TIME_WAIT 表示访问已经结束，比如浏览网页结束的时候会显示此状态，此状态非常短暂
    4. SYN_SENT 一种同步信号的端口，此状态变化很快，当你要访问其它的计算机的服务时首先要发个同步信号给该端口，此时状态为SYN_SENT，如果连接成功了就变为 ESTABLISHED，此时SYN_SENT状态非常短暂。但如果发现SYN_SENT非常多且在向不同的机器发出，那你的机器可能中了冲击波或震荡波 之类的病毒了。这类病毒为了感染别的计算机，它就要扫描别的计算机，在扫描的过程中对每个要扫描的计算机都要发出了同步请求，这也是出现许多 SYN_SENT的原因。

## 常用方法
### 检测端口是否在用
检测1500到1530端口是否在用
```bash
#!/bin/bash
for loop in 15{00..30}
do
    result=$(netstat -tnl | grep ":${loop} ")
    if [[ $result == "" ]]
    then
        echo "$result is not used"
    fi
done 
```
> -t 是表示显示tcp连接  
-n 是禁用域名解析功能  
-l 是显示listen的状态

### 列出所有连接
netstat -a
```text 
netstat -a 

Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:https           0.0.0.0:*               LISTEN
tcp        0     36 node1:ssh               116.230.155.238:52073   ESTABLISHED
Active UNIX domain sockets (servers and established)
Proto RefCnt Flags       Type       State         I-Node   Path
unix  2      [ ACC ]     SEQPACKET  LISTENING     10499    /run/udev/control
```

### 获取进程名，以及用户ID 
netstat -nlpte
```text
[root@node1 ~]# netstat -nlpte
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       User       Inode      PID/Program name
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN      0          14541      949/nginx: master p
tcp        0      0 0.0.0.0:8001            0.0.0.0:*               LISTEN      26         14590      922/postmaster
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      0          14542      949/nginx: master p
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      0          15786      1298/sshd
tcp6       0      0 :::8001                 :::*                    LISTEN      26         14600      922/postmaster
tcp6       0      0 :::3000                 :::*                    LISTEN      996        14826      894/grafana-server
```
> -p 是表示显示进程ID  
-e 是表示用户

### 打印统计数据
netstat -s
```text
[root@node1 ~]# netstat -s
Ip:
    1188710 total packets received
    0 forwarded
    0 incoming packets discarded
    1188709 incoming packets delivered
    1141146 requests sent out
    108 dropped because of missing route
Icmp:
    5937 ICMP messages received
    451 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 5
```
> 只要加上对应的选项-t 就能获取tcp统计信息


### 显示内核路由信息
netstat -rn
```text
[root@node1 ~]# netstat -rn
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         172.31.127.253  0.0.0.0         UG        0 0          0 eth0
169.254.0.0     0.0.0.0         255.255.0.0     U         0 0          0 eth0
172.31.112.0    0.0.0.0         255.255.240.0   U         0 0          0 eth0
```

### 打印网络接口
netstat -ie 
```text
[root@node1 ~]# netstat -ie
Kernel Interface table
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.31.124.205  netmask 255.255.240.0  broadcast 172.31.127.255
        ether 00:16:3e:06:fc:0f  txqueuelen 1000  (Ethernet)
        RX packets 772596  bytes 382978722 (365.2 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 557479  bytes 77484792 (73.8 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 654738  bytes 223136974 (212.8 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 654738  bytes 223136974 (212.8 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
> -e 可以输出用户友好的信息

### 实时监控连接的请求
netstat -ct 
```text
[root@node1 ~]# netstat -ct
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 node1:56118             245.23.241.35.bc.:https ESTABLISHED
tcp        0     36 node1:ssh               116.230.155.238:52073   ESTABLISHED
tcp        0      0 node1:48260             151.101.108.133:https   ESTABLISHED
tcp        0      0 node1:52814             100.100.30.25:http      ESTABLISHED
```
> -t 表示tcp信息


### 监控出去连接状态的tcp请求
watch -d -n0 "netstat -atnp | grep ESTA"
```text
watch -d -n0 "netstat -atnp | grep ESTA"
Every 0.1s: netstat -atnp | grep ESTA                                                                                                                                                                       Tue Nov 19 13:16:00 2019
tcp        0      0 172.31.124.205:22       116.230.155.238:52073   ESTABLISHED 15185/sshd: root@pt
tcp        0      0 172.31.124.205:52814    100.100.30.25:80        ESTABLISHED 23260/AliYunDun
```