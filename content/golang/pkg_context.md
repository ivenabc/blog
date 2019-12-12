---
title: "Golang context包"
date: 2019-12-12
draft: false
author: "Iven"
---

golang context包
<!--more-->

golang 的context主要的用途用来控制goroutine ，context的使用建议：

- context应该通过方法传递，不能放在结构体中传递
- 不知道要传递什么类型的context的时候，可以用context.TODO
- context的参数建议传递必须的

**包功能说明**
```golang
// 返回一个context，同时返回一个cancel方法，用来取消goroutine的
// 调用cancel方法会关闭context.Done()返回的channel，以此来触发通知
func WithCancel(parent Context) (ctx Context, cancel CancelFunc)
// 相对于WithCancel多了一个超时时间，如果超时了 就直接关闭context.Done()返回的channel
func WithDeadline(parent Context, d time.Time) (Context, CancelFunc)
// 和WithDeadline一样，只是超时时间换成了多少时间后取消
func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)
type CancelFunc
type Context
    // 定义一个空的context
    func Background() Context
    // 当你不知道改用哪个的时候就暂时用它吧
    func TODO() Context
    // 可以往context里保存参数，建议只传必要的
    func WithValue(parent Context, key, val interface{}) Context
```

---
<br>
**Examples**

**WithCancel**
```golang
package main

import (
	"context"
	"fmt"
)

func main() {
	gen := func(ctx context.Context) <-chan int {
		dst := make(chan int)
		n := 1
		go func() {
			for {
				select {
				case <-ctx.Done():
					return // 检测到关闭直接返回
				case dst <- n:
					n++
				}
			}
		}()
		return dst
	}

	ctx, cancel := context.WithCancel(context.Background())
	defer cancel() // cancel when we are finished consuming integers

	for n := range gen(ctx) {
		fmt.Println(n)
		if n == 5 {
			break
		}
	}
}
```

**WithDeadline**
```golang
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	d := time.Now().Add(50 * time.Millisecond)
	ctx, cancel := context.WithDeadline(context.Background(), d)

    // 即使ctx已经过期了，最佳得实践方式也还是主动触发一下
	defer cancel()

	select {
	case <-time.After(1 * time.Second):
		fmt.Println("overslept")
	case <-ctx.Done():
		fmt.Println(ctx.Err())
	}

}
```