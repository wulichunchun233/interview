# nginx

nginx是一种 web 服务器，应该是目前最常用的 web server。其主要有三大功能：

- 反向代理
- 负载均衡
- 静态资源服务器

nginx 的工作流程如下图所示，用户的请求首先发送到 nginx 上，然后 nginx 根据用户请求的详细信息来选择一台指定的 App Server 来完成响应。：

![nginx](/assets/images/linux/4.jpeg)

根据 nginx 的工作流程来介绍 nginx 的三个主要的功能：

- **反向代理** 是相对与 **正向代理（简称代理）** 而言的，代理是指使用基于 vpn 等技术实现通过一台指定的服务器来浏览公共的网络资源，此时公共的网络资源处理的是该服务器的请求而非用户的请求；而反向代理是指用户请求一个指定的网络资源，但该网络资源在根据用户的请求去寻找指定的一台服务器去处理，此时用户只是请求该公共资源而非请求指定的服务器。简而言之：**正向代理隐藏了真实的客户端；反向代理隐藏了真实的服务器**。

- **负载均衡** 是指对于 nginx 服务器来说，其管理了众多的 App Server。而有些访问量巨大的网站其实很多的 App Server 所执行的功能是一致的。这种情况下如何面向用户的请求来分配这多个 App Server 便是所谓的负载均衡。

- **静态资源服务器** 是指 nginx 可以将网站所需的静态资源放置在一台单独的服务器中，当有请求访问静态资源的时候便直接返回指定的文件而无需执行 App Server 中的相关逻辑。

接下来详细介绍 nginx 的相关知识点，主要分为安装和配置两部分。在日常使用中最常用的是根据实际需求进行 nginx 的配置工作，因此相关的配置内容会重点进行介绍。另外也会给出相应的源码分析的介绍，这里引用淘宝团队的内容。

## 安装

### 下载所需文件

在安装之前我们得下载所需的程序的源码，这里需要下载 nginx、pcre、zlib 以及 openssl。

