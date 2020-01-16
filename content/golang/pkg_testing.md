---
title: "Golang testing"
date: 2020-01-16
draft: false
author: "Iven"
---

go testing
<!--more-->

go test测试文件运行需要使用 `go test`命令，可以使用 `go help test` 和 `go help testflag`两个命令查看

## type B 
性能测试和标准测试的区别是总是会打印日志，  另外调用 FailNow，Fatal，Fatalf，SkipNow，Skip或Skipf中的任何方法时结束该测试方法。
```golang
type B struct {
    N int
    // contains filtered or unexported fields
}
// 等于执行 Log + Fail
func (c *B) Error(args ...interface{})
// 等于执行 Logf + Fail
func (c *B) Errorf(format string, args ...interface{})
// 标志测试失败，但是还会继续往下执行
func (c *B) Fail()
// FailNow将函数标记为失败，并通过调用runtime.Goexit来退出这个goroutine里的程序，其他的gorutine不受影响
func (c *B) FailNow()
// 报告是否是测试方法失败了
func (c *B) Failed() bool
// 等于 Log + FailNow
func (c *B) Fatal(args ...interface{})
// 等于 Logf + FailNow
func (c *B) Fatalf(format string, args ...interface{})
// 需要告诉测试套件这个方法是辅助函数（helper）。通过这样做，当测试失败时所报告的行号将在函数调用中而不是在辅助函数内部
func (c *B) Helper()
// 当命令加入-test.V 时候会输出这个日志
func (c *B) Log(args ...interface{})
// 当命令加入-test.V 时候会输出这个日志
func (c *B) Logf(format string, args ...interface{})
// 返回当前测试的名字
func (c *B) Name() string
// 用于打开当前基准测试的内存统计功能， 与 go test 使用 -benchmem 标志类似，但 ReportAllocs 只影响那些调用了该函数的基准测试。
func (b *B) ReportAllocs()
// 自定义汇报指标
func (b *B) ReportMetric(n float64, unit string)
// 重置时间指标
func (b *B) ResetTimer()
// 内部调用一次测试方法
func (b *B) Run(name string, f func(b *B)) bool
// 开启并行测试功能，最多并行数量和 GOMAXPROCS 有关，也可以通过 -cpu设置，
func (b *B) RunParallel(body func(*PB))
//SetBytes记录单个操作中处理的字节数。如果调用此方法，则基准测试将报告ns / op和MB / s。
func (b *B) SetBytes(n int64)
// RunParallel 默认是根据GOMAXPROCS的，但是可以通过SetParallelism来控制
func (b *B) SetParallelism(p int)
// Skip 等于 Log + SkipNow
func (c *B) Skip(args ...interface{})
// SkipNow将测试标记为已跳过，并通过调用runtime.Goexit停止执行。skipNow不会影响其他goroutine
func (c *B) SkipNow()
// 等于 Logf + SkipNow
func (c *B) Skipf(format string, args ...interface{})
// 返回测试是否skipped
func (c *B) Skipped() bool
// 标记当前运行的测试方法开始时间，然后调用StopTimer开始停止时间
func (b *B) StartTimer()
// 停止计时
func (b *B) StopTimer()
```

## BenchmarkResult
BenchmarkResult Bench统计结果

```golang
type BenchmarkResult struct {
    N         int           // 总共运行了多少次
    T         time.Duration // 花了多少时间
    Bytes     int64         // 每次花了多少字节
    MemAllocs uint64        // 总共分配了多少内存
    MemBytes  uint64        // 总共分配了多少字节
    Extra map[string]float64 // 1.13引出的额外导出数据
}
// 传入测试方法，返回测试结果
func Benchmark(f func(b *B)) BenchmarkResult
// 等于 r.MemBytes / r.N ，单次运行用了多少字节
func (r BenchmarkResult) AllocedBytesPerOp() int64
AllocedBytesPerOp returns the "B/op" metric, which is calculated as r.MemBytes / r.N.
// r.MemAllocs / r.N  单次分配了多少次内存
func (r BenchmarkResult) AllocsPerOp() int64
// 返回 r.AllocedBytesPerOp， r.AllocsPerOp的内容，内容格式与`go test`命令输出的格式一样
func (r BenchmarkResult) MemString() string
// 返回 ns/op 测量数据
func (r BenchmarkResult) NsPerOp() int64
// 返回BenchmarkResult 返回数据
func (r BenchmarkResult) String() string
```

## type Cover 
Cover记录有关测试覆盖率检查的信息。注意：此结构在测试基础结构内部，并且可能会更改。 Go 1兼容性准则尚未涵盖它。
```golang
type Cover struct {
    Mode            string
    Counters        map[string][]uint32
    Blocks          map[string][]CoverBlock
    CoveredPackages string
}
// CoverBlock记录单个基本块的coverage数据。
type CoverBlock1.2
type CoverBlock struct {
    Line0 uint32 // Line number for block start.
    Col0  uint16 // Column number for block start.
    Line1 uint32 // Line number for block end.
    Col1  uint16 // Column number for block end.
    Stmts uint16 // Number of statements included in this block.
}
type InternalBenchmark
An internal type but exported because it is cross-package; part of the implementation of the "go test" command.

type InternalBenchmark struct {
    Name string
    F    func(b *B)
}
type InternalExample
type InternalExample struct {
    Name      string
    F         func()
    Output    string
    Unordered bool // Go 1.7
}
type InternalTest
An internal type but exported because it is cross-package; part of the implementation of the "go test" command.

type InternalTest struct {
    Name string
    F    func(*T)
}
type M1.4
M is a type passed to a TestMain function to run the actual tests.

type M struct {
    // contains filtered or unexported fields
}
func MainStart(deps testDeps, tests []InternalTest, benchmarks []InternalBenchmark, examples []InternalExample) *M
func (m *M) Run() int

type PB struct {
}
func (pb *PB) Next() bool

type T struct {
}
func (c *T) Error(args ...interface{})
func (c *T) Errorf(format string, args ...interface{})
func (c *T) Fail()
func (c *T) FailNow()
func (c *T) Failed() bool
func (c *T) Fatal(args ...interface{})
func (c *T) Fatalf(format string, args ...interface{})
func (c *T) Helper()
func (c *T) Log(args ...interface{})
func (c *T) Logf(format string, args ...interface{})
func (c *T) Name() string
func (t *T) Parallel()
func (t *T) Run(name string, f func(t *T)) bool
func (c *T) Skip(args ...interface{})
func (c *T) SkipNow()
func (c *T) Skipf(format string, args ...interface{})
func (c *T) Skipped() bool
```
