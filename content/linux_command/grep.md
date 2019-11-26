---
title: "Linux Grep 命令"
date: 2019-11-26
draft: false
author: "Iven"
weight: 1
---

grep 命令作为Unix中用于文本搜索的神奇工具，能够接受正则表达式，生成各种格式的输出。
<!--more-->

{{< hint info >}}
对多个文件进行搜索  
{{< /hint >}}
```bash
grep "文件" *.md
grep "文件" ip.md set.md
```
{{< hint info >}}
输出行中着重标记出匹配到的单词
{{< /hint >}}
```bash
grep "文件" *.md --color=auto
```

{{< hint info >}}
使用正则表达式进行搜索
{{< /hint >}}
```bash
$ grep -E "^[0-9]" ip.md
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
```
{{< hint info >}}
递归搜索文件
{{< /hint >}}
```bash
$ grep "text" . -R -n
```
> -R -r功能一样 -n可以显示行号

{{< hint info >}}
利用grep删除文件
{{< /hint >}}
```bash
$ grep "test" file* -l
file1
file3

$ grep "test" file* -lZ | xargs -0 rm

$ ls
file2

```
>-Z 选项输出以0值字节作为终结符的文件名（ \0 ）