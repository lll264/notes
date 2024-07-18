参考

[尚硅谷nginx](https://blog.csdn.net/qq_40036754/article/details/102463099?ops_request_misc=%7B%22request%5Fid%22%3A%22171642240216800222871151%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=171642240216800222871151&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-102463099-null-null.142%5Ev100%5Epc_search_result_base2&utm_term=nginx&spm=1018.2226.3001.4187)  

[nginx教程](https://blog.csdn.net/qq_40036754/article/details/102463099)

# 安装

### 1. 在 `/usr/local` 建nginx文件夹，下载nginx压缩文件

> nginx下载地址：http://nginx.org/download
> 找稳定版本

执行命令

```shell
cd /usr/local
mkdir nginx
wget http://nginx.org/download/nginx-1.24.0.tar.gz
tar -zxvf nginx-1.24.0.tar.gz
```

### 2. 安装编译工具、库文件

```shell
yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel pcre-devel
```

说明：

> gcc、gcc-c++ 主要用来进行编译相关使用
>
> openssl、openssl-devel 一般当配置https服务的时候就需要这个了
>
> zlib、zlib-devel 主要用于文件的解压缩
>
> pcre、pcre-devel Nginx的rewrite模块和HTTP核心模块会用到PCRE正则表达式语法

### 3. 进入安装包目录，编译安装

```shell
cd nginx-1.24.0
./configure --prefix=/usr/local/nginx
make && make install
```

说明：

> ./configure 编译
> make && make install 安装

### 4. 进入sbin目录，启动nginx

```shell
cd /usr/local/nginx/sbin
./nginx
```

# 一、nginx简介

## 1.什么是nginx和可以做什么事情

- Nginx 是高性能的 HTTP 和反向代理的web服务器，处理高并发能力是十分强大的，能经受高负 载的考验,有报告表明能支持高达 50,000 个并发连接数。
- 其特点是占有内存少，并发能力强，事实上nginx的并发能力确实在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：百度、京东、新浪、网易、腾讯、淘宝等。


## 2.Nginx 作为 web 服务器

Nginx 可以作为静态页面的 web 服务器，同时还支持 CGI 协议的动态语言，比如 perl、php 等。但是不支持 java。Java 程序只能通过与 tomcat 配合完成。Nginx 专为性能优化而开发， 性能是其最重要的考量,实现上非常注重效率 ，能经受高负载的考验,有报告表明能支持高 达 50,000 个并发连接数。
https://lnmp.org/nginx.html

## 3.正向代理

Nginx 不仅可以做反向代理，实现负载均衡。还能用作正向代理来进行上网等功能。 正向代理：如果把局域网外的 Internet 想象成一个巨大的资源库，则局域网中的客户端要访 问 Internet，则需要通过代理服务器来访问，这种代理服务就称为正向代理。

简单一点：**通过代理服务器来访问服务器的过程 就叫 正向代理**。
需要在客户端配置代理服务器进行指定网站访问

## 4.反向代理

反向代理，其实客户端对代理是无感知的，因为客户端不需要任何配置就可以访问。
我们只 需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后，在返 回给客户端，此时反向代理服务器和目标服务器对外就是一个服务器，**暴露的是代理服务器 地址，隐藏了真实服务器 IP 地址**。

## 5.负载均衡

增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的 情况改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负 载均衡

客户端发送多个请求到服务器，服务器处理请求，有一些可能要与数据库进行交互，服 务器处理完毕后，再将结果返回给客户端。

# 二、常用命令和配置文件

## 1.常用命令

注意，使用nginx操作命令前提条件，必须进入到nginx的目录

> /usr/local/nginx/sbin

```bash
# 查看版本号
./nginx -v
# 启动nginx
./nginx
# 关闭nginx
./nginx -s stop
# 重新加载nginx（修改配置文件使其生效）
./nginx -s reload
```

## 2.配置文件

nginx的配置文件在`/usr/local/nginx/conf`中的**nginx.conf**

nginx的配置文件由三部分组成

去掉注释后的nginx.conf文件如下

```
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;

        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}

```

### 全局块

从配置文件开始到events块之间的内容，主要会设置一些影响nginx服务器**整体运行的配置指令**

比如配置

```bash
worker_processes 1;
```

这是nginx服务器并发处理服务的关键配置，它的值越大，可以支持的并发处理量也越多，但是会受到硬件、软件等设备的制约。

### events块

events块涉及的指令主要影响nginx服务器与用户的网络连接，常用的设置包括**是否开启对多 work process下的网络连接进行序列化**，**是否允许同时接收多个网络连接**，**选取哪种事件驱动模型来处理连接请求**，每个work process可以同时**支持的最大连接数**等

比如配置

```
worker_connections 1024;
```

支持的最大连接数为1024，这部分配置对于nginx的性能影响较大，在实际中应灵活配置

### http块

```
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;    

        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```

这算是 Nginx 服务器配置中最频繁的部分，代理、缓存和日志定义等绝大多数功能和第三方模块的配置都在这里。

需要注意的是：http 块也可以包括 http全局块、server 块

- **http全局块**
  这部分包括文件引入，MIME-TYPE定义，日志自定义、连接超时时间、单链接请求数上限等。
- **server块**
  这块和虚拟主机有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是完全一样的，该技术的产生是为了 节省互联网服务器硬件成本。
  每个 http 块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机。
  而每个 server 块也分为全局 server 块，以及可以同时包含多个 locaton 块。

(1)**全局 server 块**

最常见的配置是本虚拟机主机的监听配置和本虚拟主机的名称或IP配置。
比如配置，代表监听80端口，服务名称为localhost

```
 listen  			  80;
 server_name  localhost;
```
(2)**location 块**

一个 server 块可以配置多个 location 块。这块的主要作用是基于 Nginx 服务器接收到的请求字符串（例如 server_name/uri-string），对虚拟主机名称 （也可以是IP 别名）之外的字符串（例如 前面的 /uri-string）进行匹配，对特定的请求进行处理。 地址定向、数据缓存和应答控制等功能，还有许多第三方模块的配置也在这里进行。
比如配置

```
location /{
	root    html;
	index   index.html main.html
}
```

# 三、nginx实例配置

## 1.反向代理

### 案例1

#### 实现效果

- 打开浏览器，在浏览器地址栏输入地址 www.123.com，跳转到 liunx 系统 tomcat 主页面中

#### tomcat准备

- 下载tomcat压缩包解压到**/usr/local/apache-tomcat-8.5.87**
- 进入其中bin目录**通过./startup.sh启动tomcat，./shutdown.sh关闭**
- 可以通过修改conf目录下的server文件改变默认监听的8080端口
- 在浏览器通过**192.168.158.11:8080访问tomcat**

#### 具体步骤

1.在 windows 系统的 host 文件进行域名和 ip 对应关系的配置

![在这里插入图片描述](assets/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwMDM2NzU0,size_16,color_FFFFFF,t_70.png)

添加内容到hosts中

192.168.158.11 www.123.com

2.第二步 在 nginx 进行请求转发的配置（反向代理配置）

```
server {
        listen       80;
        server_name  192.168.158.11;    

        location / {
            root   html;
            proxy_pass http://192.168.158.11:8080;
            index  index.html index.htm;
        }
```

3.启动tomcat`/usr/local/apache-tomcat-8.5.87/bin/startup.sh`

4.在windows中访问www.123.com或192.168.158.11

### 案例2

#### **实现效果**

实现效果：使用 nginx 反向代理，根据访问的路径跳转到不同端口的服务中
nginx 监听端口为 8001，
访问 http://192.168.158.11:8001/edu/ 直接跳转到 127.0.0.1:8081
访问 http://192.168.158.11/vod/ 直接跳转到 127.0.0.1:8082

#### **tomcat准备**

**在解压或复制一个tomcat文件夹，模拟两个不同的服务器**，重新设置一下/conf/server.xml文件中的端口，监听8081

**1、修改server 的默认端口，由默认8005->8091**

**2、修改http协议的默认端口，由默认的8080->8081**

**3、修改默认ajp协议的默认端口，由默认的8009->9001**

并准备好测试的页面,写一个a.html页面

tomcat8080的tomcat，放到目录 /webapp/vod 下，内容：

```html
<h1>fengfanchen-nginx-8081!!!</h1>
```

tomcat8082的tomcat，放到目录 /webapp/edu 下，内容：

```html
<h1>fengfanchen-nginx-8082!!!</h1>
```

#### 具体步骤

1.nginx配置。修改nginx的配置文件nginx.conf

```
server {
        listen       80;
        server_name  192.168.158.11;    

        location / {
            root   html;
            index  index.html index.htm;
        }
        
        location ~ /vod/ {
            proxy_pass http://192.168.158.11:8080;
        }
        location ~ /edu/ {
            proxy_pass http://192.168.158.11:8081;
        }
```

2.通过192.168.158.11/vod和192.168.158.11/edu分别访问到不同的tomcat服务器

![image-20240523225838054](assets/image-20240523225838054.png)

![image-20240523225817951](assets/image-20240523225817951.png)

#### **location 指令说明**

该指令用于匹配 URL。

语法如下：

1、= ：用于不含正则表达式的 uri 前，要求请求字符串与 uri 严格匹配，如果匹配 成功，就停止继续向下搜索并立即处理该请求。

2、~：用于表示 uri 包含正则表达式，并且区分大小写。

3、~*：用于表示 uri 包含正则表达式，并且不区分大小写。

4、^~：用于不含正则表达式的 uri 前，要求 Nginx 服务器找到标识 uri 和请求字 符串匹配度最高的 location 后，立即使用此 location 处理请求，而不再使用 location 块中的正则 uri 和请求字符串做匹配。

注意：如果 uri 包含正则表达式，则必须要有 ~ 或者 ~*标识。

## 2.负载均衡

### 实现效果

浏览器地址栏输入地址 http://208.208.128.122/edu/a.html，负载均衡效果，平均 8081 和 8082 端口中

### tomcat准备

- 准备两台 tomcat 服务器，一台 8080，一台 8081
- 在两个tomcat服务器webapp文件夹中，都创建一个edu文件夹，里面放a.html

1.nginx配置

```
upstream myserver {
	server 192.168.158.11:8080;
	server 192.168.158.11:8081;
}
server {
        listen       80;
        server_name  192.168.158.11;    
	
        location / {
            root   html;
            proxy_pass http://myserver;
            index  index.html index.htm;
        }
```

2.启动两个tomcat和nginx

```bash
启动第一个tomcat监听8080端口
/usr/local/apache-tomcat-8.5.87/bin/startup.sh

启动第二个tomcat监听8081端口
/usr/local/tomcat8081/apache-tomcat-8.5.87/bin/start.sh

启动nginx监听默认的80端口
/usr/local/nginx/sbin/nginx
```

3.浏览器输入http://192.168.158.11/edu/a.html多次访问，可以看到发出的请求，分别访问到了tomcat8080和tomcat8081服务器

![image-20240524133552517](assets/image-20240524133552517.png)

<img src="assets/image-20240524133627553.png" alt="image-20240524133627553" style="zoom:80%;" />

### 分配策略配置

#### 1）轮询（默认）

**每个请求按时间顺序逐一分配到不同的后端服务器**，如果后端服务器 down 掉，能自动剔除。

#### 2）weight

weight 代表权重, 默认为 1,**权重越高被分配的客户端越多**

```
upstream myserver {
	server 192.168.158.11:8080 weight=2;
	server 192.168.158.11:8081 weight=1;
}
```

#### 3）ip_hash

ip_hash 每个请求**按访问 ip 的 hash 结果分配**，这样每个访客**固定访问一个后端服务器**

```
upstream myserver {
	ip_hash;
	server 192.168.158.11:8080 weight=2;
	server 192.168.158.11:8081 weight=1;
}
```

#### 4）fair（第三方）

fair（第三方），按后端服务器的响应时间来分配请求，**响应时间短的优先分配。**

```
upstream myserver {
	server 192.168.158.11:8080 weight=2;
	server 192.168.158.11:8081 weight=1;
	fair;
}
```

## 3.动静分离

