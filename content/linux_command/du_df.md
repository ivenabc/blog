---
title: "Linux df和du"
date: 2019-11-06
draft: true
author: "Iven"
weight: 1
---

df 和 du 是Linux中用于统计磁盘使用情况的两个重要命令。 df 是disk free的缩写， du 是disk
usage的缩写。

<!--more-->

## du 


{{< hint info >}}
对多个文件进行搜索  
{{< /hint >}}
```
-0, --null            end each output line with 0 byte rather than newline
-a, --all             打印所有文件大小
-b, --bytes           equivalent to '--apparent-size --block-size=1'
-m                    like --block-size=1M

-c, --total           在末尾打印出一个统计
-D, --dereference-args  dereference only symlinks that are listed on the
                        command line
-d, --max-depth=N     print the total for a directory (or file, with --all)
                        only if it is N or fewer levels below the command
                        line argument;  --max-depth=0 is the same as
                        --summarize
    --files0-from=F   根据文件F里的文件名进行查询
-H                    equivalent to --dereference-args (-D)
-h, --human-readable  print sizes in human readable format (e.g., 1K 234M 2G)
    --inodes          list inode usage information instead of block usage
-L, --dereference     dereference all symbolic links
-l, --count-links     count sizes many times if hard linked
-P, --no-dereference  don't follow any symbolic links (this is the default)
-S, --separate-dirs   for directories do not include size of subdirectories
    --si              like -h, but use powers of 1000 not 1024
-s, --summarize       display only a total for each argument
-t, --threshold=SIZE  exclude entries smaller than SIZE if positive,
                        or entries greater than SIZE if negative
    --time            show time of the last modification of any file in the
                        directory, or any of its subdirectories
    --time=WORD       show time as WORD instead of modification time:
                        atime, access, use, ctime or status
    --time-style=STYLE  show times using STYLE, which can be:
                        full-iso, long-iso, iso, or +FORMAT;
                        FORMAT is interpreted like in 'date'
-X, --exclude-from=FILE  exclude files that match any pattern in FILE
    --exclude=PATTERN    exclude files that match PATTERN
-x, --one-file-system    skip directories on different file systems
```

{{< hint info >}}
递归地输出指定目录或多个目录中所有文件的统计结果
{{< /hint >}}
```bash
du -ah tablespace1/
```
> -h 打印可读性更强的文件大小格式(e.g., 1K 234M 2G)

{{< hint info >}}
输出合计数据
{{< /hint >}}
```bash
du -sh ./
```