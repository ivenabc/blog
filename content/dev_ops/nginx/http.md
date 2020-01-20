---
title: "nginx http"
date: 2020-01-18
draft: true
author: "Iven"
---

http 模块配置
<!--more-->

ngx_http_core_module模块配置里包括http块、server块、location块、upstream块

## 配置块server

### 监听端口

```
listen 127.0.0.1:8000; 
listen 127.0.0.1; #注意:不加端口时，默认监听80端口
```

如果服务器使用IPv6地址，那么可以这样使用:
```
listen [::]:8000;
listen [fe80::1];
listen [:::a8c9:1234]:80;

listen 443 default_server ssl;
listen 127.0.0.1 default_server accept_filter=dataready backlog=1024;

listen 443 default_server ssl;
listen 127.0.0.1 default_server accept_filter=dataready backlog=1024;
```
listen各个参数意义：

- default_server: 将所在的server块作为整个Web服务的默认server块。如果没有设置这个参数， 那么将会以在nginx.conf中找到的第一个server块作为默认server块。
- backlog=num:表示TCP中backlog队列的大小。默认为–1，表示不予设置。在TCP建 立三次握手过程中，进程还没有开始处理监听句柄，这时backlog队列将会放置这些新连接。 可如果backlog队列已满，还有新的客户端试图通过三次握手建立TCP连接，这时客户端将会 建立连接失败。
- rcvbuf=size:设置监听句柄的SO_RCVBUF参数。 
- sndbuf=size:设置监听句柄的SO_SNDBUF参数。 
- accept_filter:设置accept过滤器，只对FreeBSD操作系统有用。
- deferred:在设置该参数后，若用户发起建立连接请求，并且完成了TCP的三次握手， 内核也不会为了这次的连接调度worker进程来处理，只有用户真的发送请求数据时(内核已 经在网卡中收到请求数据包)，内核才会唤醒worker进程处理这个连接。这个参数适用于大 并发的情况下，它减轻了worker进程的负担。当请求数据来临时，worker进程才会开始处理 这个连接。只有确认上面所说的应用场景符合自己的业务需求时，才可以使用deferred配
置。
- bind:绑定当前端口/地址对，如127.0.0.1:8000。只有同时对一个端口监听多个地址时 才会生效。
- ssl:在当前监听的端口上建立的连接必须基于SSL协议。

### 主机名称

```
语法: server_name name[...]; 
默认: server_name "";
```

server_name后可以跟多个主机名称，如server_name www.testweb.com download.testweb.com;。
在开始处理一个HTTP请求时，Nginx会取出header头中的Host，与每个server中的 server_name进行匹配，以此决定到底由哪一个server块来处理这个请求。有可能一个Host与 多个server块中的server_name都匹配，这时就会根据匹配优先级来选择实际处理的server块。 server_name与Host的匹配优先级如下:  

1. 首先选择所有字符串完全匹配的server_name，如www.testweb.com 。
2. 其次选择通配符在前面的server_name，如*.testweb.com。 
3. 再次选择通配符在后面的server_name，如www.testweb.* 。 
4. 最后选择使用正则表达式才匹配的server_name，如~^\.testweb\.com$。


如果Host与所有的server_name都不匹配，这时将会按下列顺序选 择处理的server块。  
1)优先选择在listen配置项后加入[default|default_server]的server块。  
2)找到匹配listen端口的第一个server块。
如果server_name后跟着空字符串(如server_name"";)，那么表示匹配没有Host这个 HTTP头部的请求。

