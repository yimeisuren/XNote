[TOC]



# nginx

## 安装nginx

1. 下载nginx安装包, 上传到linux系统中并解压

2. 安装必要的编译工具等

   ```bash
   #安装编译工具及库文件
   yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel pcre-devel
   ```

3. 编译安装

   ```bash
   make & make install
   ```

4. 安装完成后, 进入到`/usr/local`目录下查看, 可以看到`nginx`目录. 即安装目录为`/usr/local/nginx`

## 配置

创建nginx运行使用的用户

```shell
/usr/sbin/groupadd bing
/usr/sbin/useradd -g bing bing
```

配置nginx.conf ，将/usr/local/nginx/conf/nginx.conf替换为以下内容

```shell
cat /usr/local/nginx/conf/nginx.conf
```

## 启动nginx

进入到nginx安装目录下的`sbin`中通过`./nginx`启动nginx服务器

```shell
/usr/local/nginx/sbin/nginx

#或者
cd /usr/local/nginx/sbin
./nginx
```

## 其它操作

```shell
/usr/local/nginx/sbin/nginx -s reload            # 重新载入配置文件
/usr/local/nginx/sbin/nginx -s reopen            # 重启 Nginx
/usr/local/nginx/sbin/nginx -s stop              # 停止 Nginx

ps -ef | grep nginx
```

## 连接失败的原因

```shell
#可能是防火墙80端口没有开放
```

# 配置文件

## 全局块

影响nginx服务器整体运行

```shell
user xiong xiong;
worker_processes 2; #设置值和CPU核心数一致
error_log /usr/local/webserver/nginx/logs/nginx_error.log crit; #日志位置和日志级别
pid /usr/local/webserver/nginx/nginx.pid;
#Specifies the value for maximum file descriptors that can be opened by this process.
worker_rlimit_nofile 65535;
```

## events块

影响nginx服务器与用户的网络连接

```shell
events
{
  use epoll;
  worker_connections 65535;
}
```

## http块

这里面又分全局块, server块. 原理同上

```shell
http
{
  include mime.types;
  default_type application/octet-stream;
  log_format main  '$remote_addr - $remote_user [$time_local] "$request" '
               '$status $body_bytes_sent "$http_referer" '
               '"$http_user_agent" $http_x_forwarded_for';
  
#charset gb2312;
     
  server_names_hash_bucket_size 128;
  client_header_buffer_size 32k;
  large_client_header_buffers 4 32k;
  client_max_body_size 8m;
     
  sendfile on;
  tcp_nopush on;
  keepalive_timeout 60;
  tcp_nodelay on;
  fastcgi_connect_timeout 300;
  fastcgi_send_timeout 300;
  fastcgi_read_timeout 300;
  fastcgi_buffer_size 64k;
  fastcgi_buffers 4 64k;
  fastcgi_busy_buffers_size 128k;
  fastcgi_temp_file_write_size 128k;
  gzip on; 
  gzip_min_length 1k;
  gzip_buffers 4 16k;
  gzip_http_version 1.0;
  gzip_comp_level 2;
  gzip_types text/plain application/x-javascript text/css application/xml;
  gzip_vary on;
 
  #limit_zone crawler $binary_remote_addr 10m;
 #下面是server虚拟主机的配置
 server
  {
    listen 80;#监听端口
    server_name localhost;#域名
    index index.html index.htm index.php;
    root /usr/local/webserver/nginx/html;#站点目录
      location ~ .*\.(php|php5)?$
    {
      #fastcgi_pass unix:/tmp/php-cgi.sock;
      fastcgi_pass 127.0.0.1:9000;
      fastcgi_index index.php;
      include fastcgi.conf;
    }
    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|ico)$
    {
      expires 30d;
  # access_log off;
    }
    location ~ .*\.(js|css)?$
    {
      expires 15d;
   # access_log off;
    }
    access_log off;
  }

}
```





# 配置实例

## 实例1: 反向代理

实现效果 : 打开浏览器, 在浏览器地址栏中输入地址www.xiong.com, 跳转linux系统tomcat主页面中

1. 在linux中安装tomcat

   链接https://blog.csdn.net/qq_36273886/article/details/103328813

2. 在window主机中配置域名的端口映射(修改host)

   ```shell
   # 将域名和nginx的端口进行映射
   192.168.1.105 www.xiong.com
   ```

