---
title: "Golang html/template"
date: 2020-02-10
draft: true
author: "Iven"
---

html template模板介绍
<!--more-->

索引
```golang
func HTMLEscape(w io.Writer, b []byte)
func HTMLEscapeString(s string) string
func HTMLEscaper(args ...interface{}) string
func IsTrue(val interface{}) (truth, ok bool)
func JSEscape(w io.Writer, b []byte)
func JSEscapeString(s string) string
func JSEscaper(args ...interface{}) string
func URLQueryEscaper(args ...interface{}) string
type CSS
type Error
    func (e *Error) Error() string
type ErrorCode
type FuncMap
type HTML
type HTMLAttr
type JS
type JSStr
type Srcset
type Template
    func Must(t *Template, err error) *Template
    func New(name string) *Template
    func ParseFiles(filenames ...string) (*Template, error)
    func ParseGlob(pattern string) (*Template, error)
    func (t *Template) AddParseTree(name string, tree *parse.Tree) (*Template, error)
    func (t *Template) Clone() (*Template, error)
    func (t *Template) DefinedTemplates() string
    func (t *Template) Delims(left, right string) *Template
    func (t *Template) Execute(wr io.Writer, data interface{}) error
    func (t *Template) ExecuteTemplate(wr io.Writer, name string, data interface{}) error
    func (t *Template) Funcs(funcMap FuncMap) *Template
    func (t *Template) Lookup(name string) *Template
    func (t *Template) Name() string
    func (t *Template) New(name string) *Template
    func (t *Template) Option(opt ...string) *Template
    func (t *Template) Parse(text string) (*Template, error)
    func (t *Template) ParseFiles(filenames ...string) (*Template, error)
    func (t *Template) ParseGlob(pattern string) (*Template, error)
    func (t *Template) Templates() []*Template
```

## 模板语法

### 变量
传给模板的变量可以通过`.`来访问。
```
{{.}}
{{.User.Name}}
```
同时也可以定义变量，`$name := .User.Name`这样可以在模板中引用这个变量
```
{{ $name := .User.Name }}
hist name is {{ $name }}
```

### 判断循环

#### if/else语句
```
{{ if .User.Name}}
his name is {{.User.Name}}
{{ else if .User.Alias}}
his name is {{ .User.Alias }}
{{ else }}
他木得名字
{{end}}
```

#### 循环判断
```
{{range .Users}} 
他的名字是 {{.Name}}
 {{else}} 
 T0 
 {{end}}
```
> else 部分是指没有.Users时执行的部分


### 比较
```
eq arg1 == arg2
ne arg1 != arg2
lt arg1 < arg2
le arg1 <= arg2
gt arg1 > arg2
ge arg1 >= arg2
```
使用方式
```
{{if  eq .A1 .A2 }}
{{ end }}
```
> 如果A1和A2相等则执行if的语句块


### 管道
管道类似于linux的管道。例如：
```
{{"\"output\""}}
	A string constant.
{{`"output"`}}
	A raw string constant.
{{printf "%q" "output"}}
	A function call.
{{"output" | printf "%q"}}
	A function call whose final argument comes from the previous
	command.
{{printf "%q" (print "out" "put")}}
	A parenthesized argument.
{{"put" | printf "%s%s" "out" | printf "%q"}}
	A more elaborate call.
{{"output" | printf "%s" | printf "%q"}}
	A longer chain.
{{with "output"}}{{printf "%q" .}}{{end}}
	A with action using dot.
{{with $x := "output" | printf "%q"}}{{$x}}{{end}}
	A with action that creates and uses a variable.
{{with $x := "output"}}{{printf "%q" $x}}{{end}}
	A with action that uses the variable in another action.
{{with $x := "output"}}{{$x | printf "%q"}}{{end}}
	The same, but pipelined.
```



### 模板函数
### 内部函数
**and**方法返回第一个为空的参数或最后一个参数。可以有任意多个参数。
```
{{ and x y }} 
```
**or**  返回第一个不为空的参数或最后一个参数。可以有任意多个参数。
```
{{ or x y}}
```
**call** 返回方法的第一个返回参数
```
{{ call .X.Y 1 2}}
```
>返回.X.Y方法的第一个返回参数

**html** 返回转义的html内容
```
{{ html .Content}}
```
**index** 获取索引位置
```
{{ index x 1 2 3}}
```
>获取x[1][2][3]  

**slice** 获取切片， `slice x 1 2`表示 `x[1:2]`，`slice x`表示`x[:]`，`slice x 1`表示`x[1:]`

**not** 获取相反的结果 `if not .X` 表示如果X不存在

**print** 类似于fmt.Sprint

**printf** 类似于fmt.Sprintf

**println** 类似于fmt.Sprintln

**urlquery** url内容会被转义



### 嵌套模板和布局

#### template
可以把一部分模板单独定义，从而实现多出使用。模板定义：
```
{ {- define "footer"}}
<footer> 
	<p>Here is the footer</p>
</footer>
{ {end}}
```
使用方式
```
{ {template "footer" .AK}}
```
> .AK 是传入的变量

#### block
block与template实现的功能都是模板嵌套。template如果找不到模板会报错，而block找不到模板是使用默认的定义
```
{{block "list" .}}
{{"\n"}}
{{range .}}
{{println "-" .}}
{{end}}
{{end}}
```
> block块里面的定义就是默认的模板定义


## 解释

### 生成模板
生成模板可以通过字符串，文件，以及正则文件匹配
```golang
func ParseFiles(filenames ...string) (*Template, error)
func ParseGlob(pattern string) (*Template, error)
func (t *Template) Parse(text string) (*Template, error)
func (t *Template) ParseFiles(filenames ...string) (*Template, error)
func (t *Template) ParseGlob(pattern string) (*Template, error)
```
在使用`ParseFiles`、`ParseGlob` 函数 的时候，默认以文件的路径的最后一个部分作为模版名称， 比如文件a/foo的模版名称为foo，但是如果参数中不同的文件夹下有相同的文件名时，最后那个同名的模版文件会"覆盖"前面的重名文件模版，官方库的实现中不能保存重名的模版文件。`ParseFiles`返回的模板以第一个为主模板，如果要特别指定也可以用`Lookup`函数来寻找模板

### 例子
假设有三个文件: hello.html、header.html、footer.html，其中hello.html是一个栏目的统一布局。模板生成代码:
```golang
var templates map[string]*template.Template
func init() {
	if templates == nil {
		templates = make(map[string]*template.Template)
	}
	//templatesDir := "./templates/"
  templatesDir := "./"
	layouts, err := filepath.Glob(templatesDir + "layouts/*.html")
	if err != nil {
		log.Fatal(err)
	}
	widgets, err := filepath.Glob(templatesDir + "widgets/*.html")
	if err != nil {
		log.Fatal(err)
	}
	for _, layout := range layouts {
		files := append(widgets, layout)
		templates[filepath.Base(layout)] = template.Must(template.ParseFiles(files...))
	}
}
```
模板渲染代码:
```golang
func renderTemplate(w http.ResponseWriter, name string, data interface{}) error {
	tmpl, ok := templates[name]
	if !ok {
		return fmt.Errorf("The template %s does not exist.", name)
	}
	w.Header().Set("Content-Type", "text/html; charset=utf-8")
	return tmpl.ExecuteTemplate(w, name, data)
}
```

---
参考：  

https://colobu.com/2019/11/05/Golang-Templates-Cheatsheet/  
https://golang.org/pkg/text/template/#example_Template  

