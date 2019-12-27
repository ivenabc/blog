---
title: "Linux IP 命令"
date: 2019-11-19
draft: false
author: "Iven"
---

ip 提供了一系列管理网络的功能
<!--more-->

## 常用方法
### 添加IP地址
```text
[root@node1 ~]# ip addr add 172.31.124.204/20 dev eth0
[root@node1 ~]# ip addr show
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:16:3e:06:fc:0f brd ff:ff:ff:ff:ff:ff
    inet 172.31.124.205/20 brd 172.31.127.255 scope global dynamic eth0
       valid_lft 313732299sec preferred_lft 313732299sec
    inet 172.31.124.204/20 scope global secondary eth0
       valid_lft forever preferred_lft forever
```
### 删除IP地址
```text
[root@node1 ~]# ip addr del 172.31.124.204/20 dev eth0
[root@node1 ~]# ip addr show
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:16:3e:06:fc:0f brd ff:ff:ff:ff:ff:ff
    inet 172.31.124.205/20 brd 172.31.127.255 scope global dynamic eth0
       valid_lft 313732240sec preferred_lft 313732240sec
```

### 激活和停止网络接口
```text
sudo ip link set ens33 down
 
sudo ip link set ens33 up
```
