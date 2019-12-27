---
title: "Linux PS"
date: 2019-12-27
draft: false
author: "Iven"
---

ps 是收集进程信息的重要工具。它提供的信息包括：拥有进程的用户、进程的起始时间、进程对应的命令行路径、PID、进程所属的终端（TTY）、进程使用的内存、进程占用的CPU等

<!--more-->

## 常用选项说明

- f : ps -f 表示所有列的信息都会显示出来
- a : ps -a 表示所有终端(tty)的程序（用户进入终端）
- x : ps -x 表示所有非终端(tty)的程序 tty那一栏显示的是问号的就是不依赖终端的程序
- e : ps -e 表示所有进程的 等于 ps -ax 
- o : ps -o 可以打印出需要的列，可用的列有：pcpu,pid,ppid,pmem,comm等


---

## 信息含义

### stat列的信息

状态 | 含义
---|---
R |	运行状态。进程正在运行或准备运行
S | 睡眠状态。进程不在运行，而是在等待某事件的发生，如键盘输入或者收到网络报文
D | 不可中断的睡眠状态，进程在等待 I/O 操作，如硬盘驱动
T | 暂停状态。进程被指示暂停（后续还可以继续运行）
Z | 无效或者“僵尸”进程。子进程被终止，但是还没有被父进程彻底释放掉
< | 高优先级进程。进程可以被赋予更多的重要性，分配更多的 CPU 时间
N |低优先级进程

### -o列说明


参数 | 描述
---|---
pcpu | CPU占用率
pid | 进程ID
ppid | 父进程ID
pmem | 内存使用率
comm | 可执行文件名
cmd | 简单命令
user | 启动进程的用户
nice | 优先级
time | 累计的CPU时间 
etime | 进程启动后流逝的时间
tty | 所关联的TTY设备
euid | 有效用户ID 
stat | 进程状态


## 常用案例
{{< hint info >}}
列出占用CPU最多的10个进程
{{< /hint >}}

```bash
ps -eo comm,pcpu --sort -pcpu | head
```


{{< hint info >}}
找出给定命令名所对应的进程ID
{{< /hint >}}
```bash
ps -C ./prometheus -o pid=
```


{{< hint info >}}
进程线程的相关信息
{{< /hint >}}
NLWP是进程的线程数量，NLP是ps输出中每个条目的线程ID
```bash
ps -eLf --sort -nlwp | head
```