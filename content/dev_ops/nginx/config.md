---
title: "nginx 配置"
date: 2020-01-18
draft: true
author: "Iven"
---

nginx 配置介绍
<!--more-->

## 进程
Nginx是支持单进程(master进程)提供服务的，那么为什么产品环境下要按照master-worker方式配置同时启动多个进程呢?这样做的好处主要有以下两点:  
由于master进程不会对用户请求提供服务，只用于管理真正提供服务的worker进程， 所以master进程可以是唯一的，它仅专注于自己的纯管理工作，为管理员提供命令行服务， 包括诸如启动服务、停止服务、重载配置文件、平滑升级程序等。master进程需要拥有较大 的权限，例如，通常会利用root用户启动master进程。worker进程的权限要小于或等于master 进程，这样master进程才可以完全地管理worker进程。当任意一个worker进程出现错误从而导 致coredump时，master进程会立刻启动新的worker进程继续服务。
Nginx worker进程可以同时处理的请求数只受限于内存大小，而且在架构设计上，不同的worker进程之间处理并发请求时几乎没有同步锁的限制，worker进程通常不会进入睡眠状态，因此，**当Nginx上的进程数与CPU核心数相等时(最好每一个worker进程都绑定特定的CPU核心)，进程间切换的代价是最小的**。如果产品中的服务器CPU核心数为8，那么就需要配置8个worker进程。

```text
user nobody;
worker_processes 8;
error_log varlog/nginx/error.log error; #pid logs/nginx.pid;
events {
    use epoll;
    worker_connections 50000; 
}
http {
    include mime.types;
    ...
}
```
块配置
```
events {
    ...
} 
http {
    upstream backend {
        server 127.0.0.1:8080;
    }
    gzip on; 
    server {
        ...
        location /webstatic { 
            gzip off;
        } 
    }
}
```


## 配置项

### 配置项单位
K或者k千字节(KiloByte，KB)  
M或者m兆字节(MegaByte，MB)  

```
gzip_buffers 4 8k; 
client_max_body_size 64M;
```
当指定时间时，可以使用的单位包括:  
ms(毫秒)，s(秒)，m(分钟)，h(小时)，d(天)，w(周，包含7天)， M(月，包含30天)，y(年，包含365天)
```
expires 10y; 
proxy_read_timeout 600; 
client_body_timeout 2m;
```

### 使用变量
```
log_format main '$remote_addr - $remote_user [$time_local] "$request" ' '$status $bytes_sent "$http_referer" '
'"$http_user_agent" "$http_x_forwarded_for"';
```

## 调试进程和定位问题的配置项

**是否以守护进程方式运行Nginx**  

```
语法: daemon on|off;  
默认: daemon on;  
```
守护进程(daemon)是脱离终端并且在后台运行的进程。它脱离终端是为了避免进程执 行过程中的信息在任何终端上显示，这样一来，进程也不会被任何终端所产生的信息所打 断。Nginx毫无疑问是一个需要以守护进程方式运行的服务，因此，默认都是以这种方式运 行的。不过Nginx还是提供了关闭守护进程的模式，之所以提供这种模式，是为了方便跟踪调 试Nginx，毕竟用gdb调试进程时最烦琐的就是如何继续跟进fork出的子进程了。

**是否以master/worker方式工作**
```
语法: master_process on|off;
默认: master_process on;
```

**error日志的设置**
```
语法: error_log pathfile level; 
默认: error_log logs/error.log error;
```

pathfile参数可以是一个具体的文件，例如，默认情况下是logs/error.log文件，最好将它 放到一个磁盘空间足够大的位置;pathfile也可以是/dev/null，这样就不会输出任何日志了， 这也是关闭error日志的唯一手段;pathfile也可以是stderr，这样日志会输出到标准错误文件 中。
level是日志的输出级别，取值范围是debug、info、notice、warn、error、crit、alert、 emerg，从左至右级别依次增大。当设定为一个级别时，大于或等于该级别的日志都会被输 出到pathfile文件中，小于该级别的日志则不会输出。例如，当设定为error级别时，error、 crit、alert、emerg级别的日志都会输出。
如果设定的日志级别是debug，则会输出所有的日志，这样数据量会很大，需要预先确 保pathfile所在磁盘有足够的磁盘空间。

**是否处理几个特殊的调试点**
```
语法: debug_points[stop|abort]
```
这个配置项也是用来帮助用户跟踪调试Nginx的。它接受两个参数:stop和abort。Nginx 在一些关键的错误逻辑中(Nginx 1.0.14版本中有8处)设置了调试点。如果设置了 debug_points为stop，那么Nginx的代码执行到这些调试点时就会发出SIGSTOP信号以用于调 试。如果debug_points设置为abort，则会产生一个coredump文件，可以使用gdb来查看Nginx当 时的各种信息。通常不会使用这个配置项。

