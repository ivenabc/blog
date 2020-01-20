---
title: "Golang net/url"
date: 2020-01-19
draft: false
author: "Iven"
---

golang net/url包
<!--more-->

## URL 转义
```golang
// 可以把url格式字符串给转义了。能够放在url里 例如：a/b ==> a%2Fb
func PathEscape(s string) string
// 反方向PathEscape
func PathUnescape(s string) (string, error)
// QueryEscape对字符串进行转义，以便可以将其安全地放置在URL查询中。
func QueryEscape(s string) string
// 进行QueryEscape的逆变换
func QueryUnescape(s string) (string, error)
```

## Error
```golang
type Error
    func (e *Error) Error() string
    func (e *Error) Temporary() bool
    func (e *Error) Timeout() bool
    func (e *Error) Unwrap() error
type EscapeError
    func (e EscapeError) Error() string
type InvalidHostError
    func (e InvalidHostError) Error() string
```

## URL
url的结构
```
[scheme:][//[userinfo@]host][/]path[?query][#fragment]
```

```golang
type URL struct {
    Scheme     string
    Opaque     string    // encoded opaque data
    User       *Userinfo // username and password information
    Host       string    // host or host:port
    Path       string    // path (relative paths may omit leading slash)
    RawPath    string    // encoded path hint (see EscapedPath method); added in Go 1.5
    ForceQuery bool      // append a query ('?') even if RawQuery is empty; added in Go 1.7
    RawQuery   string    // encoded query values, without '?'
    Fragment   string    // fragment for references, without '#'
}
// 可以解析绝对路径和相对路径
// https://www.bing.com/aa heello?#faf => https://www.bing.com/aa%20heello?#faf
func Parse(rawurl string) (*URL, error)
// 与Parse的区别是，ParseRequestURI不包括fragment 
// https://www.bing.com/aa heello?#faf => https://www.bing.com/aa%20heello
func ParseRequestURI(rawurl string) (*URL, error)
// 转义路径
func (u *URL) EscapedPath() string
// 返回hostName eg: https://example.org:8000/path => example.org
func (u *URL) Hostname() string
// 返回是否是绝对路径
func (u *URL) IsAbs() bool
// https://example.org:8000/path  aaa => []byte("https://example.org:8000/path%20%20aaa")
func (u *URL) MarshalBinary() (text []byte, err error)
// 传入相对路径，生成新的绝对路径，之前包含的path里面会被替代
func (u *URL) Parse(ref string) (*URL, error)
// 获取路径里包含的端口，没有返回空
func (u *URL) Port() string
// 获取query部分值
func (u *URL) Query() Values
// 返回URI 内容
func (u *URL) RequestURI() string
// 可以引用另一个url的相对部分ref url,合并在自己的身上
func (u *URL) ResolveReference(ref *URL) *URL
func (u *URL) String() string
func (u *URL) UnmarshalBinary(text []byte) error
```

###  Example
当url路径里面没有转义字符串的时候，path和rawPath一样。当路径含有转义字符串的时候path就会显示转义后的，rawPath保持一样
```golang
package main
import (
	"fmt"
	"log"
	"net/url"
)
func main() {
	// Parse + String preserve the original encoding.
	u, err := url.Parse("https://example.com/foo%2fbar")
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(u.Path)  // /foo/bar
	fmt.Println(u.RawPath) // /foo%2fbar
	fmt.Println(u.String()) //https://example.com/foo%2fbar
}
```

修改url里的值
```golang
package main
import (
	"fmt"
	"log"
	"net/url"
)
func main() {
	u, err := url.Parse("http://bing.com/search?q=dotnet")
	if err != nil {
		log.Fatal(err)
	}
	u.Scheme = "https"
	u.Host = "google.com"
	q := u.Query()
	q.Set("q", "golang")
	u.RawQuery = q.Encode()
    fmt.Println(u.String())
    // https://google.com/search?q=golang
}
```

url的EscapedPath()与Path区别
```golang
package main
import (
	"fmt"
	"log"
	"net/url"
)
func main() {
	u, err := url.Parse("http://example.com/path with spaces")
	if err != nil {
		log.Fatal(err)
    }
    fmt.Println(u.String()) // http://example.com/path%20with%20spaces
	fmt.Println(u.EscapedPath()) // /path%20with%20spaces
	fmt.Println(u.Path)   // /path with spaces
}
```

重新生成新的url
```golang
package main

import (
	"fmt"
	"log"
	"net/url"
)

func main() {
	u, err := url.Parse("https://example.org/hello")
	if err != nil {
		log.Fatal(err)
	}
	rel, err := u.Parse("/foo")
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(rel)  //https://example.org/foo
	_, err = u.Parse(":foo")
	if _, ok := err.(*url.Error); !ok {
		log.Fatal(err)
	}
}

```

获取query部分值
```golang
package main

import (
	"fmt"
	"log"
	"net/url"
)

func main() {
	u, err := url.Parse("https://example.org/?a=1&a=2&b=&=3&&&&")
	if err != nil {
		log.Fatal(err)
	}
	q := u.Query()
	fmt.Println(q["a"]) //[1 2]
	fmt.Println(q.Get("b")) // 
	fmt.Println(q.Get("")) //3
}
```

引用另一个url的内容
```golang
package main

import (
	"fmt"
	"log"
	"net/url"
)

func main() {
	u, err := url.Parse("../../..//search?q=dotnet")
	if err != nil {
		log.Fatal(err)
	}
	base, err := url.Parse("http://example.com/directory/")
	if err != nil {
		log.Fatal(err)
	}
    fmt.Println(base.ResolveReference(u))
    // http://example.com/search?q=dotnet
}
```

## UserInfo
```golang
type Userinfo struct {
    // contains filtered or unexported fields
}
func User(username string) *Userinfo
func UserPassword(username, password string) *Userinfo
func (u *Userinfo) Password() (string, bool)
func (u *Userinfo) String() string
func (u *Userinfo) Username() string
```

## Values
```golang
type Values map[string][]string
func ParseQuery(query string) (Values, error)
func (v Values) Del(key string)
func (v Values) Add(key, value string)
func (v Values) Set(key, value string)
func (v Values) Get(key string) string
func (v Values) Encode() string

```
### Example
```golang
package main

import (
	"fmt"
	"net/url"
)

func main() {
	v := url.Values{}
	v.Set("name", "Ava")
	v.Add("friend", "Jess")
	v.Add("friend", "Sarah")
	v.Add("friend", "Zoe")
	// v.Encode() == "name=Ava&friend=Jess&friend=Sarah&friend=Zoe"
	fmt.Println(v.Get("name"))
	fmt.Println(v.Get("friend"))
	fmt.Println(v["friend"])
}
// Ava
// Jess
// [Jess Sarah Zoe]
```