3. 修改linux系统中的nginx的配置文件

   ```shell
    server
     {
       listen 80;
       server_name 192.168.1.105;#修改此处
       
       #访问192.168.1.105:80(外部访问虚拟机的ip地址)会转发到127.0.0.1:8080(虚拟机自己访问自己时候的localhost)端口
       location / {
       	root html;
       	proxy_pass http://127.0.0.1:8080;
       	index index.html index.html;
       }
       #访问的时候带有/edu,则访问8080
       location ~ /edu/ {
       	proxy_pass http://127.0.0.1:8080;
    }
       #访问的时候带有/vod,则访问8081
       location ~ /vod/ {
       	proxy_pass http://127.0.0.1:8081;
       }
     }
   
   ```
   
   最终在自己电脑上访问www.xiong.com/edu/test.html就可以成功访问
   
   在其它电脑上由于没有配置本地的域名解析, 所以不可以使用www.xiong.com而需要输入它所对应ip地址192.168.1.105, 输入192.168.1.105/edu/test.html
   
   

## 实例2: 负载均衡

实现效果 : 在浏览器输入地址http://192.168.1.105/edu/a.html, 负载均衡效果, 平均8082和8083端口中

1. 在linux上准备两台tomcat服务器, 端口号分别为8082和8083,并分别为其在webapps目录下创建edu文件夹, 并准备test.html页面

2. 在nginx的配置文件nginx.conf中进行负载均衡的配置

   ```shell
   http{
   	upstream myserver{ #添加这一段
   		ip_hash;
   		server 192.168.1.105:8082 weight=1;
   		server 192.168.1.105:8083 weight=1;
   	}
   	server{
   		listen 80;
   		server_name 192.168.1.105;#这里修改成linux系统的端口号
   		
   		
   		location / {
   			proxy_pass http://myserver; #添加上面自定义的myserver
   			root html;
   			index index.htmll index.html;
   		}
   		
   	}
   }
   ```

分配策略:

1. 轮询 (默认): 每个请求按时间顺序逐一分配到不同的后端服务器,如果后端服务器down掉,能自动剔除

2. weight(权重): 权重越高, 任务越重

3. ip_hash: 每个请求按访问的IP的hash结果分配, 这样每个 访客固定访问一个后端服务器, 可以解决session问题

4. fair(第三方): 按照后端服务器的响应时间来分配, 响应时间短的先分配, 只需要将ip_hash替换成fair

   

## 实例3: 动静分离

为了加快网页解析页面, 将动态资源和静态资源分开进行部署, 静态资源服务器和动态资源服务器. 静态文件独立成单独的域名,放在独立的服务器上(主流推崇)

+ 先在服务器(linux虚拟机)创建static目录, 用来存放静态资源
+ 动态资源放到tomcat的目录下
+ linux是一台服务器, 其中的tomcat又是一台服务器

expires来设置缓存过期时间, 在过期时间内, 如果服务器资源没有发生变化, 则直接从浏览器缓存获取,并返回状态码304

1. 在linux系统中准备一些静态资源static--->css和js

2. 在nginx的配置文件中进行配置

   ```shell
   server {
   	location /css{
   		root /static/;#这两行表示出根目录为static,/css为根目录下的css文件
   		index index.html index.html;
   	}
   	local /js{
   		root /static/;#和static目录的创建位置有关
   		index index.html index.html;
   		autoindex on;#就和很多镜像仓库的那些目录一样
   	}
   }
   ```

3. 在浏览器中输入地址http://192.168.1.105/css/01.jpg (01.jpg是css文件夹中的一张图片)

## 实例4: 高可用

实现效果 : 即使nginx宕机了,请求仍然可以实现效果

![image-20200515114723662](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200515114723662.png)

1. 需要两台nginx服务器
2. 需要keepalived
3. 需要虚拟ip

配置高可用环境

1. 两台虚拟机linux
2. 在linux上安装nginx和keepalived (yum install xxx)
3. 修改keepalived的配置文件(/etc/keepalived)
4. 在/usr/local/src下放置检测nginx是否宕机的脚本文件
5. 把两台虚拟机服务上的nginx和keepalived都启动 (systemctl start keepalived.service)