## Nginx 记录

### 安装

```bash
# 下载安装包
wget http://nginx.org/download/nginx-1.12.2.tar.gz
# 安装依赖
yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel
# 解压缩
tar -zxvf nginx-1.12.2.tar.gz
cd nginx-1.12.2/
# 执行配置
./configure
# 编译安装(默认安装在/usr/local/nginx)
make
make install
```

普通用户安装设置权限：

```bash
# nginx分配给root用户
sudo chown root nginx
sudo chmod u+s nginx
```

**配置文件：**

- nginx主配置文件：`/usr/local/nginx/conf/nginx.conf`
- nginx日志文件：`/usr/local/nginx/logs/access.log`

### 启动与关闭

```bash
# 测试配置文件
${Nginx}/sbin/nginx -t
# 启动(默认配置文件 conf/nginx.conf，-c 指定)
${Nginx}/sbin/nginx 
# 关闭
${Nginx}/sbin/nginx -s stop/quit
# 重启
${Nginx}/sbin/nginx -s reload
```

### 配置

Nginx的主配置文件是nginx.conf，这个配置文件一共由三部分组成，分别为**全局块、events块和http块**。大致结构：

```bash
#全局块
#user  nobody;
worker_processes  1;

#event块
events {
    worker_connections  1024;
}

#http块
http {
    #http全局块
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    #server块
    server {
        #server全局块
        listen       8000;
        server_name  localhost;
        #location块
        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
    #这边可以有多个server块
    server {
      ...
    }
}
```

#### 全局块

