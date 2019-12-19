---
title: "Linux cut命令"
date: 2019-12-19
draft: false
author: "Iven"
---

cut 可以分割linux文件内容
<!--more-->

{{< hint info >}}
提取特定的字段或列  
{{< /hint >}}
-f 
```text
[root@node1 ~]# cat data.txt
No,Name,Mark,Percent
1,Sarath,45,90
2,Alex,49,98
3,Anu,45,90

[root@node1 ~]# cut -f 2,3 -d "," data.txt
Name,Mark
Sarath,45
Alex,49
Anu,45
```

{{< hint info >}}
指定字段的字符  
{{< /hint >}}
```text
[root@node1 ~]# cat range.txt
abcdefghijklmnopqrstuvwxyz
abcdefghijklmnopqrstuvwxyz
abcdefghijklmnopqrstuvwxyz
abcdefghijklmnopqrstuvwxy

[root@node1 ~]# cut -c1-5 range.txt
abcde
abcde
abcde
abcde

[root@node1 ~]# cut range.txt -c1-3,6-9 --output-delimiter ","
abc,fghi
abc,fghi
abc,fghi
abc,fghi
```


---

{{< hint info >}}
说明  
{{< /hint >}}
```text
Usage: cut OPTION... [FILE]...
Print selected parts of lines from each FILE to standard output.

Mandatory arguments to long options are mandatory for short options too.
  -b, --bytes=LIST        select only these bytes
  -c, --characters=LIST   select only these characters
  -d, --delimiter=DELIM   use DELIM instead of TAB for field delimiter
  -f, --fields=LIST       select only these fields;  also print any line
                            that contains no delimiter character, unless
                            the -s option is specified
  -n                      with -b: don't split multibyte characters
      --complement        complement the set of selected bytes, characters
                            or fields
  -s, --only-delimited    do not print lines not containing delimiters
      --output-delimiter=STRING  use STRING as the output delimiter
                            the default is to use the input delimiter
      --help     display this help and exit
      --version  output version information and exit

Use one, and only one of -b, -c or -f.  Each LIST is made up of one
range, or many ranges separated by commas.  Selected input is written
in the same order that it is read, and is written exactly once.
Each range is one of:

  N     N'th byte, character or field, counted from 1
  N-    from N'th byte, character or field, to end of line
  N-M   from N'th to M'th (included) byte, character or field
  -M    from first to M'th (included) byte, character or field

With no FILE, or when FILE is -, read standard input.
```