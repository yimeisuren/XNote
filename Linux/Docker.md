[TOC]



# Docker

## 介绍

```
以前是搬家,现在是搬楼.将所有的环境配置都一次性打包,解决在我的机器上能跑,但是在服务器上出现各种问题的毛病.使用镜像,达到一处构建,处处运行
三要素: 镜像,容器,仓库
```

## 查看版本

```shell
#查看系统版本
[root@izbp11cvux96riz6fo5klaz /]# uname -r		
3.10.0-514.26.2.el7.x86_64

#查看centos的版本
[root@izbp11cvux96riz6fo5klaz /]# cat /etc/redhat-release
CentOS Linux release 7.3.1611 (Core) 

```

## Docker在Centos7上的安装

```shell
# 官方文档地址
https://docs.docker.com/engine/install/centos/
```

## 用阿里云镜像加速器

```shell
# 登录到下面的网址
https://cr.console.aliyun.com/cn-hangzhou/instances/repositories
# centos7的配置文件/etc/docker/daemon.json

#直接复制第3部分的内容,粘贴即可
```



## Docker命令

### Docker进程相关的命令

```shell
# 启动docker进程,并查看运行状态
# Active: active (running) since Sat 2020-04-11 13:38:33 CST; 8min ago
[root@izbp11cvux96riz6fo5klaz /]# systemctl start docker
[root@izbp11cvux96riz6fo5klaz /]# systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
   Active: active (running) since Sat 2020-04-11 13:38:33 CST; 8min ago
     Docs: https://docs.docker.com
 Main PID: 21641 (dockerd)
    Tasks: 10
   Memory: 51.9M
   CGroup: /system.slice/docker.service
           └─21641 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/contai...

# 停止docker服务
[root@izbp11cvux96riz6fo5klaz /]# systemctl stop docker
[root@izbp11cvux96riz6fo5klaz /]# systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: https://docs.docker.com

# 重启docker服务
[root@izbp11cvux96riz6fo5klaz /]# systemctl restart docker

# 设置开机自启动
[root@izbp11cvux96riz6fo5klaz /]# systemctl enable docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.

```

### Docker镜像相关命令

```shell
# 查看镜像
	# TAG表示版本号
	#IMAGE ID表示镜像的ID,删除的时候能够用到
[root@izbp11cvux96riz6fo5klaz /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              fce289e99eb9        15 months ago       1.84kB



# 搜索镜像
[root@izbp11cvux96riz6fo5klaz /]# docker search redis
NAME                             DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
redis                            Redis is an open source key-value store that…   8010                [OK]                
bitnami/redis                    Bitnami Redis Docker Image                      141                                     [OK]
sameersbn/redis                                                                  79                                      [OK]
grokzen/redis-cluster            Redis cluster 3.0, 3.2, 4.0 & 5.0               65                                      
rediscommander/redis-commander   Alpine image for redis-commander - Redis man…   36                                      [OK]
kubeguide/redis-master           redis-master with "Hello World!"                31                                      
redislabs/redis                  Clustered in-memory database engine compatib…   24                                      
oliver006/redis_exporter          Prometheus Exporter for Redis Metrics. Supp…   21                                      
arm32v7/redis                    Redis is an open source key-value store that…   21                                      
redislabs/redisearch             Redis With the RedisSearch module pre-loaded…   20                                      
bitnami/redis-sentinel           Bitnami Docker Image for Redis Sentinel         12                                      [OK]
webhippie/redis                  Docker images for Redis                         11                                      [OK]
redislabs/redisgraph             A graph database module for Redis               10                                      [OK]
s7anley/redis-sentinel-docker    Redis Sentinel                                  9                                       [OK]
insready/redis-stat              Docker image for the real-time Redis monitor…   9                                       [OK]
arm64v8/redis                    Redis is an open source key-value store that…   9                                       
redislabs/redismod               An automated build of redismod - latest Redi…   7                                       [OK]
centos/redis-32-centos7          Redis in-memory data structure store, used a…   5                                       
redislabs/redisinsight           RedisInsight - The GUI for Redis                4                                       
circleci/redis                   CircleCI images for Redis                       4                                       [OK]
clearlinux/redis                 Redis key-value data structure server with t…   2                                       
wodby/redis                      Redis container image with orchestration        1                                       [OK]
tiredofit/redis                  Redis Server w/ Zabbix monitoring and S6 Ove…   1                                       [OK]
runnable/redis-stunnel           stunnel to redis provided by linking contain…   1                                       [OK]
xetamus/redis-resource           forked redis-resource                           0                                       [OK]



# 拉取镜像
[root@izbp11cvux96riz6fo5klaz /]# docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
c499e6d256d6: Pull complete 
74cda408e262: Pull complete 
ffadbd415ab7: Pull complete 
Digest: sha256:282530fcb7cd19f3848c7b611043f82ae4be3781cb00105a1d593d7e6286b596
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
# 可以在Docker Hub中搜索查看nginx维护的版本号,不加版本号则默认下载最新的



# 删除镜像(需要先删除容器)
	# rm 表示remove
	# i 表示images
	# fce289e99eb9表示id,可以换成nginx:latest
[root@izbp11cvux96riz6fo5klaz /]# docker rmi fce289e99eb9
# 查看所有镜像的id
[root@izbp11cvux96riz6fo5klaz /]# docker images -q
ed21b7a8aee9
fce289e99eb9
# 将所有镜像的id当作一个参数传给docker rmi '?'
[root@izbp11cvux96riz6fo5klaz /]# docker rmi 'docker images -q'



# 如果有基于该镜像创建的容器，则默认无法删除镜像，需要先通过docker rm删除容器，然后再用docker rmi删除镜像，或者使用docker rmi -f强制删除(不推荐)
```

