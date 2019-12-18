---
title: "Golang reflect包"
date: 2019-12-16
draft: false
author: "Iven"
---

golang 反射
<!--more-->

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
    // 获取type的第i个Method
    func (t Type) Method(i int) Method
    // 根据name获取type的方法，同时返回是否包含此方法的结果
    func (t Type) MethodByName(name string) (Method, bool)
    // 返回已经导出的方法的个数
    func (t Type) NumMethod() int
    // 返回类型名，不含包名
    func (t Type) Name() string 
    // 返回包名
    func (t Type) PkgPath() string 
     // 返回类型名字，实际就是 PkgPath() + Name()
    func (t Type) String() string
    // 返回 rtype.size 即类型大小，单位是字节数
    func (t Type) Size() uintptr
    // 返回type的类型
    func (t Type) Kind() Kind
     // 判断t是否实现了u的interface{}接口
    func (t Type) Implements(u Type) bool
    // 检查当前类型能不能赋值给接口 u
    func (t Type) AssignableTo(u Type) bool
    // 检查当前类型能不能转换成接口 u 类型
    func (t Type) ConvertibleTo(u Type) bool
    // 判断这个类型的值是否可以比较
    func (t Type) Comparable() bool
    // 返回类型的字节数，如果type不是 unsized Int, Uint, Float, or Complex这些类型
    func (t Type) Bits() bool 
    // t类型必须是Channel，返回channel的方向
    func (t Type) ChanDir() 
    // 返回函数类型的最后一个参数是不是可变数量的，"..." 就这样的，同样，如果不是函数类型，会 panic
    func (t Type) IsVariadic() bool
    // 返回所包含元素的类型，只有 Array, Chan, Map, Ptr, Slice 这些才能调，其他类型会 panic。
    func (t Type) Elem() Type
     // 返回 struct 类型的第 i 个字段，不是 struct 会 panic，i 越界也会 panic
    func (t Type) Field(i int) StructField 
      // 跟上边一样，不过是嵌套调用的，比如 [1, 2] 就是说返回当前 struct 的第1个struct 的第2个字段，适用于 struct 本身嵌套的类型
    func (t Type) FieldByIndex(index []int) StructField
     // 按名字找 struct 字段，第二个返回值 ok 表示有没有
    func (t Type) FieldByName(name string) (StructField, bool)
    // 按函数名找 struct 字段，因为 struct 里也可能有类型是 func
    func (t Type) FieldByNameFunc(match func(string) bool) (StructField, bool)
     // 返回函数第 i 个参数的类型，不是 func 会 panic
    func (t Type) In(i int) Type
     // 返回 map 的 key 的类型，不是 map 会 panic
    func (t Type) Key() Type
    // 返回 array 的长度，不是 array 会 panic
    func (t Type) Len() int 
     // 返回 struct 字段数量，不是 struct 会 panic
    func (t Type) NumField() int 
     // 返回函数的参数数量，不是 func 会 panic
    func (t Type) NumIn() int 
      // 返回函数的返回值数量，不是 func 会 panic
    func (t Type) NumOut() int 
    // 返回函数第 i 个返回值的类型，不是 func 会 panic
    func (t Type) Out(i int) Type
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
    // 前提 v 是一个 func，然后调用 v，并传入 in 参数，第一个参数是 in[0]，第二个是 in[1]，以此类推
    func (v Value) Call(in []Value) []Value
    func (v Value) CallSlice(in []Value) []Value
    func (v Value) CanAddr() bool
    func (v Value) CanInterface() bool
    func (v Value) CanSet() bool
    func (v Value) Cap() int
    func (v Value) Close()
    func (v Value) Complex() complex128
    func (v Value) Convert(t Type) Value
    // 返回 v 的接口值或者指针,比如 是&user{}会
    func (v Value) Elem() Value
    // 前提 v 是一个 struct，返回第 i 个字段，这个主要用于遍历
    func (v Value) Field(i int) Value
    
    func (v Value) FieldByIndex(index []int) Value
    // 前提 v 是一个 struct，根据字段名直接定位返回
    func (v Value) FieldByName(name string) Value
    func (v Value) FieldByNameFunc(match func(string) bool) Value
    func (v Value) Float() float64
    // 前提 v 是 Array, Slice, String 之一，返回第 i 个元素，主要也是用于遍历，注意不能越界
    func (v Value) Index(i int) Value
    func (v Value) Int() int64
    func (v Value) Interface() (i interface{})
    func (v Value) InterfaceData() [2]uintptr
    // 判断 v 是不是 nil，只有 chan, func, interface, map, pointer, slice 可以用，其他类型会 panic
    func (v Value) IsNil() bool
    // 判断 v 是否合法，如果返回 false，那么除了 String() 以外的其他方法调用都会 panic，事前检查是必要的
    func (v Value) IsValid() bool
    func (v Value) Kind() Kind
    func (v Value) Len() int
    // 前提 v 是个 map，返回对应 value
    func (v Value) MapIndex(key Value) Value
    // 前提 v 是个 map，返回所有 key 组成的一个 slice
    func (v Value) MapKeys() []Value

    func (v Value) MapRange() *MapIter
    func (v Value) Method(i int) Value
    func (v Value) MethodByName(name string) Value
    // 前提 v 是个 struct，返回字段个数
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