- nginx下载地址：[http://nginx.org/en/download.html](http://nginx.org/en/download.html)
- pcre库下载地址（nginx需要，用于字符串的正则表达式解析）：[http://sourceforge.net/projects/pcre/files/pcre/](http://sourceforge.net/projects/pcre/files/pcre/)
- zlib下载地址（nginx需要，用于数据压缩与解压缩）：[http://www.zlib.net/](http://www.zlib.net/)
- openssl下载地址（nginx需要，用于进行 https 协议）：[https://github.com/openssl/openssl](https://github.com/openssl/openssl)

将这些文件下载到服务器中，并放置到一个文件夹下进行解压

### 安装前配置

之后进入 nginx 的目录中，输入如下的命令进行安装：

```shell
./configure \
 --prefix=/usr/local/nginx \ # 安装路径
 --with-http_ssl_module \ # 开启 https
 --with-http_flv_module \ # 开启对 FLV 文件的拖动播放
 --with-http_stub_status_module \ # 开启 nginx 状态监控
 --with-http_gzip_static_module \ # 开启数据压缩
 --with-pcre=../pcre-8.43 \ # 指定 pcre 的路径
 --with-zlib=../zlib-1.2.11 \ # 指定 zlib 的路径
 --with-
openssl=../openssl-master # 指定 openssl 的路径
```

上面的命令详细介绍了内容，下面的命令可以直接粘贴到服务器中运行：

```shell
./configure --prefix=/usr/local/nginx --with-ht
tp_ssl_module --with-http_flv_module --with-http_stub_status_module --with-http
_gzip_static_module --with-pcre=../pcre-8.43 --with-zlib=../zlib-1.2.11 --with-
openssl=../openssl-master
```

假如执行上面的命令出现如下的错误：

```
./configure: error: C compiler cc is not found
```

这是因为没有安装对应的 c 语言执行环境，执行下面的命令进行安装：

```shell
$ sudo apt install gcc
$ sudo apt install g++
```

之后再执行上面的命令进行配置

### 编译安装

输入如下的命令进行编译安装：

```shell
$ make && sudo make install
```

如果提示 make 不存在的话使用下面的命令进行安装：

```shell
$ sudo apt install make
```

安装完成之后 Nginx 会被安装在 /usr/local/nginx 目录下（也可以使用参数--prefix=指定自己需要的位置）， 安装成功后 /usr/local/nginx 目录下有四个子目录分别是：conf、html、logs、sbin 。 其中 Nginx 的配置文件存放于 conf/nginx.conf， bin 文件是位于 sbin 目录下的 nginx 文件。 确保系统的 80 端口没被其他程序占用，运行 sbin/nginx 命令来启动 Nginx，

```shell
$sudo /usr/local/nginx/sbin/nginx
```


查看当前的 nginx 是否启动成功；

```shell
sudo netstat -ano | grep 80
```

接下来打开浏览器访问此机器的 IP，如果浏览器出现 Welcome to nginx! 则表示 Nginx 已经安装并运行成功

### nginx 控制命令

- 检查配置文件是否正确
```shell
/usr/local/sbin/nginx -t 
```

- 可以看到编译选项

```shell
/usr/local/sbin/nginx -V
```

- 重启Nginx，nginx在重启时是属于平滑重启，不会对当前的业务产生影响。

```shell
sudo /usr/local/sbin/nginx -s reload
```

- 关闭Nginx

```shell
sudo /usr/local/sbin/nginx -s stop
```

- 优雅停止服务

```shell
sudo /usr/local/sbin/nginx -s quit
kill -s SIGQUIT pid_master
kill -s SIGWINCH pid_master
```

## 配置

打开 nginx.conf 配置文件，该文件主要分为如下几个部分：

```shell
main
events {
  ....
}
http {
  ....
  upstream myproject {
    .....
  }
  server {
    ....
    location {
        ....
    }
  }
  server {
    ....
    location {
        ....
    }
  }
  ....
}
```

nginx配置文件主要分为六个区域：

- main(全局设置)
- events(nginx工作模式)
- http(http设置)
- sever(主机设置)
- location(URL匹配)
- upstream(负载均衡服务器设置)

下面依次来看下具体内容

### main 模块

下面是一个 main 区域的设置，他是一个全局的设置：

```shell
user nobody nobody;
worker_processes 2;
error_log  /usr/local/var/log/nginx/error.log  notice;
pid        /usr/local/var/run/nginx/nginx.pid;
worker_rlimit_nofile 1024;
```

其中：

- user 来指定Nginx Worker进程运行用户以及用户组，默认由nobody账号运行。
- worker_processes来指定了Nginx要开启的子进程数。每个Nginx进程平均耗费10M~12M内存。根据经验，一般指定1个进程就足够了，如果是多核CPU，建议指定和CPU的数量一样的进程数即可。我这里写2，那么就会开启2个子进程，总共3个进程。
- error_log用来定义全局错误日志文件。日志输出级别有debug、info、notice、warn、error、crit可供选择，其中，debug输出日志最为最详细，而crit输出日志最少。
- pid用来指定进程id的存储文件位置。
- worker_rlimit_nofile用于指定一个nginx进程可以打开的最多文件描述符数目，这里是65535，需要使用命令“ulimit -n 65535”来设置。

### events 模块

events 模块来用指定 nginx 的工作模式以及连接数上限，一般设置为：

```shell
events {
    use epoll; #Linux平台
    worker_connections  1024;
}
```

其中：

- use 用来指定 Nginx 的工作模式。Nginx 支持的工作模式有 select、poll、kqueue、epoll、rtsig 和 /dev/poll。其中 select 和 poll 都是标准的工作模式，kqueue 和 epoll 是高效的工作模式，不同的是 epoll 用在Linux平台上，而kqueue 用在BSD系统中,对于 Linux 系统，epoll 工作模式是首选。

- worker_connections 用于定义 Nginx 每个进程的最大连接数，即接收前端的最大请求数，默认是1024。最大客户端连接数由 worker_processes 和worker_connections 决定，即Max_clients=worker_processes*worker_connections，在作为反向代理时，Max_clients 变为：Max_clients = worker_processes * worker_connections/4。 进程的最大连接数受Linux系统进程的最大打开文件数限制，在执行操作系统命令“ulimit -n 65536”后worker_connections 的设置才能生效。

### http 模块

http模块可以说是最核心的模块了，它负责HTTP服务器相关属性的配置，它里面的 server 和 upstream子 模块，至关重要，会在反向代理和负载均衡以及虚拟目录那块仔细说明。

下面是一个 http 模块的配置文件，接下来详细介绍下这段代码中每个配置选项的含义。

```shell
http{
    include       mime.types;
    default_type  application/octet-stream;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /usr/local/var/log/nginx/access.log  main;
    sendfile        on;
    tcp_nopush      on;
    tcp_nodelay     on;
    keepalive_timeout  10;
    #gzip  on;
    upstream myproject {
        .....
    }
    server {
        ....
    }
}
```

- include 来用设定文件的mime类型,类型在配置文件目录下的mime.type文件定义，来告诉nginx来识别文件类型。
- default_type设定了默认的类型为二进制流，也就是当文件类型未定义时使用这种方式，例如在没有配置asp 的locate 环境时，Nginx是不予解析的，此时，用浏览器访问asp文件就会出现下载了。
- log_format用于设置日志的格式，和记录哪些参数，这里设置为main，刚好用于access_log来纪录这种类型。main的类型日志如下：也可以增删部分参数。

```
127.0.0.1 - - [21/Apr/2015:18:09:54 +0800] "GET /index.php HTTP/1.1" 200 87151 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2272.76 Safari/537.36"
```

- access_log 用来纪录每次的访问日志的文件地址，后面的main是日志的格式样式，对应于log_format的main。
- sendfile参数用于开启高效文件传输模式。将tcp_nopush和tcp_nodelay两个指令设置为on用于防止网络阻塞。
- keepalive_timeout设置客户端连接保持活动的超时时间。在超过这个时间之后，服务器会关闭该连接。

### server 模块

sever 模块是 http 的子模块，它用来定义一个虚拟主机。

我们看一下一个简单的server 是如何做的：

```shell
server {
        listen       8080;
        server_name  localhost 192.168.12.10 www.yangyi.com;
        # 全局定义，如果都是这一个目录，这样定义最简单。
        root   /Users/yangyi/www;
        index  index.php index.html index.htm; 
        charset utf-8;
        access_log  usr/local/var/log/host.access.log  main;
        aerror_log  usr/local/var/log/host.error.log  error;
        ....
}
```
其中主要的参数配置如下：

- server标志定义虚拟主机开始。
- listen用于指定虚拟主机的服务端口。
- server_name用来指定IP地址或者域名，多个域名之间用空格分开。
- root 表示在这整个server虚拟主机内，全部的root web根目录。注意要和locate {}下面定义的区分开来。
- index 全局定义访问的默认首页地址。注意要和locate {}下面定义的区分开来。
- charset用于设置网页的默认编码格式。
- access_log用来指定此虚拟主机的访问日志存放路径，最后的main用于指定访问日志的输出格式。

### location 模块

location模块是nginx中用的最多的，也是最重要的模块了，什么负载均衡啊、反向代理啊、虚拟域名啊都与它相关。慢慢来讲：

location 根据它字面意思就知道是来定位的，定位URL，解析URL，所以，它也提供了强大的正则匹配功能，也支持条件判断匹配，用户可以通过location指令实现Nginx对动、静态网页进行过滤处理。像我们的php环境搭建就是用到了它。

我们先来看这个，设定默认首页和虚拟机目录。

```shell
location / {
            root   /Users/yangyi/www;
            index  index.php index.html index.htm;
        }
```

其中，location /表示匹配访问根目录。

root 指令用于指定访问根目录时，虚拟主机的web目录，这个目录可以是相对路径（相对路径是相对于nginx的安装目录）。也可以是绝对路径。

```shell
    # 反向代理配置
    location /test/ {
        proxy_pass http://127.0.0.1:12345;
        proxy_set_header X-real-i$remote_addr;
        proxy_set_header Hos$http_host;
    }


    # 采用uwsgi方式
    location /python/ {
        include uwsgi_params;
        uwsgi_pass 127.0.0.1:33333;
    }


    # 访问nginx本机目录的文件
    location / {
        root   /home/itcast/xwp/itcast/;
        index  index.html index.htm;
    }

    # 静态文件
    location  /static/ {
        alias /var/static/;
    }
```

### upstram 模块

upstream 模块负债负载均衡模块，通过一个简单的调度算法来实现客户端IP到后端服务器的负载均衡。

```shell
upstream test.com{
    ip_hash;
    server 192.168.123.1:80;
    server 192.168.123.2:80 down;
    server 192.168.123.3:8080  max_fails=3  fail_timeout=20s;
    server 192.168.123.4:8080;
}
```

在上面的例子中，通过 upstream 指令指定了一个负载均衡器的名称 test.com 。这个名称可以任意指定，在后面需要的地方直接调用即可。

里面是 ip_hash 这是其中的一种负载均衡调度算法。

Nginx的负载均衡模块目前支持4种调度算法:

- weight 轮询（默认）。每个请求按时间顺序逐一分配到不同的后端服务器，如果后端某台服务器宕机，故障系统被自动剔除，使用户访问不受影响。weight。指定轮询权值，weight值越大，分配到的访问机率越高，主要用于后端每个服务器性能不均的情况下。
- ip_hash。每个请求按访问IP的hash结果分配，这样来自同一个IP的访客固定访问一个后端服务器，有效解决了动态网页存在的session共享问题。
- fair。比上面两个更加智能的负载均衡算法。此种算法可以依据页面大小和加载时间长短智能地进行负载均衡，也就是根据后端服务器的响应时间来分配请求，响应时间短的优先分配。Nginx本身是不支持fair的，如果需要使用这种调度算法，必须下载Nginx的upstream_fair模块。
- url_hash。按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，可以进一步提高后端缓存服务器的效率。Nginx本身是不支持url_hash的，如果需要使用这种调度算法，必须安装Nginx 的hash软件包。
在HTTP Upstream模块中，可以通过server指令指定后端服务器的IP地址和端口，同时还可以设定每个后端服务器在负载均衡调度中的状态。常用的状态有：
- down，表示当前的server暂时不参与负载均衡。
- backup，预留的备份机器。当其他所有的非backup机器出现故障或者忙的时候，才会请求backup机器，因此这台机器的压力最轻。
- max_fails，允许请求失败的次数，默认为1。当超过最大次数时，返回proxy_next_upstream 模块定义的错误。
- fail_timeout，在经历了max_fails次失败后，暂停服务的时间。max_fails可以和fail_timeout一起使用。

注意:

- 当负载调度算法为 ip_hash 时，后端服务器在负载均衡调度中的状态不能是 weight 和 backup。
- nginx的worker_rlimit_nofile达到上限时，再有客户端链接报502错误. 用了log_format指令设置了日志格式之后，需要用access_log指令指定日志文件的存放路径.

## nginx 主要功能介绍

### 反向代理

正向代理，也就是传说中的代理,他的工作原理就像一个跳板，简单的说，我是一个用户，我访问不了某网站，但是我能访问一个代理服务器，这个代理服务器呢，他能访问那个我不能访问的网站，于是我先连上代理服务器，告诉他我需要那个无法访问网站的内容，代理服务器去取回来，然后返回给我。 从网站的角度，只在代理服务器来取内容的时候有一次记录，有时候并不知道是用户的请求，也隐藏了用户的资料，这取决于代理告不告诉网站。结论就是，正向代理 是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并将获得的内容返回给客户端。客户端必须要进行一些特别的设置才能使用正向代理。

反向代理（Reverse Proxy）方式是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。

从用途上来讲：

- 正向代理的典型用途是为在防火墙内的局域网客户端提供访问Internet的途径。
- 正向代理还可以使用缓冲特性减少网络使用率。反向代理的典型用途是将防火墙后面的服务器提供给Internet用户访问。
- 反向代理还可以为后端的多台服务器提供负载平衡，或为后端较慢的服务器提供缓冲服务。
- 另外，反向代理还可以启用高级URL策略和管理技术，从而使处于不同web服务器系统的web页面同时存在于同一个URL空间下。

#### 反向代理配置

1. proxy_pass

```shell
proxy_pass URL;
配置块 location if
此配置将当前请求代理到URL参数指定的服务器上,URL可以是主机名或者IP地址加PORT的形式
proxy_pass http://localhost:8000;
也可以结合负载均衡实用<负载均衡会说明这种情况>
也可以吧HTTP转换成HTTPS
proxy_pass http://192.168.0.1;
默认情况反向代理不转发请求中的Host头部,如果需要设置抓发头部则设置： proxy_set_header Host $host;
```

2. proxy_method

```shell
proxy_method method_name;
配置块 http server location
此配置项表示转发时的协议方法名:proxy_method POST;
那么客户端发来的GET请求在转发时方法改为POST;
```

3. proxy_hide_header

```shell
proxy_hide_header header1;
配置块 http server location;
Nginx会将上游服务器的响应转发给客户端,但默认不转发HTTP头部字段(Date Server X-Pad X-Accel-* )
使用proxy_hide_header可以指定任意头部不能被转发
proxy_hide_header Cache-Control;
proxy_hide_header MicrosoftOfficeWebServer;
```

4. proxy_pass_header

```shell
proxy_pass_header header1;
配置块 http server location
功能与 proxy_hide_header相反,是设置哪些头部允许转发.
proxy_pass_header X-Accel-Redirect;
```

5. proxy_pass_request_body

```shell
proxy_pass_request_body off|on;
默认 on
配置块 http server location;
确定上游服务器是否向上游服务器转发HTTP包体
```

6. proxy_pass_request_header

```shell
proxy_pass_request_header on | off;
默认on
配置块 http server location
确定是否转发HTTP头部
```

7. proxy_redirect

```shell
proxy_redirect [default | off |redirect |replacement]
默认default
配置块 http server location
当上游服务响应时重定向或刷新(HTTP 301 302),proxy_redirect可以重设HTTP头部的location或refresh字段

proxy_redirect http://locahost:8000/two/ http://frontend/one/;
上游响应302,location是URI是http://locahost:8000/two/some/uri/
那是实际转发给客户端的是 http://frontend/one/some/uri/;
可以使用前面提到的ngx_http_core_module模块提供的变量 
proxy_redirect http://locahost:8000/two/ http://$host:server_port/;
可以省略replacement参数的主机名部分,这时候用虚拟主机名填充
proxy_redirect http://locahost:8000/two/ /one/;

使用off参数的时候,将使location和refresh的字段维持不变
proxy_redirect off;

如果使用的 proxy_redirect default;
下面两种配置是等效的
    location /{
        proxy_pass http://upstream:port/two/;
        proxy_redirect default;
    }
    location /{
        proxy_pass http://upstream:port/two/;
        proxy_redirect http://upstream:port/two/ /one/;
    }
```

8. proxy_next_upstream

```shell
proxy_next_upstream [error |timeout |invalid_header |http_500 |http_502~504 |http_404 | off]
默认 proxy_next_upstream error timeout;
配置块 http server location

此配置表示上游一台服务器转发请求出现错误时,继续换一套服务器处理这个请求
其参数用来说明在那些情况下继续选择下一台上游服务器转发请求.
error 向上游发起连接 发送请求 读取响应时出错
timeout 发送请求或读取响应时出错
invalid_header 上游服务器发送的响应时不合法
http_500 上游响应500
http_502 上游响应502
http_503 上游响应503
http_504 上游响应504
http_404 上游响应404
off      关闭proxy_next_upstream功能 只要一出错就选择另外一台上游再次出发
```

Nginx反向代理模块中还提供很多配置,如设置连接的超时时间,临时文件如何存储,如何缓存上游服务器响应等功能.
可以通过阅读 ngx_http_proxy_module了解更多详细情况

```shell
#sudo vim /usr/local/nginx/conf/nginx.conf 

server {
    listen       80;
    server_name  localhost;
    location / {
    #保证代理机器能访问到 下面的机器并装有nginx  在主机号为100的机器上有响应网页
    proxy_pass http://192.168.1.100;
    root   html;
    index  index.html index.htm;
    }
}

sudo /usr/local/nginx/sbnginx -s reload
```

加一些判断条件获取到对方请求的主机防止别人代理到自己的主机上.

## 负载均衡

负载均衡是由多台服务器以对称的方式组成一个服务器集合，每台服务器都具有等价的地位，都可以单独对外提供服务而无须其他服务器的辅助。通过某种负载分担技术，将外部发送来的请求按照事先设定分配算法分配到对称结构中的某一台服务器上，而接收到请求的服务器独立地回应客户的请求。

均衡负载能够平均分配客户请求到服务器列阵，籍此提供快速获取重要数据，解决大量并发访问服务问题。

1. upstream块

```shell
upstream name {...}
配置块 http
upstream块定义一个上游服务器的集群,便于反向代理中的proxy_pass使用

upstream mynet{
    server www.wopai1.com;
    server www.wopai2.com;
    server www.wopai3.com;
}
server {
    location /{
        proxy_pass http://mynet;
    }
}
```

2. server

```shell
server name [paramenters]
配置块upstream
server配置项指定了一台上游服务器的名字,可以是域名 IP地址端口 UNIX句柄
weight= number;设置向这台服务器转发的权重,默认为1
max_fails=number;该选项域fail_timeout配合使用
        指在fail_timeout时间段内如果转发上游失败超过number次就认为当前的fail_timeout时间内
        这台服务器不可用,max_fails默认为1 如果设置为0 表示不检查失败次数
fail_timeout=time; fail_timeout表示该时间内转发多少次失败后就认为上游不可用.默认10s
down    表示上游服务器永久下线,只能在ip_hash配置时才有效
backup  在ip_hash配置时无效.只有所有非备份机都失败,才向上游备份服务器转发请求.
upstream mynet{
    server www.wopai1.com weight=5;
    server www.wopai2.com:8081 max_fails=3 fail_timeout=300s;
    server www.wopai2.com down;
}
```

3. ip_hash

```shell
配置块 upstream
希望来自某一个用户的请求始终落在固定的一台服务器上进行处理.
根据客户端的IP散列计算出一个key,将key按照upstream集群中的上游服务器进行取模,求得的值对应的主机接收转发请求.
ip_hash不可以与weight同时使用
如果upstream配置中有一台服务器暂时不可用,不能直接删除该配置,而应该使用down标识.
upstream mynet{
    ip_hash;
    server www.wowpai1.top;
    server www.wowpai2.top;  
    server www.wowpai3.top down;
}
```

例子,服务器负载均衡基本配置,nginx中可以进行负载均衡的相关设置:

```shell
upstream my.net{    #my.net是自定义的命名 在server结构中引用即可

#代理服务器为 两台机器192.168.22.136 192.168.22.147做负载均衡操作 
#两台机器上 可以跑apache负载功能更为强大的网页相关任务

#max_fails 表示尝试出错最大次数 即可认为该服务器 在fail_timeout时间内不可用
# server servername:port   servername可以写主机名 或者点分式IP
server 192.168.22.136:80 max_fails=1 fail_timeout=300s;
server 192.168.22.147:80 max_fails=1 fail_timeout=300s;  
}


server {
listen       80;
server_name  localhost; 
location / {
    #upstream 块名
    proxy_pass http://my.net;
    root   html;
    index  index.html index.htm;
}
```

## 源码分析

nginx 的源码介绍可以访问淘宝团队出品的资料：[http://tengine.taobao.org/book/](http://tengine.taobao.org/book/)