### Docker容器相关命令

+ 创建容器(通过镜像创建)

  ```shell
  #交互式容器(-it)
  docker run -it --name=xiong centos:7 /bin/bash
  #守护式容器(-id)
  docker run -id --name=xiong2 centos:7
  ```

  ```shell
  #使用-it创建的容器在退出后立马关闭
      -i 表示即使没有客户端连接,仍然保持运行
      -t 表示打开一个伪终端
      --name=xiong 表示命名
      cnetos:7 表示指定镜像
  [root@izbp11cvux96riz6fo5klaz /]# docker run -it --name=xiong centos:7 /bin/bash
  [root@bd871e6a941a /]# 					# 此时相当于进入到docker容器的内部,bd871e6a941a就是对应容器的id
  [root@bd871e6a941a /]# exit 			# 退出容器
  
  
  
  [root@izbp11cvux96riz6fo5klaz /]# docker ps				#查看当前运行中的容器
  CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
  [root@izbp11cvux96riz6fo5klaz /]# docker ps -a			#查看容器的历史记录
  CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                         PORTS               NAMES
  bd871e6a941a        centos:7            "/bin/bash"         7 minutes ago       Exited (0) 3 minutes ago                           xiong
  5edd4c6e1a87        hello-world         "/hello"            56 minutes ago      Exited (0) 56 minutes ago                          goofy_haslett
  0aa4a1784b7d        hello-world         "/hello"            About an hour ago   Exited (0) About an hour ago                       upbeat_curran
  1f4d683362ac        hello-world         "/hello"            9 days ago          Exited (0) 9 days ago                              pensive_haibt
  
  
  
  #使用下面这种方式创建容器,不会自动进入到容器内部,在退出时也不会自动关闭
  [root@izbp11cvux96riz6fo5klaz /]# docker run -id --name=xiong2 centos:7		#创建容器
  8c41326304ff18943a8eea6a122e909579600f44bd06565626e516e53f1c30b2
  [root@izbp11cvux96riz6fo5klaz /]# docker exec -it xiong2 /bin/bash			#进入到容器中
  [root@8c41326304ff /]# exit													#退出容器
  exit
  [root@izbp11cvux96riz6fo5klaz /]# docker ps									#查看容器
  CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS               NAMES
  8c41326304ff        centos:7            "/bin/bash"         About a minute ago   Up About a minute                       xiong2
  ```

  

+ 查看容器

  ```shell
  #查看当前运行中的容器
  docker ps
  #查看所有容器
  docker ps -a
  ```

  

+ 进入容器

  ```shell
  #交互式容器在创建时自动进入
  #守护式容器的进入方法
  docker exec -it xiong2 /bin/bash
  ```

  

+ 启动容器

  ```shell
  docker start 容器名称/容器id
  ```

  

+ 停止容器

  ```shell
  [root@izbp11cvux96riz6fo5klaz /]# docker stop 8c41326304ff
  ```

  

+ 删除容器

  ```shell
  [root@izbp11cvux96riz6fo5klaz /]# docker rm `docker ps -aq`
  #这些列出来的表示删除成功
  bd871e6a941a
  5edd4c6e1a87
  0aa4a1784b7d
  1f4d683362ac
  #开启中的容器不能被删除,此时报错
  Error response from daemon: You cannot remove a running container 8c41326304ff18943a8eea6a122e909579600f44bd06565626e516e53f1c30b2. Stop the container before attempting removal or force remove
  
  ```

  

+ 查看容器信息

  ```shell
  docker inspect 容器名称
  ```

## Docker容器的数据卷

```shell
# 为了解决以下问题
Docker容器删除后,在容器中产生的数据还在吗?不在
	答 : 配置数据卷
Docker容器和外部机器可以直接交换文件吗?不可以

容器之间想要进行数据交互怎么办?
	答 : 两个容器挂载同一个数据卷,如果容器不止2个,可以使用数据卷容器
```