**仅对指定的客户端输出debug级别的日志**
```
语法: debug_connection[IP|CIDR]
```
这个配置项实际上属于事件类配置，因此，它必须放在events{...}中才有效。它的值可以是IP地址或CIDR地址
```
events {
    debug_connection 10.224.66.14; 
    debug_connection 10.224.57.0/24;
}
```
这样，仅仅来自以上IP地址的请求才会输出debug级别的日志，其他请求仍然沿用 error_log中配置的日志级别。
> 使用debug_connection前，需确保在执行configure时已经加入了--with-debug参
数，否则不会生效。


**限制coredump核心转储文件的大小**
```
语法: worker_rlimit_core size;
```
在Linux系统中，当进程发生错误或收到信号而终止时，系统会将进程执行时的内存内 容(核心映像)写入一个文件(core文件)，以作为调试之用，这就是所谓的核心转储 (core dumps)。当Nginx进程出现一些非法操作(如内存越界)导致进程直接被操作系统强 制结束时，会生成核心转储core文件，可以从core文件获取当时的堆栈、寄存器等信息，从 而帮助我们定位问题。但这种core文件中的许多信息不一定是用户需要的，如果不加以限 制，那么可能一个core文件会达到几GB，这样随便coredumps几次就会把磁盘占满，引发严 重问题。通过worker_rlimit_core配置可以限制core文件的大小，从而有效帮助用户定位问 题。

**指定coredump文件生成目录**
```
语法: working_directory path;
```
worker进程的工作目录。这个配置项的唯一用途就是设置coredump文件所放置的目录， 协助定位问题。因此，需确保worker进程有权限向working_directory指定的目录中写入文 件。

## 正常运行的配置项

**定义环境变量**
```
语法: env VAR|VAR=VALUE
```
这个配置项可以让用户直接设置操作系统上的环境变量。例如
```
env TESTPATH=/tmp/;
```

**嵌入其他配置文件**
```
语法: includepathfile;
```
include配置项可以将其他配置文件嵌入到当前的nginx.conf文件中，它的参数既可以是绝 对路径，也可以是相对路径(相对于Nginx的配置目录，即nginx.conf所在的目录)，例如:
```
include mime.types; 
include vhost/*.conf;
```

**pid文件的路径**

```
语法: pid path/file;
默认: pid logs/nginx.pid;
```
保存master进程ID的pid文件存放路径。默认与configure执行时的参数“--pid-path”所指定 的路径是相同的，也可以随时修改，但应确保Nginx有权在相应的目标中创建pid文件，该文 件直接影响Nginx是否可以运行。

**Nginx worker进程运行的用户及用户组**
```
语法: user username[groupname];
默认: user nobody nobody;
```
user用于设置master进程启动后，fork出的worker进程运行在哪个用户和用户组下。当按 照“user username;”设置时，用户组名与用户名相同。  
若用户在configure命令执行时使用了参数--user=username和--group=groupname，此时 nginx.conf将使用参数中指定的用户和用户组。

**指定Nginx worker进程可以打开的最大句柄描述符个数**
```
语法: worker_rlimit_nofile limit;
```
设置一个worker进程可以打开的最大文件句柄数。

**限制信号队列**
```
语法: worker_rlimit_sigpending limit;
```
设置每个用户发往Nginx的信号队列的大小。也就是说，当某个用户的信号队列满了，
这个用户再发送的信号量会被丢掉。

## 优化性能的配置项

**Nginx worker进程个数**
```
语法: worker_processes number;
默认: worker_processes 1;
```
在master/worker运行方式下，定义worker进程的个数。
worker进程的数量会直接影响性能。那么，用户配置多少个worker进程才好呢?这实际 上与业务需求有关。
每个worker进程都是单线程的进程，它们会调用各个模块以实现多种多样的功能。如果 这些模块确认不会出现阻塞式的调用，那么，有多少CPU内核就应该配置多少个进程;反 之，如果有可能出现阻塞式调用，那么需要配置稍多一些的worker进程。


**绑定Nginx worker进程到指定的CPU内核**
```
语法: worker_cpu_affinity cpumask[cpumask...]
```
为什么要绑定worker进程到指定的CPU内核呢?假定每一个worker进程都是非常繁忙
的，如果多个worker进程都在抢同一个CPU，那么这就会出现同步问题。反之，如果每一个 worker进程都独享一个CPU，就在内核的调度策略上实现了完全的并发。

