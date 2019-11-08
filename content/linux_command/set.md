---
title: "Bash Set命令"
date: 2019-11-06
draft: false
author: "Iven"
weight: 1
---


set命令常常用来调试脚本功能

<!--more-->

## set -x 
set -x （等价于 set -o xtrace）命令可以打印执行的那一行命令，并且在前面加上一个+

```bash
set -x 
echo hello
```
结果：
```
+ echo hello
hello
```

