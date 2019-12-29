---
title: "Golang io包"
date: 2019-12-29
draft: false
author: "Iven"
---


golang io包
<!--more-->

## 包说明
### Constants
```golang
const (
    SeekStart   = 0 // seek relative to the origin of the file
    SeekCurrent = 1 // seek relative to the current offset
    SeekEnd     = 2 // seek relative to the end
)
```

### Variables
```golang
// 读取内容正常结束的时候返回EOF错误，如果是非正常的会返回ErrUnexpectedEOF或者其他的错误
var EOF = errors.New("EOF")
// 当写入或者读取的管道已经关闭的时候会返回这个错误
var ErrClosedPipe = errors.New("io: read/write on closed pipe")
// 当未读取到内容或者返回错误的时候会返回这个错误
var ErrNoProgress = errors.New("multiple Read calls return no data or error")
// 当需要读取的长度大于提供的长度的时候会返回这个错误
var ErrShortBuffer = errors.New("short buffer")
// 写入的字节数少于需要的字节数的时候返回这个错误
var ErrShortWrite = errors.New("short write")
// 表示在读取的时候遇到了EOF的错误了
var ErrUnexpectedEOF = errors.New("unexpected EOF")
```

### Index
```golang
// 把src里的内容全部读取到dst，直到遇到EOF错误。copy成功的标志是err返回nil。
// 如果src实现了WriteTo接口，会直接调用WriteTo方法
// 如果dst实现了ReadFrom接口，会直接调用ReadFrom方法
// 内部实现也不是一次性读到dst中的，而是一次以默认32KB的大小读取和存放，如果自己实现了LimitedReader方法，会使用LimitedReader来限制自己数
// 如果自定义一次读取的大小可以用CopyN来实现，也可以用CopyBuffer
func Copy(dst Writer, src Reader) (written int64, err error)
// 和Copy方法一样，自己指定字节数组可以实现自定义长度 buf := make([]byte, 8)
func CopyBuffer(dst Writer, src Reader, buf []byte) (written int64, err error)
// 和Copy方法一样，多了一个参数可以自己指定buf byte长度 默认是32*1024byte
func CopyN(dst Writer, src Reader, n int64) (written int64, err error)
// 创建一个管道，返回PipeReader和PipeWriter，可以对读和写并行调用，保证安全。可以用来做通讯
func Pipe() (*PipeReader, *PipeWriter)
// 从r中读取至少min字节数
func ReadAtLeast(r Reader, buf []byte, min int) (n int, err error)
// 把r中读取buf的长度内容，如果不满足buf长度则报错
func ReadFull(r Reader, buf []byte) (n int, err error)
// 往w中写入字符串
func WriteString(w Writer, s string) (n int, err error)
// ByteReader接口
type ByteReader interface {
    ReadByte() (byte, error)
}
type ByteScanner interface {
    ByteReader
    UnreadByte() error
}
type ByteWriter interface {
    WriteByte(c byte) error
}
type Closer interface {
    Close() error
}
// 限制每次读取的内容
type LimitedReader struct {
    R Reader // underlying reader
    N int64  // max bytes remaining
}
// 实现一个LimitReader的方法
func LimitReader(r Reader, n int64) Reader
func (l *LimitedReader) Read(p []byte) (n int, err error)
type PipeReader struct {}
    func (r *PipeReader) Close() error
    func (r *PipeReader) CloseWithError(err error) error
    func (r *PipeReader) Read(data []byte) (n int, err error)
type PipeWriter struct {}
    func (w *PipeWriter) Close() error
    func (w *PipeWriter) CloseWithError(err error) error
    func (w *PipeWriter) Write(data []byte) (n int, err error)
// 可以合并多个Reader
func MultiReader(readers ...Reader) Reader
// 从Reader中读取的时候会从w中写入
func TeeReader(r Reader, w Writer) Reader
//  生成一个局部reader
func NewSectionReader(r ReaderAt, off int64, n int64) *SectionReader

type SectionReader struct{}
    func (s *SectionReader) Read(p []byte) (n int, err error)
    func (s *SectionReader) ReadAt(p []byte, off int64) (n int, err error)
    func (s *SectionReader) Seek(offset int64, whence int) (int64, error)
    func (s *SectionReader) Size() int64

// 同时往writer里写入
func MultiWriter(writers ...Writer) Writer
```

### interface
```golang
type ReadCloser interface {
    Reader
    Closer
}
type ReadSeeker interface {
    Reader
    Seeker
}
type ReadWriteCloser interface {
    Reader
    Writer
    Closer
}
type ReadWriteSeeker interface {
    Reader
    Writer
    Seeker
}
type ReadWriter interface {
    Reader
    Writer
}
type ReaderAt interface {
    ReadAt(p []byte, off int64) (n int, err error)
}
type ReaderFrom interface {
    ReadFrom(r Reader) (n int64, err error)
}
type RuneReader interface {
    ReadRune() (r rune, size int, err error)
}
type RuneScanner interface {
    RuneReader
    UnreadRune() error
}
// golang里神一样的存在的Reader
type Reader interface {
    Read(p []byte) (n int, err error)
}
type Seeker interface {
    Seek(offset int64, whence int) (int64, error)
}
type StringWriter interface {
    WriteString(s string) (n int, err error)
}
type WriteCloser interface {
    Writer
    Closer
}
type Writer interface {
    Write(p []byte) (n int, err error)
}

type WriterAt interface {
    WriteAt(p []byte, off int64) (n int, err error)
}
type WriterTo interface {
    WriteTo(w Writer) (n int64, err error)
}
```

## 栗子

{{< expand "CopyBuffer" "..." >}}
```golang
package main

import (
	"io"
	"log"
	"os"
	"strings"
)

func main() {
	r1 := strings.NewReader("first reader\n")
	r2 := strings.NewReader("second reader\n")
	buf := make([]byte, 8)

	// buf is used here...
	if _, err := io.CopyBuffer(os.Stdout, r1, buf); err != nil {
		log.Fatal(err)
	}

	// ... reused here also. No need to allocate an extra buffer.
	if _, err := io.CopyBuffer(os.Stdout, r2, buf); err != nil {
		log.Fatal(err)
	}

}
```
{{< /expand >}}


{{< expand "CopyBuffer" "..." >}}
```golang
package main

import (
	"bytes"
	"fmt"
	"io"
)

func main() {
	r, w := io.Pipe()

	go func() {
		fmt.Fprint(w, "some text to be read\n")
		w.Close()
	}()

	buf := new(bytes.Buffer)
	buf.ReadFrom(r)
	fmt.Print(buf.String())

}
```
{{< /expand >}}