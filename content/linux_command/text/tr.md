---
title: "Linux tr命令"
date: 2020-01-01
draft: false
author: "Iven"
---

tr命令是用来替换文本
<!--more-->


tr只能通过stdin(标准输入)，而无法通过命令行参数来接受输入。它的调用格式如下:  
```text
tr [options] set1 set2 
```
将来自stdin的输入字符从set1映射到set2，然后将输出写入stdout(标准输出)。set1 和set2是字符类或字符集。如果两个字符集的长度不相等，那么set2会不断重复其最后一个字 符，直到长度与set1相同。如果set2的长度大于set1，那么在set2中超出set1长度的那部分 字符则全部被忽略。

---

## 常用选项
- -d: 指定需要被删除的字符集合  
- -c: 补集，配合-d 使用 删除不在补集中的内容
- -s: 删除多余的字符 eg: `tr -s '\n'` 删除`\n`

---

## 例子
{{< hint info >}}
大写变小写
{{< /hint >}}
```bash
echo "HELLO WHO IS THIS" | tr 'A-Z' 'a-z'
```

{{< hint info >}}
rot13加密方法
{{< /hint >}}
```bash
echo "tr came, tr saw, tr conquered." | tr 'a-zA-Z' 'n-za-mN-ZA-M'
```

{{< hint info >}}
删除数字
{{< /hint >}}
```bash
echo "Hello 123 world 456" | tr -d '0-9'
```