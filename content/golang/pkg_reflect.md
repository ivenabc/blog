---
title: "Golang reflect包"
date: 2019-12-16
draft: false
author: "Iven"
---

```golang
// 浅拷贝，两个都是数组且类型一样。dst如果是[]uint8，src可以是字符串
func Copy(dst, src Value) int
// 比较两个interface{}类型和值是否一样
func DeepEqual(x, y interface{}) bool
// slice必须是切片否则会panic，
func Swapper(slice interface{}) func(i, j int)
// 有三种方式：RecvDir,SendDir,BothDir
type ChanDir
    func (d ChanDir) String() string
// 类型：数据类型
type Kind
    func (k Kind) String() string
// map的迭代器
type MapIter
    func (it *MapIter) Key() Value
    func (it *MapIter) Next() bool
    func (it *MapIter) Value() Value
type Method
type SelectCase
type SelectDir
type SliceHeader
type StringHeader
type StructField
type StructTag
    func (tag StructTag) Get(key string) string
    func (tag StructTag) Lookup(key string) (value string, ok bool)
type Type
    func ArrayOf(count int, elem Type) Type
    func ChanOf(dir ChanDir, t Type) Type
    func FuncOf(in, out []Type, variadic bool) Type
    func MapOf(key, elem Type) Type
    func PtrTo(t Type) Type
    func SliceOf(t Type) Type
    func StructOf(fields []StructField) Type
    func TypeOf(i interface{}) Type
type Value
    func Append(s Value, x ...Value) Value
    func AppendSlice(s, t Value) Value
    func Indirect(v Value) Value
    func MakeChan(typ Type, buffer int) Value
    func MakeFunc(typ Type, fn func(args []Value) (results []Value)) Value
    func MakeMap(typ Type) Value
    func MakeMapWithSize(typ Type, n int) Value
    func MakeSlice(typ Type, len, cap int) Value
    func New(typ Type) Value
    func NewAt(typ Type, p unsafe.Pointer) Value
    func Select(cases []SelectCase) (chosen int, recv Value, recvOK bool)
    func ValueOf(i interface{}) Value
    func Zero(typ Type) Value
    func (v Value) Addr() Value
    func (v Value) Bool() bool
    func (v Value) Bytes() []byte
    func (v Value) Call(in []Value) []Value
    func (v Value) CallSlice(in []Value) []Value
    func (v Value) CanAddr() bool
    func (v Value) CanInterface() bool
    func (v Value) CanSet() bool
    func (v Value) Cap() int
    func (v Value) Close()
    func (v Value) Complex() complex128
    func (v Value) Convert(t Type) Value
    func (v Value) Elem() Value
    func (v Value) Field(i int) Value
    func (v Value) FieldByIndex(index []int) Value
    func (v Value) FieldByName(name string) Value
    func (v Value) FieldByNameFunc(match func(string) bool) Value
    func (v Value) Float() float64
    func (v Value) Index(i int) Value
    func (v Value) Int() int64
    func (v Value) Interface() (i interface{})
    func (v Value) InterfaceData() [2]uintptr
    func (v Value) IsNil() bool
    func (v Value) IsValid() bool
    func (v Value) Kind() Kind
    func (v Value) Len() int
    func (v Value) MapIndex(key Value) Value
    func (v Value) MapKeys() []Value
    func (v Value) MapRange() *MapIter
    func (v Value) Method(i int) Value
    func (v Value) MethodByName(name string) Value
    func (v Value) NumField() int
    func (v Value) NumMethod() int
    func (v Value) OverflowComplex(x complex128) bool
    func (v Value) OverflowFloat(x float64) bool
    func (v Value) OverflowInt(x int64) bool
    func (v Value) OverflowUint(x uint64) bool
    func (v Value) Pointer() uintptr
    func (v Value) Recv() (x Value, ok bool)
    func (v Value) Send(x Value)
    func (v Value) Set(x Value)
    func (v Value) SetBool(x bool)
    func (v Value) SetBytes(x []byte)
    func (v Value) SetCap(n int)
    func (v Value) SetComplex(x complex128)
    func (v Value) SetFloat(x float64)
    func (v Value) SetInt(x int64)
    func (v Value) SetLen(n int)
    func (v Value) SetMapIndex(key, val Value)
    func (v Value) SetPointer(x unsafe.Pointer)
    func (v Value) SetString(x string)
    func (v Value) SetUint(x uint64)
    func (v Value) Slice(i, j int) Value
    func (v Value) Slice3(i, j, k int) Value
    func (v Value) String() string
    func (v Value) TryRecv() (x Value, ok bool)
    func (v Value) TrySend(x Value) bool
    func (v Value) Type() Type
    func (v Value) Uint() uint64
    func (v Value) UnsafeAddr() uintptr
type ValueError
    func (e *ValueError) Error() string

```