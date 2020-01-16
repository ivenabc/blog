---
title: "Golang qa"
date: 2020-01-09
draft: true
author: "Iven"
---

golang qa

<!--more-->




{{< hint info >}}
## 字节换算单位
{{< /hint >}}
```text
1B(Byte)=8bit，
1KB (Kilobyte)=1024B，
1MB (Megabyte)=1024KB，
1GB (Gigabyte)=1024MB，
1TB (Trillionbyte)=1024GB，其中1024=2^10 ( 2 的10次方)
```
---

{{< hint info >}}
## 类型别名和类型声明的区别
{{< /hint >}}
类型别名和原类型是相同的，而类型定义和原类型是不同的两个类型
```golang
package main
type T1 = int
type T2 int
func main() {
	v := 10
	var d T1 = v
	var i T2 = v // 会报错  
}
```