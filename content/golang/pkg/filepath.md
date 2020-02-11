---
title: "Golang http"
date: 2020-02-09
draft: true
author: "Iven"
---

## 索引
```golang
func Abs(path string) (string, error)
func Base(path string) string
func Clean(path string) string
func Dir(path string) string
func EvalSymlinks(path string) (string, error)
func Ext(path string) string
func FromSlash(path string) string
func Glob(pattern string) (matches []string, err error)
func HasPrefix(p, prefix string) bool
func IsAbs(path string) bool
func Join(elem ...string) string
func Match(pattern, name string) (matched bool, err error)
func Rel(basepath, targpath string) (string, error)
func Split(path string) (dir, file string)
func SplitList(path string) []string
func ToSlash(path string) string
func VolumeName(path string) string
func Walk(root string, walkFn WalkFunc) error
type WalkFunc
```

## 解释
### 绝对路径
返回绝对路径
```
func Abs(path string) (string, error)
```

### 最后一个路径部分
返回路径的最后一部分，比如：  
/foo/bar/baz.js -> baz.js  
/foo/bar/baz -> baz  
/foo/bar/baz/ -> baz  
dev.txt -> dev.txt  
../todo.txt -> todo.txt  
.. -> ..  
. -> .  
/ -> /  
"" -> ""
```golang
func Base(path string) string
```

### 最短有效路径
返回最短有效路径。  
../../txt/// -> ../../txt
```golang
func Clean(path string)string
```

### 返回路径的目录
Dir返回除路径的最后一个元素以外的所有元素，通常是路径的目录。删除最后一个元素后，Dir会在路径上调用Clean并删除斜杠。如果路径为空，则Dir返回“。”。如果路径完全由分隔符组成，则Dir返回单个分隔符。除非它是根目录，否则返回的路径不会以分隔符结尾。  
/foo/bar/baz.js -> /foo/bar  
/foo/bar/baz -> /foo/bar  
/foo/bar/baz/ -> /foo/bar/baz  
/dirty//path/// -> /dirty/path  
dev.txt -> .  
../todo.txt -> ..  
/ -> /  
```golang
func Dir(path string) string
```

### 返回后缀名
根据路径最后一个点来获取后缀名，如果没有点则返回空。  
index -> ""  
index.js -> .js  
main.test.js -> .js  
```golang
func Ext(path string) string
```

### 路径变换
windows和linux路径不一致，通常两种路径需要相互转换。  
linux下路径转换windows可以用：
```golang
func FromSlash(path string) string
```
windows下路径转换成linux：
```golang
func ToSlash(path string) string
```

### 合并路径
合并多个路径，根据Separator添加分隔符  
unix下：  
"a", "b", "c" -> a/b/c 
```golang
func Join(elem ...string) string
```

### 获取相对路径
根据基础路径，把相对路径提取出来。如果传入相对路径会报错。  
路径：`/a/b/c`在基础路径为 `/a`的情况下，会提取`b/c`
```golang
func Rel(basepath, targpath string) (string, error)
```

### 提取目录和文件
可以根据路径提取目录和文件相当于 path=dir+file
```golang
func Split(path string) (dir, file string)
```

### 列出匹配的文件或目录
 列出与指定的模式 pattern 完全匹配的文件或目录  
比如：pattern是`usr/*`表示列出usr/目录下的文件
```golang
Glob(pattern string) (matches []string, err error)
```