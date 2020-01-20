---
title: "Golang http"
date: 2020-01-18
draft: false
author: "Iven"
---

golang http包
<!--more-->

```golang

// 传入不和规范的header的key，返回符合规范的key。比如传入’accept-encoding‘，’Accept-Encoding‘
func CanonicalHeaderKey(s string) string
// 检测传入body的内容是什么类型，最多检测传入的512bytes，如果检测不到返回application/octet-stream
func DetectContentType(data []byte) string
func Error(w ResponseWriter, error string, code int)
// MaxBytesReader与io.LimitReader相似，但旨在限制传入请求主体的大小。与io.LimitReader相比，MaxBytesReader的结果为ReadCloser，对于超出限制的Read返回非EOF错误，并在调用其Close方法时关闭基础阅读器。
// MaxBytesReader可以防止客户端意外或恶意发送大请求并浪费服务器资源。
func MaxBytesReader(w ResponseWriter, r io.ReadCloser, n int64) io.ReadCloser
// 解析时间，内部执行的是time.Parse
func ParseTime(text string) (t time.Time, err error)
func ProxyFromEnvironment(req *Request) (*url.URL, error)
func ProxyURL(fixedURL *url.URL) func(*Request) (*url.URL, error)
func Redirect(w ResponseWriter, r *Request, url string, code int)
func Serve(l net.Listener, handler Handler) error
func ServeContent(w ResponseWriter, req *Request, name string, modtime time.Time, content io.ReadSeeker)
func ServeFile(w ResponseWriter, r *Request, name string)
func ServeTLS(l net.Listener, handler Handler, certFile, keyFile string) error
func SetCookie(w ResponseWriter, cookie *Cookie)
func StatusText(code int) string
type Client
    func (c *Client) CloseIdleConnections()
    func (c *Client) Do(req *Request) (*Response, error)
    func (c *Client) Get(url string) (resp *Response, err error)
    func (c *Client) Head(url string) (resp *Response, err error)
    func (c *Client) Post(url, contentType string, body io.Reader) (resp *Response, err error)
    func (c *Client) PostForm(url string, data url.Values) (resp *Response, err error)
type CloseNotifier
type ConnState
    func (c ConnState) String() string
type Cookie
    func (c *Cookie) String() string
type CookieJar
type Dir
    func (d Dir) Open(name string) (File, error)
type File
type FileSystem
type Flusher
type Handler
    func FileServer(root FileSystem) Handler
    func NotFoundHandler() Handler
    func RedirectHandler(url string, code int) Handler
    func StripPrefix(prefix string, h Handler) Handler
    func TimeoutHandler(h Handler, dt time.Duration, msg string) Handler
type HandlerFunc
    func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request)
type Header
    func (h Header) Add(key, value string)
    func (h Header) Clone() Header
    func (h Header) Del(key string)
    func (h Header) Get(key string) string
    func (h Header) Set(key, value string)
    func (h Header) Write(w io.Writer) error
    func (h Header) WriteSubset(w io.Writer, exclude map[string]bool) error
type Hijacker
type ProtocolError
    func (pe *ProtocolError) Error() string
type PushOptions
type Pusher
type Request
    func NewRequest(method, url string, body io.Reader) (*Request, error)
    func NewRequestWithContext(ctx context.Context, method, url string, body io.Reader) (*Request, error)
    func ReadRequest(b *bufio.Reader) (*Request, error)
    func (r *Request) AddCookie(c *Cookie)
    func (r *Request) BasicAuth() (username, password string, ok bool)
    func (r *Request) Clone(ctx context.Context) *Request
    func (r *Request) Context() context.Context
    func (r *Request) Cookie(name string) (*Cookie, error)
    func (r *Request) Cookies() []*Cookie
    func (r *Request) FormFile(key string) (multipart.File, *multipart.FileHeader, error)
    func (r *Request) FormValue(key string) string
    func (r *Request) MultipartReader() (*multipart.Reader, error)
    func (r *Request) ParseForm() error
    func (r *Request) ParseMultipartForm(maxMemory int64) error
    func (r *Request) PostFormValue(key string) string
    func (r *Request) ProtoAtLeast(major, minor int) bool
    func (r *Request) Referer() string
    func (r *Request) SetBasicAuth(username, password string)
    func (r *Request) UserAgent() string
    func (r *Request) WithContext(ctx context.Context) *Request
    func (r *Request) Write(w io.Writer) error
    func (r *Request) WriteProxy(w io.Writer) error
type Response
    func Get(url string) (resp *Response, err error)
    func Head(url string) (resp *Response, err error)
    func Post(url, contentType string, body io.Reader) (resp *Response, err error)
    func PostForm(url string, data url.Values) (resp *Response, err error)
    func ReadResponse(r *bufio.Reader, req *Request) (*Response, error)
    func (r *Response) Cookies() []*Cookie
    func (r *Response) Location() (*url.URL, error)
    func (r *Response) ProtoAtLeast(major, minor int) bool
    func (r *Response) Write(w io.Writer) error
type ResponseWriter
type RoundTripper
    func NewFileTransport(fs FileSystem) RoundTripper
type SameSite
type ServeMux
    func NewServeMux() *ServeMux
    func (mux *ServeMux) Handle(pattern string, handler Handler)
    func (mux *ServeMux) HandleFunc(pattern string, handler func(ResponseWriter, *Request))
    func (mux *ServeMux) Handler(r *Request) (h Handler, pattern string)
    func (mux *ServeMux) ServeHTTP(w ResponseWriter, r *Request)
type Server
    func (srv *Server) Close() error
    func (srv *Server) ListenAndServe() error
    func (srv *Server) ListenAndServeTLS(certFile, keyFile string) error
    func (srv *Server) RegisterOnShutdown(f func())
    func (srv *Server) Serve(l net.Listener) error
    func (srv *Server) ServeTLS(l net.Listener, certFile, keyFile string) error
    func (srv *Server) SetKeepAlivesEnabled(v bool)
    func (srv *Server) Shutdown(ctx context.Context) error
type Transport
    func (t *Transport) CancelRequest(req *Request)
    func (t *Transport) Clone() *Transport
    func (t *Transport) CloseIdleConnections()
    func (t *Transport) RegisterProtocol(scheme string, rt RoundTripper)
    func (t *Transport) RoundTrip(req *Request) (*Response, error)

func Handle(pattern string, handler Handler)
func HandleFunc(pattern string, handler func(ResponseWriter, *Request))
func ListenAndServe(addr string, handler Handler) error
func ListenAndServeTLS(addr, certFile, keyFile string, handler Handler) error
func NotFound(w ResponseWriter, r *Request)
func ParseHTTPVersion(vers string) (major, minor int, ok bool)
```