---
title: "VI 使用"
date: 2019-11-08
draft: false
author: "Iven"
weight: 1
---


vi命令常常用来编辑文件

<!--more-->

## 替换文本
:[addr]s/源字符串/目标字符串/[option]
参数说明:
[addr] - 表示检索范围，省略时表示当前行
- % ：表示整个文件，同"1,$"
- 1,$ : 从当前行到文件尾
[option] - 表示操作类型
- g: 全局替换
- c: 表示进行确认
- p: 表示替换结果逐行显示（Ctrl+L恢复屏幕）

```bash
:s/20/22/ #把第一个20换成22
:s/20/22/g #把全部20换成22
```
结果：
```
+ echo hello
hello
```