## 案例

{{< expand "动态调用方法" "..." >}}
```golang
package main

import (
	"fmt"
	"reflect"
)

// I - i
type I struct{}

// GetName - get name
func (i I) GetName(name string) string {
	return name
}

func main() {
	i := I{}
	name := "hello world"
	value := reflect.ValueOf(i)
	nameValue := reflect.ValueOf(name)
	returnValue := value.MethodByName("GetName").Call([]reflect.Value{nameValue})
	fmt.Println("GetName:", returnValue[0].Interface())
}
```
{{< /expand >}}


{{< expand "解析tag" "..." >}}
```golang
package main

import (
	"fmt"
	"reflect"
)

// I - i
type I struct {
	b int `test:"tag1,tag2"`
}

// GetName - get name
func (i I) GetName(name string) string {
	return name
}

func main() {
	i := I{}

	value := reflect.TypeOf(i)
	num := value.NumField()
	for i := 0; i < num; i++ {
		field := value.Field(i)
		test := field.Tag.Get("test")
		fmt.Println("test tag:", test) //tag1,tag2
	}
}
```
{{< /expand >}}


{{< expand "struct设置值" "..." >}}
```golang
package main

import (
	"encoding/json"
	"fmt"
	"reflect"
)

type user struct {
	Name string
	Age  string
	Pets []string
	Home *home
}
type home struct {
	Address string
}

func main() {

	h := home{
		Address: "aaa",
	}
	data, _ := json.Marshal(&h)
	u := user{}
	v := reflect.ValueOf(&u)
	elem := v.Elem()
	num := elem.NumField()
	for i := 0; i < num; i++ {
		f := elem.Field(i)
		if f.Kind() == reflect.String && f.CanSet() {
			f.SetString("helllo")
		}
		if f.Kind() == reflect.Ptr && f.IsNil() {
			fv := reflect.New(f.Type().Elem())
			f.Set(fv)
			json.Unmarshal(data, f.Interface())
		}
		if f.Kind() == reflect.Slice && f.IsNil() {
			// fv := reflect.New(f.Type())
			// fmt.Println("SLICE elem:", f.Type().Elem())
			sliceTypeElem := f.Type().Elem()
			fv := reflect.MakeSlice(reflect.SliceOf(sliceTypeElem), 0, 0)
			fv = reflect.Append(fv, reflect.ValueOf("hello world"))
			f.Set(fv)
		}
	}
	fmt.Println("USER:", u)
	fmt.Println("USER->home:", u.Home)
}
```
{{< /expand >}}

{{< expand "判断实例是否实现了某接口" "..." >}}
```golang
type IT interface {
    test1()
}

type T struct {
    A string
}

func (t *T) test1() {}

func main() {
    t := &T{}
    ITF := reflect.TypeOf((*IT)(nil)).Elem()
    tv := reflect.TypeOf(t)
    fmt.Println(tv.Implements(ITF))
}
```
{{< /expand >}}