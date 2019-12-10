---
title: "Golang sync包"
date: 2019-12-10
draft: false
author: "Iven"
---

Golang sync包
<!--more-->

{{< hint info >}}
## type Cond
{{< /hint >}}

方法
```golang
//创建一个Cond，需要传入一个Locker 接口，可以使用sync.Mutex。
func NewCond(l Locker) *Cond 
//唤醒所有使用wait()方法的goroutines
func (c *Cond) Broadcast() 
//唤醒单个使用wait()方法的goroutines
func (c *Cond) Signal() 
// Wait方法会自动解锁c.L ，并且暂停往下执行goroutine,当使用Broadcast()和Signal方法后会解锁wait，并且往下执行
func (c *Cond) Wait()
```


{{< hint info >}}
## type Map
{{< /hint >}}
sync.Map是协程安全的map
```golang
func (m *Map) Delete(key interface{})
func (m *Map) Load(key interface{}) (value interface{}, ok bool)
func (m *Map) LoadOrStore(key, value interface{}) (actual interface{}, loaded bool)
func (m *Map) Range(f func(key, value interface{}) bool)
func (m *Map) Store(key, value interface{})
```

{{< hint info >}}
## type Mutex
{{< /hint >}}
```golang
func (m *Mutex) Lock()
func (m *Mutex) Unlock()
```

{{< hint info >}}
## type Once
{{< /hint >}}
```golang
func (o *Once) Do(f func())
```
example:
```golang
var once sync.Once
onceBody := func() {
    fmt.Println("Only once")
}
done := make(chan bool)
for i := 0; i < 10; i++ {
    go func() {
        once.Do(onceBody)
        done <- true
    }()
}
for i := 0; i < 10; i++ {
    <-done
}
```

{{< hint info >}}
## type Pool
{{< /hint >}}
```golang
func (p *Pool) Get() interface{}
func (p *Pool) Put(x interface{})
```
example
```golang
package main

import (
	"bytes"
	"io"
	"os"
	"sync"
	"time"
)
var bufPool = sync.Pool{
	New: func() interface{} {
		return new(bytes.Buffer)
	},
}
func timeNow() time.Time {
	return time.Unix(1136214245, 0)
}
// Log log
func Log(w io.Writer, key, val string) {
	b := bufPool.Get().(*bytes.Buffer)
	b.Reset()
	b.WriteString(timeNow().UTC().Format(time.RFC3339))
	b.WriteByte(' ')
	b.WriteString(key)
	b.WriteByte('=')
	b.WriteString(val)
	w.Write(b.Bytes())
	bufPool.Put(b)
}
func main() {
	Log(os.Stdout, "path", "/search?q=flowers")
}
```

{{< hint info >}}
## type RWMutex
{{< /hint >}}
```golang
// 完全锁住，禁止读和写
func (rw *RWMutex) Lock()
// 解锁 配对 Lock()
func (rw *RWMutex) Unlock()
// 写锁住，允许读
func (rw *RWMutex) RLock()
// 解锁，配对 RLock
func (rw *RWMutex) RUnlock()
// 返回自己实现的Locker接口
func (rw *RWMutex) RLocker() Locker
```

{{< hint info >}}
## type WaitGroup
{{< /hint >}}
```golang
func (wg *WaitGroup) Add(delta int)
func (wg *WaitGroup) Done()
func (wg *WaitGroup) Wait()
```

{{< hint info >}}
## Package atomic
{{< /hint >}}
```golang
func AddInt32(addr *int32, delta int32) (new int32)
func AddInt64(addr *int64, delta int64) (new int64)
func AddUint32(addr *uint32, delta uint32) (new uint32)
func AddUint64(addr *uint64, delta uint64) (new uint64)
func AddUintptr(addr *uintptr, delta uintptr) (new uintptr)
func CompareAndSwapInt32(addr *int32, old, new int32) (swapped bool)
func CompareAndSwapInt64(addr *int64, old, new int64) (swapped bool)
func CompareAndSwapPointer(addr *unsafe.Pointer, old, new unsafe.Pointer) (swapped bool)
func CompareAndSwapUint32(addr *uint32, old, new uint32) (swapped bool)
func CompareAndSwapUint64(addr *uint64, old, new uint64) (swapped bool)
func CompareAndSwapUintptr(addr *uintptr, old, new uintptr) (swapped bool)
func LoadInt32(addr *int32) (val int32)
func LoadInt64(addr *int64) (val int64)
func LoadPointer(addr *unsafe.Pointer) (val unsafe.Pointer)
func LoadUint32(addr *uint32) (val uint32)
func LoadUint64(addr *uint64) (val uint64)
func LoadUintptr(addr *uintptr) (val uintptr)
func StoreInt32(addr *int32, val int32)
func StoreInt64(addr *int64, val int64)
func StorePointer(addr *unsafe.Pointer, val unsafe.Pointer)
func StoreUint32(addr *uint32, val uint32)
func StoreUint64(addr *uint64, val uint64)
func StoreUintptr(addr *uintptr, val uintptr)
func SwapInt32(addr *int32, new int32) (old int32)
func SwapInt64(addr *int64, new int64) (old int64)
func SwapPointer(addr *unsafe.Pointer, new unsafe.Pointer) (old unsafe.Pointer)
func SwapUint32(addr *uint32, new uint32) (old uint32)
func SwapUint64(addr *uint64, new uint64) (old uint64)
func SwapUintptr(addr *uintptr, new uintptr) (old uintptr)
type Value
    func (v *Value) Load() (x interface{})
    func (v *Value) Store(x interface{})
```