[参考资料一](https://www.cnblogs.com/54chensongxia/p/12938929.html)

[参考资料二](https://juejin.cn/post/6844903727011201037#heading-13)

全局块是默认配置文件从开始到events块之间的一部分内容，主要设置一些影响Nginx服务器整体运行的配置指令，因此，这些指令的作用域是Nginx服务器全局

```bash
# 这个代表nginx处理并发的关键配置，值越大，处理并发能力越强。但是会受到硬件、软件等约束。
worker_processes  1;
# 指定可以运行nginx服务的用户和用户组，只能在全局块配置
# user [user] [group]
# 将user指令注释掉，或者配置成nobody的话所有用户都可以运行
# user nobody nobody;
```

#### events块

events块涉及的指令主要影响Nginx服务器与用户的网络连接。常用到的设置包括是否开启对多worker process下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型处理连接请求，每个worker process可以同时支持的最大连接数等。

```bash
# 代表nginx支持的最大连接数
worker_connections  1024

# 当某一时刻只有一个网络连接到来时，多个睡眠进程会被同时叫醒，但只有一个进程可获得连接。如果每次唤醒的进程数目太多，会影响一部分系统性能。在Nginx服务器的多进程下，就有可能出现这样的问题。
# 开启的时候，将会对多个Nginx进程接收连接进行序列化，防止多个进程对连接的争抢
# 默认是开启状态，只能在events块中进行配置
# accept_mutex on | off;

# 指定使用哪种网络IO模型，method可选择的内容有：select、poll、kqueue、epoll、rtsig、/dev/poll以及eventport，一般操作系统不是支持上面所有模型的。
# 只能在events块中进行配置
# use method
# use epoll
```

#### http块

http块是Nginx服务器配置中的重要部分，代理、缓存和日志定义等绝大多数的功能和第三方模块的配置都可以放在这个模块中，包含`http块`和`server块`

```bash
# include指令，用于包含其他的配置文件，可以放在配置文件的任何地方，但是要注意你包含进来的配置文件一定符合配置规范，比如说你include进来的配置是worker_processes指令的配置，而你将这个指令包含到了http块中，着肯定是不行的，上面已经介绍过worker_processes指令只能在全局块中。
# 下面的指令将mime.types包含进来，mime.types和ngin.cfg同级目录，不同级的话需要指定具体路径
 include  mime.types;

# 配置默认类型，如果不加此指令，默认值为text/plain。
# 此指令还可以在http块、server块或者location块中进行配置。
# default_type  application/octet-stream;

# access_log配置，此指令可以在http块、server块或者location块中进行设置
# 在全局块中，我们介绍过errer_log指令，其用于配置Nginx进程运行时的日志存放和级别，此处所指的日志与常规的不同，它是指记录Nginx服务器提供服务过程应答前端请求的日志
# access_log path [format [buffer=size]]
# 如果你要关闭access_log,你可以使用下面的命令
# access_log off;

# log_format指令，用于定义日志格式，此指令只能在http块中进行配置
# log_format  main '$remote_addr - $remote_user [$time_local] "$request" '
#                  '$status $body_bytes_sent "$http_referer" '
#                  '"$http_user_agent" "$http_x_forwarded_for"';
# 定义了上面的日志格式后，可以以下面的形式使用日志
# access_log  logs/access.log  main;

# 开启关闭sendfile方式传输文件，可以在http块、server块或者location块中进行配置
# sendfile  on | off;

# 设置sendfile最大数据量,此指令可以在http块、server块或location块中配置
# sendfile_max_chunk size;
# 其中，size值如果大于0，Nginx进程的每个worker process每次调用sendfile()传输的数据量最大不能超过这个值(这里是128k，所以每次不能超过128k)；如果设置为0，则无限制。默认值为0。
# sendfile_max_chunk 128k;

# 配置连接超时时间,此指令可以在http块、server块或location块中配置。
# 与用户建立会话连接后，Nginx服务器可以保持这些连接打开一段时间
# timeout，服务器端对连接的保持时间。默认值为75s;header_timeout，可选项，在应答报文头部的Keep-Alive域设置超时时间：“Keep-Alive:timeout= header_timeout”。报文中的这个指令可以被Mozilla或者Konqueror识别。
# keepalive_timeout timeout [header_timeout]
# 下面配置的含义是，在服务器端保持连接的时间设置为120 s，发给用户端的应答报文头部中Keep-Alive域的超时时间设置为100 s。
# keepalive_timeout 120s 100s

# 配置单连接请求数上限，此指令可以在http块、server块或location块中配置。
# Nginx服务器端和用户端建立会话连接后，用户端通过此连接发送请求。指令keepalive_requests用于限制用户通过某一连接向Nginx服务器发送请求的次数。默认是100
# keepalive_requests number;		
```

### 示例

#### 配置请求转发

```bash
server {
    listen       80;
    server_name  47.104.xxx.xxx;

    location / {
        root   html;
        proxy_pass http://127.0.0.1:8080;
        index  index.html index.htm;
    }
}
# 以上这段配置的意思就是请求是47.104.xxx.xxx:80，都会转发至47.104.xxx.xxx:8080
```

#### 根据请求后缀分发

```bash
server {
    listen       7001;
    server_name  47.104.xxx.xxx;

    location ~ /dev/ {
        proxy_pass http://127.0.0.1:8080;
    }
    
    location ~ /prod/ {
        proxy_pass http://127.0.0.1:8081;
    }
}
# 将请求xxx:7001/dev转发到tomcat8080，将请求xxx:7001/prod转发到tomcat8081。现在我们的nginx监听的端口号是7001
```

其中，配置转发的时候用到了~，其含义内容如下：

- = 严格匹配。如果这个查询匹配，那么将停止搜索并立即处理此请求。
- ~ 为区分大小写匹配(可用正则表达式)
- !~为区分大小写不匹配
- ~* 为不区分大小写匹配(可用正则表达式)
- !~*为不区分大小写不匹配
- ^~ 如果把这个前缀用于一个常规字符串,那么告诉`nginx`如果路径匹配那么不测试正则表达式。

#### 负载均衡

```bash
# 在http块中配置两个tomcat的服务列表
# 负载策略 [轮询（默认） | weight（权重）：默认为1，权重越高，分配的请求越多 | ip hash]
upstream myserver{
    server 127.0.0.1:8080;
    server 127.0.0.1:8081;
}

# server块配置规则
server {
    listen       80;
    server_name  47.104.xxx.xxx;

    location / {
        root   html;
        proxy_pass http://myserver;
        index  index.html index.htm;
    }
}
```

