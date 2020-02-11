---
title: "Golang http"
date: 2020-01-29
draft: true
author: "Iven"
---

### 概览
```golang
func CanonicalHeaderKey(s string) string
func DetectContentType(data []byte) string
func Error(w ResponseWriter, error string, code int)
func MaxBytesReader(w ResponseWriter, r io.ReadCloser, n int64) io.ReadCloser
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

### func CanonicalHeaderKey
```golang
func CanonicalHeaderKey(s string) string
```
CanonicalHeaderKey返回s的header规范格式。规范化将第一个字母和连字符后的任何字母转换为大写；比如 `accept-encoding`是 `Accept-Encoding`。如果包含空格或者无效的头部Key则原样返回。

### func DetectContentType
```golang
func DetectContentType(data []byte) string
```
通过检测body的内容确定上传内容的类型，最多检测512字节。如果检测不到更具体的一个。则默认是`application/octet-stream`。

### func Error
```golang
func Error(w ResponseWriter, error string, code int)
```

### func Handle
```golang
func Handle(pattern string, handler Handler)
```

### func HandleFunc
```golang
func HandleFunc(pattern string, handler func(ResponseWriter, *Request))
```

### func ListenAndServe
```golang
func ListenAndServe(addr string, handler Handler) error
```

### func ListenAndServeTLS
```golang
func ListenAndServeTLS(addr, certFile, keyFile string, handler Handler) error
```

### func MaxBytesReader
```golang
func MaxBytesReader(w ResponseWriter, r io.ReadCloser, n int64) io.ReadCloser
```

### func NotFound
```golang
func NotFound(w ResponseWriter, r *Request)
```

### func ParseHTTPVersion
```golang
func ParseHTTPVersion(vers string) (major, minor int, ok bool)
```

### func ParseTime
```golang
func ParseTime(text string) (t time.Time, err error)
```
ParseTime解析时间标头（例如Date：标头），尝试使用HTTP / 1.1允许的三种格式：TimeFormat，time.RFC850和time.ANSIC。

### func ProxyFromEnvironment
```golang
func ProxyFromEnvironment(req *Request) (*url.URL, error)
```
根据环境变量HTTP_PROXY，HTTPS_PROXY，NO_PROXY设置的值，可以把request转换成代理的请求，返回一个`url`和 `error`  
HTTPS_PROXY优于HTTP_PROXY。  
如果没有设置环境变量则返回`nil`的`url`和`nil`的`error`。
如果请求request的URL是"localhost"则也会返回`nil`的`url`和`nil`的`error`。

### func ProxyURL
```golang
func ProxyURL(fixedURL *url.URL) func(*Request) (*url.URL, error)
```
ProxyURL返回始终返回相同URL的代理功能（用于传输）。

### func Redirect
```golang
func Redirect(w ResponseWriter, r *Request, url string, code int)
```
### func Serve
```golang
func Serve(l net.Listener, handler Handler) error
```
### func ServeContent
```golang
func ServeContent(w ResponseWriter, req *Request, name string, modtime time.Time, content io.ReadSeeker)
```
pass

### func ServeFile
```golang
func ServeFile(w ResponseWriter, r *Request, name string)
```
### func ServeTLS
```golang
func ServeTLS(l net.Listener, handler Handler, certFile, keyFile string) error
```

### func SetCookie
```golang
func SetCookie(w ResponseWriter, cookie *Cookie)
```
把 cookie输入到w的header中。不合法的cookie会被丢弃

### func StatusText
```golang
func StatusText(code int) string
```

### type Client
```golang
type Client struct {
    // Transport specifies the mechanism by which individual
    // HTTP requests are made.
    // If nil, DefaultTransport is used.
    Transport RoundTripper

    // CheckRedirect specifies the policy for handling redirects.
    // If CheckRedirect is not nil, the client calls it before
    // following an HTTP redirect. The arguments req and via are
    // the upcoming request and the requests made already, oldest
    // first. If CheckRedirect returns an error, the Client's Get
    // method returns both the previous Response (with its Body
    // closed) and CheckRedirect's error (wrapped in a url.Error)
    // instead of issuing the Request req.
    // As a special case, if CheckRedirect returns ErrUseLastResponse,
    // then the most recent response is returned with its body
    // unclosed, along with a nil error.
    //
    // If CheckRedirect is nil, the Client uses its default policy,
    // which is to stop after 10 consecutive requests.
    CheckRedirect func(req *Request, via []*Request) error

    // Jar specifies the cookie jar.
    //
    // The Jar is used to insert relevant cookies into every
    // outbound Request and is updated with the cookie values
    // of every inbound Response. The Jar is consulted for every
    // redirect that the Client follows.
    //
    // If Jar is nil, cookies are only sent if they are explicitly
    // set on the Request.
    Jar CookieJar

    // Timeout specifies a time limit for requests made by this
    // Client. The timeout includes connection time, any
    // redirects, and reading the response body. The timer remains
    // running after Get, Head, Post, or Do return and will
    // interrupt reading of the Response.Body.
    //
    // A Timeout of zero means no timeout.
    //
    // The Client cancels requests to the underlying Transport
    // as if the Request's Context ended.
    //
    // For compatibility, the Client will also use the deprecated
    // CancelRequest method on Transport if found. New
    // RoundTripper implementations should use the Request's Context
    // for cancellation instead of implementing CancelRequest.
    Timeout time.Duration // Go 1.3
}
```

#### func (*Client) CloseIdleConnections
```golang
func (c *Client) CloseIdleConnections()
```
#### func (*Client) Do
```golang
func (c *Client) Do(req *Request) (*Response, error)
```

#### func (*Client) Get
```golang
func (c *Client) Get(url string) (resp *Response, err error)
```
#### func (*Client) Head
```golang
func (c *Client) Head(url string) (resp *Response, err error)
```
#### func (*Client) Post
```golang
func (c *Client) Post(url, contentType string, body io.Reader) (resp *Response, err error)
```
#### func (*Client) PostForm
```golang
func (c *Client) PostForm(url string, data url.Values) (resp *Response, err error)
```

### type CloseNotifier
```golang
type CloseNotifier interface {
    CloseNotify() <-chan bool
}
```

### type ConnState
```golang
type ConnState int
const (
    StateNew ConnState = iota
    StateActive
    StateIdle
    StateHijacked
    StateClosed
)
func (c ConnState) String() string
```

### type Cookie
```golang
type Cookie struct {
    Name  string
    Value string

    Path       string    // optional
    Domain     string    // optional
    Expires    time.Time // optional
    RawExpires string    // for reading cookies only

    // MaxAge=0 means no 'Max-Age' attribute specified.
    // MaxAge<0 means delete cookie now, equivalently 'Max-Age: 0'
    // MaxAge>0 means Max-Age attribute present and given in seconds
    MaxAge   int
    Secure   bool
    HttpOnly bool
    SameSite SameSite // Go 1.11
    Raw      string
    Unparsed []string // Raw text of unparsed attribute-value pairs
}
```

#### func (*Cookie) String
```golang
func (c *Cookie) String() string
```

### type CookieJar
```golang
type CookieJar interface {
    SetCookies(u *url.URL, cookies []*Cookie)
    Cookies(u *url.URL) []*Cookie
}
```

### type Dir
```golang
type Dir string
func (d Dir) Open(name string) (File, error)
```
### type File 
```golang
type File interface {
    io.Closer
    io.Reader
    io.Seeker
    Readdir(count int) ([]os.FileInfo, error)
    Stat() (os.FileInfo, error)
}
```

### type FileSystem
```golang
type FileSystem interface {
    Open(name string) (File, error)
}
```

### type Flusher 
```golang
type Flusher interface {
    // Flush sends any buffered data to the client.
    Flush()
}
```

### type Handler
```golang
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```

### func FileServer
```golang
func FileServer(root FileSystem) Handler
```

#### func NotFoundHandler
```golang
func NotFoundHandler() Handler
```

#### func RedirectHandler
```golang
func RedirectHandler(url string, code int) Handler
```

#### func StripPrefix
```golang
func StripPrefix(prefix string, h Handler) Handler
```
#### func TimeoutHandler
```golang
func TimeoutHandler(h Handler, dt time.Duration, msg string) Handler
```

### type HandlerFunc
```golang
type HandlerFunc func(ResponseWriter, *Request)
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request)
```

### type Header 
```golang
type Header map[string][]string
```
#### func (Header) Add
```golang
func (h Header) Add(key, value string)
```
#### func (Header) Clone
```golang
func (h Header) Clone() Header
```

#### func (Header) Del 
```golang
func (h Header) Del(key string)
```

#### func (Header) Get
```golang
func (h Header) Get(key string) string
```

#### func (Header) Set
```golang
func (h Header) Set(key, value string)
```

#### func (Header) Write
```golang
func (h Header) Write(w io.Writer) error
```
#### func (Header) WriteSubset
```golang
func (h Header) WriteSubset(w io.Writer, exclude map[string]bool) error
```

### type Hijacker 
```golang
type Hijacker interface {
    Hijack() (net.Conn, *bufio.ReadWriter, error)
}
```

### type ProtocolError
```golang
type ProtocolError struct {
    ErrorString string
}
```
#### func (*ProtocolError) Error
```golang
func (pe *ProtocolError) Error() string
```

### type PushOptions
```golang
type PushOptions struct {
    Method string
    Header Header
}
```

### type Pusher 
```golang
type Pusher interface {
    Push(target string, opts *PushOptions) error
}
```

### type Request
```golang
type Request struct {
    Method string
    URL *url.URL

    Proto      string // "HTTP/1.0"
    ProtoMajor int    // 1
    ProtoMinor int    // 0

    Header Header
    Body io.ReadCloser

    GetBody func() (io.ReadCloser, error) // Go 1.8

    ContentLength int64

    TransferEncoding []string

    Close bool

    Host string
    Form url.Values

    PostForm url.Values // Go 1.1

    MultipartForm *multipart.Form

    Trailer Header

    RemoteAddr string

    RequestURI string

    TLS *tls.ConnectionState

    Cancel <-chan struct{} // Go 1.5

    Response *Response // Go 1.7
}
```

#### func NewRequest 
```golang
func NewRequest(method, url string, body io.Reader) (*Request, error)
```

#### func NewRequestWithContext
```golang
func NewRequestWithContext(ctx context.Context, method, url string, body io.Reader) (*Request, error)
```

#### func ReadRequest
```golang
func ReadRequest(b *bufio.Reader) (*Request, error)
```

#### func (*Request) AddCookie
```golang
func (r *Request) AddCookie(c *Cookie)
```

#### func (*Request) BasicAuth 
```golang
func (r *Request) BasicAuth() (username, password string, ok bool)
```

#### func (*Request) Clone
```golang
func (r *Request) Clone(ctx context.Context) *Request
```

#### func (*Request) Context
```golang
func (r *Request) Context() context.Context
```

#### func (*Request) Cookie
```golang
func (r *Request) Cookie(name string) (*Cookie, error)
```

#### func (*Request) Cookies 
```golang
func (r *Request) Cookies() []*Cookie
```

#### func (*Request) FormFile 
```golang
func (r *Request) FormFile(key string) (multipart.File, *multipart.FileHeader, error)
```

#### func (*Request) FormValue
```golang
func (r *Request) FormValue(key string) string
```

#### func (*Request) MultipartReader
```golang
func (r *Request) MultipartReader() (*multipart.Reader, error)
```

#### func (*Request) ParseForm 
```golang
func (r *Request) ParseForm() error
```

#### func (*Request) ParseMultipartForm
```golang
func (r *Request) ParseMultipartForm(maxMemory int64) error
```

#### func (*Request) PostFormValue
```golang
func (r *Request) PostFormValue(key string) string
```

#### func (*Request) ProtoAtLeast
```golang
func (r *Request) ProtoAtLeast(major, minor int) bool
```

#### func (*Request) Referer
```golang
func (r *Request) Referer() string
```

#### func (*Request) SetBasicAuth 
```golang
func (r *Request) SetBasicAuth(username, password string)
```

#### func (*Request) UserAgent
```golang
func (r *Request) UserAgent() string
```

#### func (*Request) WithContext 
```golang
func (r *Request) WithContext(ctx context.Context) *Request
```

#### func (*Request) Write
```golang
func (r *Request) Write(w io.Writer) error
func (*Request) WriteProxy
```

### type Response
```golang
type Response struct {
    Status     string // e.g. "200 OK"
    StatusCode int    // e.g. 200
    Proto      string // e.g. "HTTP/1.0"
    ProtoMajor int    // e.g. 1
    ProtoMinor int    // e.g. 0

    Header Header

    Body io.ReadCloser

    ContentLength int64

    TransferEncoding []string

    Close bool

    Uncompressed bool // Go 1.7

    Trailer Header

    Request *Request

    TLS *tls.ConnectionState // Go 1.3
}
```

### func Get 
```golang
func Get(url string) (resp *Response, err error)
```

### func Head
```golang
func Head(url string) (resp *Response, err error)
```

### func Post
```golang
func Post(url, contentType string, body io.Reader) (resp *Response, err error)
```

### func PostForm
```golang
func PostForm(url string, data url.Values) (resp *Response, err error)
```

### func ReadResponse
```golang
func ReadResponse(r *bufio.Reader, req *Request) (*Response, error)
```

### func (*Response) Cookies
```golang
func (r *Response) Cookies() []*Cookie
```
### func (*Response) Location
```golang
func (r *Response) Location() (*url.URL, error)
```

### func (*Response) ProtoAtLeast
```golang
func (r *Response) ProtoAtLeast(major, minor int) bool
```

### func (*Response) Write
```golang
func (r *Response) Write(w io.Writer) error
```

### type ResponseWriter
```golang
type ResponseWriter interface {
    Header() Header
    Write([]byte) (int, error)
    WriteHeader(statusCode int)
}
```

### type RoundTripper
```golang
type RoundTripper interface {
    RoundTrip(*Request) (*Response, error)
}
```

#### func NewFileTransport 
```golang
func NewFileTransport(fs FileSystem) RoundTripper
```

### type SameSite
```golang
type SameSite int
const (
    SameSiteDefaultMode SameSite = iota + 1
    SameSiteLaxMode
    SameSiteStrictMode
    SameSiteNoneMode
)
```

### type ServeMux 
```golang
type ServeMux struct {
    // contains filtered or unexported fields
}
func NewServeMux() *ServeMux
func (mux *ServeMux) Handle(pattern string, handler Handler)
func (mux *ServeMux) HandleFunc(pattern string, handler func(ResponseWriter, *Request))
func (mux *ServeMux) Handler(r *Request) (h Handler, pattern string)
func (mux *ServeMux) ServeHTTP(w ResponseWriter, r *Request)

