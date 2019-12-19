---
title: "Linux sed命令"
date: 2019-12-19
draft: false
author: "Iven"
---

sed是流编辑器的缩写
<!--more-->


{{< hint info >}}
可以替换给定文本中的字符串
{{< /hint >}}
-f 
```text
[root@node1 ~]# cat data.txt
No,Name,Mark,Percent
1,Sarath,45,90
2,Alex,49,98
3,Anu,45,90


[root@node1 ~]# sed 's/Alex/alex/' data.txt
No,Name,Mark,Percent
1,Sarath,45,90
2,alex,49,98
3,Anu,45,90

从stdin中读取输入
[root@node1 ~]# cat data.txt | sed 's/Alex/alex/'
No,Name,Mark,Percent
1,Sarath,45,90
2,alex,49,98
3,Anu,45,90
```

{{< hint info >}}
替换源文本的字符串
{{< /hint >}}
```text
[root@node1 ~]# sed -i 's/Alex/alex/g' data.txt
[root@node1 ~]# cat data.txt
No,Name,Mark,Percent
1,Sarath,45,90
2,alex,49,98
3,Anu,45,90
```




{{< hint info >}}
指定范围替换字符串
{{< /hint >}}
如果要替换全文 可以在末尾添加g，如果从第n处匹配的话，可以使用ng
```text
[root@node1 ~]# cat range.txt
abcdefghijklmnopqrstuvwxyz
abcdefghijklmnopqrstuvwxyz
abcdefghijklmnopqrstuvwxyz
abcdefghijklmnopqrstuvwxy

[root@node1 ~]# sed 's/abc/ABC/g' range.txt
ABCdefghijklmnopqrstuvwxyz
ABCdefghijklmnopqrstuvwxyz
ABCdefghijklmnopqrstuvwxyz
ABCdefghijklmnopqrstuvwxy
```

{{< hint info >}}
组合多个表达式
{{< /hint >}}
组合表达式有三种方式：

- sed 'expression' |  sed 'expression'
- sed 'expression;expression'
- sed -e 'expression' -e 'expression'

```text
[root@node1 ~]# sed  -e 's/abc/ABC/g' -e 's/def/hello/g' range.txt
ABChelloghijklmnopqrstuvwxyz
ABChelloghijklmnopqrstuvwxyz
ABChelloghijklmnopqrstuvwxyz
ABChelloghijklmnopqrstuvwxy
```


---

{{< hint info >}}
说明  
{{< /hint >}}
```text
[root@node1 ~]# sed --help
Usage: sed [OPTION]... {script-only-if-no-other-script} [input-file]...

  -n, --quiet, --silent
                 suppress automatic printing of pattern space
  -e script, --expression=script
                 add the script to the commands to be executed
  -f script-file, --file=script-file
                 add the contents of script-file to the commands to be executed
  --follow-symlinks
                 follow symlinks when processing in place
  -i[SUFFIX], --in-place[=SUFFIX]
                 edit files in place (makes backup if SUFFIX supplied)
  -c, --copy
                 use copy instead of rename when shuffling files in -i mode
  -b, --binary
                 does nothing; for compatibility with WIN32/CYGWIN/MSDOS/EMX (
                 open files in binary mode (CR+LFs are not treated specially))
  -l N, --line-length=N
                 specify the desired line-wrap length for the `l' command
  --posix
                 disable all GNU extensions.
  -r, --regexp-extended
                 use extended regular expressions in the script.
  -s, --separate
                 consider files as separate rather than as a single continuous
                 long stream.
  -u, --unbuffered
                 load minimal amounts of data from the input files and flush
                 the output buffers more often
  -z, --null-data
                 separate lines by NUL characters
  --help
                 display this help and exit
  --version
                 output version information and exit
```