例如，如果有4颗CPU内核，就可以进行如下配置:
```
worker_processes 4;
worker_cpu_affinity 1000 0100 0010 0001;
```

**SSL硬件加速**
```
语法: ssl_engine device;
```
如果服务器上有SSL硬件加速设备，那么就可以进行配置以加快SSL协议的处理速度。 用户可以使用OpenSSL提供的命令来查看是否有SSL硬件加速设备:
```
openssl engine -t
```

**系统调用gettimeofday的执行频率**

```
语法: timer_resolution t;
```
默认情况下，每次内核的事件调用(如epoll、select、poll、kqueue等)返回时，都会执 行一次gettimeofday，实现用内核的时钟来更新Nginx中的缓存时钟。在早期的Linux内核中， gettimeofday的执行代价不小，因为中间有一次内核态到用户态的内存复制。当需要降低 gettimeofday的调用频率时，可以使用timer_resolution配置。例如，“timer_resolution 100ms;”表示至少每100ms才调用一次gettimeofday。但在目前的大多数内核中，如x86-64体系架构，gettimeofday只是一次vsyscall，仅仅对共享内存页中的数据做访问，并不是通常的系统调用，代价并不大，一般不必使用这个配置。 而且，如果希望日志文件中每行打印的时间更准确，也可以使用它。

**Nginx worker进程优先级设置**
```
语法: worker_priority nice;
默认: worker_priority 0;
```
该配置项用于设置Nginx worker进程的nice优先级。在Linux或其他类UNIX操作系统中，当许多进程都处于可执行状态时，将按照所有进程 的优先级来决定本次内核选择哪一个进程执行。进程所分配的CPU时间片大小也与进程优先 级相关，优先级越高，进程分配到的时间片也就越大(例如，在默认配置下，最小的时间片 只有5ms，最大的时间片则有800ms)。这样，优先级高的进程会占有更多的系统资源。优先级由静态优先级和内核根据进程执行情况所做的动态调整(目前只有±5的调整)共 同决定。nice值是进程的静态优先级，它的取值范围是–20~+19，–20是最高优先级，+19是 最低优先级。因此，如果用户希望Nginx占有更多的系统资源，那么可以把nice值配置得更小 一些，但不建议比内核进程的nice值(通常为–5)还要小。


## 事件类配置项

**是否打开accept锁**
```
语法: accept_mutex[on|off] 
默认: accept_mutext on;
```
accept_mutex是Nginx的负载均衡锁，本书会在第9章事件处理框架中详述Nginx是如何实 现负载均衡的。这里，读者仅需要知道accept_mutex这把锁可以让多个worker进程轮流地、 序列化地与新的客户端建立TCP连接。当某一个worker进程建立的连接数量达到 worker_connections配置的最大连接数的7/8时，会大大地减小该worker进程试图建立新TCP连 接的机会，以此实现所有worker进程之上处理的客户端请求数尽量接近。
accept锁默认是打开的，如果关闭它，那么建立TCP连接的耗时会更短，但worker进程 之间的负载会非常不均衡，因此不建议关闭它。

**lock文件的路径**
```
语法: lock_file path/file; 
默认: lock_file logs/nginx.lock;
```
accept锁可能需要这个lock文件，如果accept锁关闭，lock_file配置完全不生效。如果打 开了accept锁，并且由于编译程序、操作系统架构等因素导致Nginx不支持原子锁，这时才会 用文件锁实现accept锁(14.8.1节将会介绍文件锁的用法)，这样lock_file指定的lock文件才 会生效。

**使用accept锁后到真正建立连接之间的延迟时间**
```
语法: accept_mutex_delay Nms;
默认: accept_mutex_delay 500ms;
```
在使用accept锁后，同一时间只有一个worker进程能够取到accept锁。这个accept锁不是 阻塞锁，如果取不到会立刻返回。如果有一个worker进程试图取accept锁而没有取到，它至 少要等accept_mutex_delay定义的时间间隔后才能再次试图取锁。

**批量建立新连接**
```
语法: multi_accept[on|off];
默认: multi_accept off;
```
当事件模型通知有新连接时，尽可能地对本次调度中客户端发起的所有TCP请求都建立连接。

**选择事件模型**
```
语法: use[kqueue|rtsig|epoll|/dev/poll|select|poll|eventport];
默认: Nginx会自动使用最适合的事件模型。
```
对于Linux操作系统来说，可供选择的事件驱动模型有poll、select、epoll三种。epoll当 然是性能最高的一种。

**每个worker的最大连接数**
```
语法: worker_connections number;
```
定义每个worker进程可以同时处理的最大连接数。
