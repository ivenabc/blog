---
title: "Find"
date: 2019-10-12T10:19:35+08:00
draft: true
tags: ["linux-command"]
categories: ["Linux"]
---

### 根据文件名或正则表达式进行搜索
选项 –name 的参数指定了文件名所必须匹配的字符
```bash
find ./ -name "*.txt" -print
```

### 匹配多个条件中的一个

```bash
find ./ \( -name "*.txt" -o -name "*.pdf" \) -print
```

### 忽略大小写
 -iname （忽略字母大小写）
```bash
find ./ -iname "example*" -print
```

### 搜索路径  
选项 -path 的参数可以使用通配符来匹配文件路径。 -name 总是用给定的文件名进行匹配。
```bash
 find /home/users -path "*/slynux/*" -print
 
 #/home/users/list/slynux.txt
 #/home/users/slynux/eg.css
```

### 正则表达式
使用正则表达式进行文本匹配的一个典型例子就是从一堆文本中解析出所有的E-mail地址。E-mail地址通常采用name@host.root这种形式，所以可以将其一般化为 
``` 
[a-z0-9]+@[a-z0-9]+.[a-z0-9]+  
```
符号 + 指明在它之前的字符类中的字符可以出现一次或多次。
```bash
 find . -regex ".*\(\.py\|\.sh\)$"
```
正则忽略大小写
```bash
 find . -iregex ".*\(\.py\|\.sh\)$"
```

### 否定参数
```bash
find . ! -name "*.txt" - print
```

### 基于目录深度的搜索
```bash
find . -maxdepth 1 -name "f*" -print
```

### 根据文件类型
```bash
find . -type d -print
```

### 根据文件时间进行搜索
Unix/Linux文件系统中的每一个文件都有三种时间戳  
以天为单位:  
访问时间（ -atime ）：用户最近一次访问文件的时间  
修改时间（ -mtime ）：文件内容最后一次被修改的时间  
变化时间（ -ctime ）：文件元数据（例如权限或所有权）最后一次改变的时间  
以时间为单位：  
-amin （访问时间）
-mmin （修改时间）
-cmin （变化时间）

> 在Unix中并没有所谓“创建时间”的概念  

-atime 、 -mtime 、 -ctime 可作为 find 的时间选项。它们可以用整数值指定，单位是天。
这些整数值通常还带有 - 或 + ： - 表示小于， + 表示大于  
```bash
#访问七天内被访问过的所有文件
find ./ -type f -atime -7 -print
#打印出恰好在7天前被访问过的所有文件
find ./ -type f -atime 7 -print
#打印出访问时间超过7天的所有文件
find . -type f -atime +7 -print
```
使用 -newer ，我们可以指定一个用于比较时间戳
的参考文件，然后找出比参考文件更新的（更近的修改时间）所有文件。  
找出比file.txt文件修改时间更近的所有文件  
```bash
find . -type f -newer file.txt -print
```

### 基于文件大小的搜索
b —— 块（512字节）。  
c —— 字节。  
w —— 字（2字节）。    
k —— 1024字节。  
M —— 1024K字节。   
G —— 1024M字节。  
```bash
# 大于2kb的文件
find . -type f -size +2k
# 小于2KB的文件
find . -type f -size -2k
# 大小等于2KB的文件
find . -type f -size 2k
```

###  删除匹配的文件
```bash
find . -type f -name "*.swp" -delete
```


### 基于文件权限和所有权的匹配
```bash
# 打印出权限为644
find . -type f -perm 644 -print
```

###  利用 find 执行命令或动作
{} 是一个与 -exec 选项搭配使用的特殊字符串。对于每一个匹配的文件，
{} 会被替换成相应的文件名
```bash
# 把root的文件改成slynux用户的
find . -type f -user root -exec chown slynux {} \;

# 把文件 拼接起来写入all_c_files.txt
find . -type f -name "*.c" -exec cat {} \;>all_c_files.txt

#用下列命令将10天前的 .txt文件复制到OLD目录中
 find . -type f -mtime +10 -name "*.txt" -exec cp {} OLD \;
```


### 让 find 跳过特定的目录
```bash
 find devel/source_path \( -name ".git" -prune \) -o \( -type f -print \)
```
\( -name ".git" -prune \) 的作用是用于进行排除，它指明了 .git目录应该被排除在外，
而 \( -type f -print \) 指明了需要执行的动作。这些动作需要被放置在第二个语句块中（打
印出所有文件的名称和路径）