### 配置数据卷(相当于共享文件夹)

```shell
docker run ... -v 宿主机目录(文件):容器内目录(文件)
#注意事项:
	目录必须时绝对路径
	如果目录不存在,会自动创建
	可以挂载多个数据卷


#将宿主机中的/root/data与容器中的/root/data_container关联起来

[root@izbp11cvux96riz6fo5klaz /]# docker run -it --name=xiong -v /root/data:/root/data_container centos:7 /bin/bash
[root@2bf270b4c2d5 /]# cd ~
[root@2bf270b4c2d5 ~]# ll
total 8
-rw------- 1 root root 3416 Oct  1  2019 anaconda-ks.cfg
drwxr-xr-x 2 root root 4096 Apr 11 07:20 data_container

#复制对话,在宿主机中查看root目录
[root@izbp11cvux96riz6fo5klaz ~]# ll
total 12
drwxr-xr-x 2 root root 4096 Apr 11 15:20 data

#数据同步
#在宿主机的data目录下新建一个xiong.txt文件
[root@izbp11cvux96riz6fo5klaz ~]# cd data/
[root@izbp11cvux96riz6fo5klaz data]# touch xiong.txt
[root@izbp11cvux96riz6fo5klaz data]# ll
total 0
-rw-r--r-- 1 root root 0 Apr 11 15:25 xiong.txt
#然后再在容器中查看关联目录中发生的变化
[root@2bf270b4c2d5 ~]# cd data_container/
[root@2bf270b4c2d5 data_container]# ll
total 0
-rw-r--r-- 1 root root 0 Apr 11 07:25 xiong.txt


#写入一些数据到文件中
[root@2bf270b4c2d5 data_container]# echo itcast > a.txt
[root@2bf270b4c2d5 data_container]# ll
total 4
-rw-r--r-- 1 root root 7 Apr 11 07:29 a.txt
-rw-r--r-- 1 root root 0 Apr 11 07:25 xiong.txt
[root@2bf270b4c2d5 data_container]# cat a.txt
itcast

```

### 配置数据卷容器

```shell
[root@izbp11cvux96riz6fo5klaz ~]# docker run -it --name=xiong -v /volume centos:7
[root@izbp11cvux96riz6fo5klaz ~]# docker run -it --name=xiong2 --volumes-from xiong centos:7
[root@e36a92c9976c /]# ll
total 60
-rw-r--r--   1 root root 12123 Oct  1  2019 anaconda-post.log
lrwxrwxrwx   1 root root     7 Oct  1  2019 bin -> usr/bin
drwxr-xr-x   5 root root   360 Apr 11 14:53 dev
drwxr-xr-x   1 root root  4096 Apr 11 14:53 etc
drwxr-xr-x   2 root root  4096 Apr 11  2018 home
lrwxrwxrwx   1 root root     7 Oct  1  2019 lib -> usr/lib
lrwxrwxrwx   1 root root     9 Oct  1  2019 lib64 -> usr/lib64
drwxr-xr-x   2 root root  4096 Apr 11  2018 media
drwxr-xr-x   2 root root  4096 Apr 11  2018 mnt
drwxr-xr-x   2 root root  4096 Apr 11  2018 opt
dr-xr-xr-x 102 root root     0 Apr 11 14:53 proc
dr-xr-x---   2 root root  4096 Oct  1  2019 root
drwxr-xr-x  11 root root  4096 Oct  1  2019 run
lrwxrwxrwx   1 root root     8 Oct  1  2019 sbin -> usr/sbin
drwxr-xr-x   2 root root  4096 Apr 11  2018 srv
dr-xr-xr-x  13 root root     0 Apr 11  2018 sys
drwxrwxrwt   7 root root  4096 Oct  1  2019 tmp
drwxr-xr-x  13 root root  4096 Oct  1  2019 usr
drwxr-xr-x  18 root root  4096 Oct  1  2019 var
drwxr-xr-x   2 root root  4096 Apr 11 07:45 volume


# 在原宿主机上查看xiong的挂载目录
[root@izbp11cvux96riz6fo5klaz ~]# docker inspect xiong
        "Mounts": [
            {
                "Type": "volume",
                "Name": "da623db6cf57d8e22bb7e71286570382f0d1a27cbcddc0769dfebb20088efd39",
                "Source": "/var/lib/docker/volumes/da623db6cf57d8e22bb7e71286570382f0d1a27cbcddc0769dfebb20088efd39/_data",
                "Destination": "/volume",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
# 其中Source目录的地址就是宿主机上的文件夹保存的位置,其它容器通过继承容器xiong,共享这一个Source文件
```

## Docker应用部署

