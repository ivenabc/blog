---
title: "Linux find命令"
date: 2020-01-01
draft: false
author: "Iven"
---

Linux find命令
<!--more-->

## 常用选项说明

- -print: 打印输出，默认自带属性，可以不写
- -name: 指定文件名所必须匹配的字符 `find ./ -name "*.tar"`
- -iname: 在`-name`基础上忽略大小写
- -path: 匹配文件目录
- -regex: 正则表达式
- -iregex: 忽略大小写的正则表达式
- !: 否定参数 
- -maxdepth: 基于目录的深度搜索。eg: `-maxdepth 1`表示当前目录下搜索，如果命令中含有`-path` ，需要放在`-path`前面
- -type: 根据文件类型(f,l,d,c,b,s,p)。 eg: `-type f` 
- -atime: 用户最近访问的的时间，计量单位是天。- 表示小于，+ 表示大于。eg: `-atime -7`最近7天内访问的，`-atime 7`表示7天前访问的，`-atime +7`表示超过7天访问的。
- -mtime: 用户最近修改的时间。- 表示小于，+ 表示大于。
- -ctime: 文件元数据(例如权限或所有权)最后一次改变的时间。- 表示小于，+ 表示大于。
- -amin: 同-atime，单位是分钟。
- -mmin: 同-mtime, 单位是分钟。
- -cmin: 同-ctime, 单位是分钟。
- -size: 根据文件大小查询文件 单位是(c,k,M,G) eg:  `-size +5M` 超过5M大小
- -delete: 删除匹配的文件 `find . -type f -name "*.swp" -delete`
- -perm: 打印出匹配的权限 `-perm 644`
- -exec: 后面接要执行的命令，eg:`find ./ -name "*.go" -exec head -n 1 {} \;`,`{}`表示文件名 `\;`表示命令里的一行，表示结束了。


## 栗子

{{< hint info >}}
查询特定的目录 
{{< /hint >}}
```bash
 find ./ -name "*account" -path "*/services/*" -type d
 ```

{{< hint info >}}
查询7天前修改的go文件
{{< /hint >}}
```bash
mac:ivenabc wubin$ find ./ -name "*.go"  -mtime +7
.//library/jwt/jwt.go
.//library/xencrypt/aes.go
```

{{< hint info >}}
打印匹配到的文件每一行
{{< /hint >}}
```bash
find ./ -name "*.go" -exec head -n 1 {} \;
 ```
