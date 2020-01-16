
---
title: "Golang tar包"
date: 2019-12-28
draft: false
author: "Iven"
---

golang tar的实现
<!--more-->

```golang
//  --! pass
type Format
    func (f Format) String() string
// 传入FileInfo和link，link值可以用FileInfo.Name()来代替
// os.FileInfo的Name方法仅返回文件名，所以Header的name需要自己修改
func FileInfoHeader(fi os.FileInfo, link string) (*Header, error)
//  
type Header
    func (h *Header) FileInfo() os.FileInfo
type Reader
    func NewReader(r io.Reader) *Reader
    // Next 返回 下一个存档文件，如果是文件非目录，可以直接读取tr来返回值
    // io.EOF 表示读取结束
    func (tr *Reader) Next() (*Header, error)
    func (tr *Reader) Read(b []byte) (int, error)
type Writer
    func NewWriter(w io.Writer) *Writer
    func (tw *Writer) Close() error
    func (tw *Writer) Flush() error
    func (tw *Writer) Write(b []byte) (int, error)
    func (tw *Writer) WriteHeader(hdr *Header) error
```

## 案例

{{< expand "打包" "..." >}}
```golang

func main1() {
	var tw *tar.Writer
	f, _ := os.OpenFile("./data.tar", os.O_CREATE|os.O_RDWR, 0755)
	defer f.Close()
	tw = tar.NewWriter(f)
	defer tw.Close()

	source := "/Users/iven/workspace/gowork/src/github.com/ivenabc/e-commerce/"

	baseDir := ""
	fi, _ := os.Stat(source)
	if fi.IsDir() {
		baseDir = filepath.Base(source)
	}
	fmt.Println("BASE DIR:", baseDir)
	filepath.Walk(source, func(path string, info os.FileInfo, err error) error {
		if err != nil {
			return err
		}
		// fmt.Println(info.Name())
		header, err := tar.FileInfoHeader(info, info.Name())
		if baseDir != "" {
			header.Name = filepath.Join(baseDir, strings.TrimPrefix(path, source))
		}
		tw.WriteHeader(header)
		if info.IsDir() {
			return nil
		}

		file, err := os.Open(path)
		if err != nil {
			return err
		}

		_, err = io.Copy(tw, file)
		if err != nil {
			return err
		}
		return nil
	})
	tw.Flush()
}
```
{{< /expand >}}


{{< expand "解压" "..." >}}
```golang
func main() {
	f, _ := os.OpenFile("./data.tar", os.O_RDWR, 0755)
	defer f.Close()
	tr := tar.NewReader(f)
	for {
		h, err := tr.Next()
		if err == io.EOF {
			break
		} else if err != nil {
			return
		}
		info := h.FileInfo()
		if info.IsDir() {
			os.MkdirAll(h.Name, info.Mode())
			continue
		}
		file, err := os.OpenFile(h.Name, os.O_CREATE|os.O_TRUNC|os.O_WRONLY, info.Mode())
		if err != nil {
			return
		}
		defer file.Close()

		io.Copy(file, tr)

	}

}
```
{{< /expand >}}