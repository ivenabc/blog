---
title: "Golang bufio包"
date: 2020-01-06
draft: false
author: "Iven"
---


golang bufio包
<!--more-->

## Constants & Variables
```golang
const (
    // 默认最大的扫描字节数，如果指定的按照指定的方式
    MaxScanTokenSize = 64 * 1024
)
```

```golang
var (
    ErrInvalidUnreadByte = errors.New("bufio: invalid use of UnreadByte")
    ErrInvalidUnreadRune = errors.New("bufio: invalid use of UnreadRune")
    ErrBufferFull        = errors.New("bufio: buffer full")
    ErrNegativeCount     = errors.New("bufio: negative count")
)
```

```golang 
var (
    ErrTooLong         = errors.New("bufio.Scanner: token too long")
    ErrNegativeAdvance = errors.New("bufio.Scanner: SplitFunc returns negative advance count")
    ErrAdvanceTooFar   = errors.New("bufio.Scanner: SplitFunc returns advance count beyond input")
    ErrFinalToken = errors.New("final token")
)
```


## Func

扫描字节数组中的字节，行，rune，单词（中文不行，用空格隔开的那种）  
返回advance:前进的字节位置，token:扫描到的内容，err:报错
```golang
func ScanBytes(data []byte, atEOF bool) (advance int, token []byte, err error)
func ScanLines(data []byte, atEOF bool) (advance int, token []byte, err error)
func ScanRunes(data []byte, atEOF bool) (advance int, token []byte, err  error)
func ScanWords(data []byte, atEOF bool) (advance int, token []byte, err error)
```

Reader 
```golang
type Reader struct {}
// 生成Reader
func NewReader(rd io.Reader) *Reader
// 生成一个限定字节数的Reader
func NewReaderSize(rd io.Reader, size int) *Reader
// 返回缓存的字节数
func (b *Reader) Buffered() int
// 丢弃n个字节数，返回实际丢弃的字节数
func (b *Reader) Discard(n int) (discarded int, err error)
// 读取n个字节，但是不会读取指针前进。
// 如果n大于reader的大小则会报错ErrBufferFull 
func (b *Reader) Peek(n int) ([]byte, error)
// 读取将数据读入p,它返回实际读入的字节数n
func (b *Reader) Read(p []byte) (n int, err error)
func (b *Reader) ReadByte() (byte, error)
func (b *Reader) ReadBytes(delim byte) ([]byte, error)
func (b *Reader) ReadLine() (line []byte, isPrefix bool, err error)
func (b *Reader) ReadRune() (r rune, size int, err error)
func (b *Reader) ReadSlice(delim byte) (line []byte, err error)
func (b *Reader) ReadString(delim byte) (string, error)
// 丢弃已经读取到的字节数
func (b *Reader) Reset(r io.Reader)
// 返回缓冲区大小
func (b *Reader) Size() int
// 将最后的字节标记为未读
func (b *Reader) UnreadByte() error
func (b *Reader) UnreadRune() error
func (b *Reader) WriteTo(w io.Writer) (n int64, err error)
```


Scanner 是一个扫描器，可以自己定制分隔符来扫描
```golang
type Scanner struct {}
func NewScanner(r io.Reader) *Scanner
// 缓存buf里的内容，同时如果已经scanCalled会崩溃
func (s *Scanner) Buffer(buf []byte, max int)
// 返回非io.EOF错误，判断扫描过程中是否发生错误
func (s *Scanner) Err() error
// 返回能否Scan
func (s *Scanner) Scan() bool
// 在Scan之前可以定制化SplitFunc
func (s *Scanner) Split(split SplitFunc)
// 调用Scan后，返回token的字符串值
func (s *Scanner) Text() string
// 调用Scan后，返回token的字节数组
func (s *Scanner) Bytes() []byte
```

writer
将数据暂时缓存,可以通过调用Flush刷到writer中
```golang
// 返回一个缓存使用默认的Writer
func NewWriter(w io.Writer) *Writer
// 指定缓存大小
func NewWriterSize(w io.Writer, size int) *Writer
// 返回一个未使用的缓存大小
func (b *Writer) Available() int
// 返回已经写入writer的buffer大小
func (b *Writer) Buffered() int
// 将底层缓存的数据写入到writer
func (b *Writer) Flush() error
// 底层的writer必须要支持ReadFrom，直接从r里面读取数据，不使用buffer
func (b *Writer) ReadFrom(r io.Reader) (n int64, err error)
// 丢弃已经缓存的数据
func (b *Writer) Reset(w io.Writer)
// Size返回以字节为单位的基础缓冲区的大小
func (b *Writer) Size() int
// 将p写入缓存中，如果nn<len(p)它将返回报错信息
func (b *Writer) Write(p []byte) (nn int, err error)
func (b *Writer) WriteByte(c byte) error
func (b *Writer) WriteRune(r rune) (size int, err error)
func (b *Writer) WriteString(s string) (int, error)
```

reader和writer集合体
```golang

type ReadWriter struct {
    *Reader
    *Writer
}

// 生成一个reader
func NewReadWriter(r *Reader, w *Writer) *ReadWriter
```

## 案例

```golang
a, t, err := bufio.ScanWords([]byte("helaaalo  world"), true)
fmt.Println("[info]:", a, string(t), len(t), err) //output:9 helaaalo 8 <nil>
```

```golang
package main

import (
	"bufio"
	"fmt"
	"strings"
)

var text = `text,t1,t2
t,tt,tt
h,hh,hh
vv,vvv,vvv
`
func main() {
	reader := strings.NewReader(text)
	scanner := bufio.NewScanner(reader)
	scanner.Split(bufio.ScanLines)
	for scanner.Scan() {
		token := scanner.Bytes()
		fmt.Println("[TOKEN]:", string(token))
	}
}
```