```

### type Server
```golang
type Server struct {
    Addr    string  // TCP address to listen on, ":http" if empty
    Handler Handler // handler to invoke, http.DefaultServeMux if nil

    TLSConfig *tls.Config

    ReadTimeout time.Duration

    ReadHeaderTimeout time.Duration // Go 1.8

    WriteTimeout time.Duration

    IdleTimeout time.Duration // Go 1.8

    MaxHeaderBytes int

    TLSNextProto map[string]func(*Server, *tls.Conn, Handler) // Go 1.1

    ConnState func(net.Conn, ConnState) // Go 1.3

    ErrorLog *log.Logger // Go 1.3

    BaseContext func(net.Listener) context.Context // Go 1.13

    ConnContext func(ctx context.Context, c net.Conn) context.Context // Go 1.13
}
```

#### func (*Server) Close
```golang
func (srv *Server) Close() error
```
#### func (*Server) ListenAndServe 
```golang
func (srv *Server) ListenAndServe() error
```
#### func (*Server) ListenAndServeTLS 
```golang
func (srv *Server) ListenAndServeTLS(certFile, keyFile string) error
```
#### func (*Server) RegisterOnShutdown

```golang
func (srv *Server) RegisterOnShutdown(f func())
```

#### func (*Server) Serve 
```golang
func (srv *Server) Serve(l net.Listener) error
```
#### func (*Server) ServeTLS
```golang
func (srv *Server) ServeTLS(l net.Listener, certFile, keyFile string) error
```

#### func (*Server) SetKeepAlivesEnabled
```golang
func (srv *Server) SetKeepAlivesEnabled(v bool)
```

#### func (*Server) Shutdown
```golang
func (srv *Server) Shutdown(ctx context.Context) error
```

### type Transport 
```golang
type Transport struct {

    Proxy func(*Request) (*url.URL, error)

    DialContext func(ctx context.Context, network, addr string) (net.Conn, error) // Go 1.7

    Dial func(network, addr string) (net.Conn, error)

    DialTLS func(network, addr string) (net.Conn, error) // Go 1.4

    TLSClientConfig *tls.Config

    TLSHandshakeTimeout time.Duration // Go 1.3

    DisableKeepAlives bool

    DisableCompression bool

    MaxIdleConns int // Go 1.7

    MaxIdleConnsPerHost int

    MaxConnsPerHost int // Go 1.11

    IdleConnTimeout time.Duration // Go 1.7

    ResponseHeaderTimeout time.Duration // Go 1.1

    ExpectContinueTimeout time.Duration // Go 1.6

    TLSNextProto map[string]func(authority string, c *tls.Conn) RoundTripper // Go 1.6

    ProxyConnectHeader Header // Go 1.8

    MaxResponseHeaderBytes int64 // Go 1.7

    WriteBufferSize int // Go 1.13

    ReadBufferSize int // Go 1.13

    ForceAttemptHTTP2 bool // Go 1.13
}
```

#### func (*Transport) CancelRequest
```golang
func (t *Transport) CancelRequest(req *Request)
```

#### func (*Transport) Clone 
```golang
func (t *Transport) Clone() *Transport
```

#### func (*Transport) CloseIdleConnections
```golang
func (t *Transport) CloseIdleConnections()
```

#### func (*Transport) RegisterProtocol
```golang
func (t *Transport) RegisterProtocol(scheme string, rt RoundTripper)
```

#### func (*Transport) RoundTrip
```golang
func (t *Transport) RoundTrip(req *Request) (*Response, error)
```