```
外部机器不可以与宿主机的容器直接通信
宿主机可以与容器直接通信
外部机可以与宿主机直接通信

通过端口映射,例如宿主机的3307对应着访问容器的3306,当外部机器需要访问容器的3306端口时,访问宿主机的3307端口即可
```

### 部署mysql

```shell
# 搜索镜像
docker search mysql
# 拉取镜像
docker pull mysql:5.6
# 创建容器,设置端口映射,目录映射
#在root用户目录下创建mysql目录
#进入到mysql目录中
mkdir ~/mysql							
cd ~/mysql								

# linux中 \ 表示命令未结束,换行
#一般容器中用3306,宿主机上也用3306
#这里的$PWD代指root/mysql,这里相当于简写
#-e表示设置环境变量,取啥名都可以
docker run -id \
-p 3307:3306 \ 							
--name=c_mysql \
-v $PWD/conf:/etc/mysql/conf.d \		
-v $PWD/logs:/logs \
-v $PWD/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \			
mysql:5.6

# 登录mysql
mysql -uroot -p123456		#123456是上面设置的密码

#通过SQLyong进行连接
需要连接宿主机的端口号和设置的映射端口3307
```

### 部署tomcat

```shell

```

## Dockerfile

### Docker镜像原理

```shell
bootfs : 各个linux内核的操作系统都一样
rootfs : ubuntu和centos不一样

#docker中的镜像是分层的
tomcat镜像 -->jdk镜像 --> rootfs基础镜像 --> bootfs
#虽然tomcat只有70MB,但是它还依赖于其它父镜像和基础镜像
```

### 镜像制作

```shell
#容器转为镜像
docker commit 容器id 镜像名称:版本号
docker save -o 压缩文件名称 镜像名称:版本号
docker load -i 压缩文件名称

#这种方法会使得通过数据卷挂载的文件失效

#查看容器docker
#将容器转为镜像,只需要一部分id即可
#查看镜像,发现新增一个mycat
#将镜像文件进行压缩,压缩文件可传输
#再将压缩文件提取出来,生成镜像文件
[root@izbp11cvux96riz6fo5klaz ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
3e39f4f485b7        tomcat              "catalina.sh run"   55 seconds ago      Up 53 seconds       8080/tcp            mycat
[root@izbp11cvux96riz6fo5klaz ~]# docker commit 3e39f4 mycat:1.0
sha256:a28394e61893bdf1a060e258e652799badfc4886acb429344e4571448d5c4c0f
[root@izbp11cvux96riz6fo5klaz ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mycat               1.0                 a28394e61893        16 seconds ago      529MB
tomcat              latest              6ab907c973d2        42 hours ago        528MB
nginx               latest              ed21b7a8aee9        11 days ago         127MB
mysql               5.6                 c21620125f9e        11 days ago         303MB
centos              7                   5e35e350aded        5 months ago        203MB
hello-world         latest              fce289e99eb9        15 months ago       1.84kB

```

### Dockerfile

```shell
# Dockerfile是一个文本文件,包含一条条指令

# 自定义centos镜像案例

[root@izbp11cvux96riz6fo5klaz ~]# mkdir docker-files
[root@izbp11cvux96riz6fo5klaz ~]# cd docker-files/
[root@izbp11cvux96riz6fo5klaz docker-files]# vim centos_dockerfile

  1 
  2 FROM centos:7 		#设置父镜像,如果机器上没有会自动去下载
  3 MAINTAINER xiong <2456387473@qq.com>	#设置作者和邮箱
  4 
  5 RUN yum install -y vim		#给镜像安装vim软件
  6 WORKDIR /usr		#设置/usr为工作目录,也就是进入镜像时所在的目录
  7 
  8 cmd /bin/bash		#设置启动命令为/bin/bash


[root@izbp11cvux96riz6fo5klaz docker-files]# docker build -f ./centos_dockerfile -t xiong_centos:1 .

```

#### 部署springboot项目

```shell
[root@izbp11cvux96riz6fo5klaz docker-files]# vim springboot_dockerfile 
  2 FROM Java:8
  3 MAINAINER xiong <2456387473@qq.com>
  4 ADD springboot-hello-0.0.1-SNAPSHOT.jar app.jar #springboot-hello-0.0.1-SNAPSHOT.jar 是springboot项目通过maven打包生成的jar包
  5 CMD java -jar app.jar
[root@izbp11cvux96riz6fo5klaz docker-files]# docker build -f ./springboot_dockerfile -t app .
```

## Docker Compose的安装与使用

```shell
# 在安装docker compose之前需要先安装docker
curl -L https:github.com/docker/compose/release/download/1.22.0/docker-compose -`uname -s` -`uname -m` -o /usr/local/bin/docker-compose

# 设置文件可执行权限
chmod +x /usr/local/bin/docker-compose
# 查看版本信息
docker-compose -version
```



