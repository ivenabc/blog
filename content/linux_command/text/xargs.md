---
title: "Linux xargs命令"
date: 2020-01-01
draft: false
author: "Iven"
---

xargs作用是将标准输入数据转换成命令行参数
<!--more-->

xargs 默认是将空格" "作为定界符

## 常用选项

- -d:为输入指定一个定制的定界符 eg: `echo "splitXsplitXsplitXsplit" | xargs -d X` 用X作为界定符
- -n: 一行包含多少个参数 eg: `echo "splitXsplitXsplitXsplit" | xargs -d X -n 2` 输出是两个`split split`作为一行
- -0: 将 `\0`作为定界符


## 栗子

{{< hint info >}}
利用xargs把参数循环传入脚本
{{< /hint >}}
创建一个脚本，打印所有传入参数
```text
#!/bin/bash 
#文件名: cecho.sh
echo $*'#'
```
无占位符的方式
```bash
echo "splitXsplitXsplitXsplit" | xargs -d X  -n 1 ./cecho.sh
```
有占位符的方式
```bash
cat args.txt | xargs -I {} ./cecho.sh -p {} -l
```

{{< hint info >}}
配合find命令删除文件
{{< /hint >}}
```bash
find . -type f -name "*.txt" -print0 | xargs -0 rm -f
```
> 由于无法知道find的界定符是什么，比如hell text.txt会被xargs误解为hell和text.txt，可能会导致误删除。
> -print0可以将每个结果后面加一个\0，xargs 加入 -0的参数，指定\0作为界定符，这样就可以避免误删除

{{< hint info >}}
查询每个匹配到的文件的行数
{{< /hint >}}
```
 find ./ -name "*.go" -print0 | xargs -0 wc -l
```
