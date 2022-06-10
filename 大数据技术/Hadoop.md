[TOC]



# Hadoop介绍



## Hadoop是什么

+ Hadoop是由Apache基金会所开发的<u>分布式系统基础架构</u>
+ 主要解决<u>海量数据的存储和分析计算</u>问题
+ Hadoop通常是指<u>Hadoop生态圈</u>

## Hadoop优势

+ 高可靠性

  通过数据冗余存储实现可靠性

+ 高扩展性

  在双十一等高并发的场景下动态添加服务器, 之后可以删除服务器

+ 高效性

  在MapReduce的思想下, Hadoop是<u>并行工作</u>的, 以加快任务处理速度

+ 高容错性

  当任务计算失败时, 会自动重新分配任务

## <u>Hadoop组成(面试重点)</u>

<img src="https://namebucket.oss-cn-beijing.aliyuncs.com/img/Hadoop%E7%BB%84%E6%88%90.svg" style="zoom:25%;" />

### HDFS架构概述

HDFS是Hadoop Distributed File System, 即<u>Hadoop分布式文件系统</u>

<u>NameNode(nn):</u> 用来记录数据存储在哪些服务器上, 即保存数据存储位置的服务器

<u>SecondeNameNone(2nn):</u> 辅助NameNode完成工作, 对NameNode服务器每隔一段时间进行<u>备份</u>

<u>DataNode(dn):</u> 用来实际存储数据, 即用来存储数据的服务器



### Yarn架构概述

<img src="https://namebucket.oss-cn-beijing.aliyuncs.com/img/Yarn%E6%9E%B6%E6%9E%84.svg" style="zoom:20%;" />

+ 客户端可以有多个
+ ResourceManager用于管理整个集群的资源(CPU和内存)
+ NodeManager用于管理每台服务器上的资源
+ <u>每台服务器上可以虚拟化出来多个Container, 用户实际获得的是Container容器</u>, 在Container中由ApplicationMaster进行管理

### MapReduce架构概述

MapReduce将计算过程分为两个阶段: Map和Reduce

1. Map阶段并行处理输入数据
2. Reduce阶段对Map结果进行汇总

<img src="https://namebucket.oss-cn-beijing.aliyuncs.com/img/MapReduce%E6%9E%B6%E6%9E%84.svg" style="zoom:20%;" />



### HDFS,Yarn和MapReduce三者之间的关系

//todo



## 大数据生态体系

![](https://namebucket.oss-cn-beijing.aliyuncs.com/img/%E5%A4%A7%E6%95%B0%E6%8D%AE%E4%BD%93%E7%B3%BB.svg)

## 推荐系统项目架构



# Hadoop运行环境搭建(开发重点)

## 模板虚拟机环境准备

1. [VM下Centos7安装配置][Centos7安装]

2. 克隆完整模板虚拟机,修改`hostname`和`ifcfg-ens32`配置文件中的IPADDR

3. 配置环境变量

   ```sh
   #JAVA_HOME
   export JAVA_HOME=/opt/module/jdk1.8.0_212
   # $PATH表示引用变量
   # :表示拼接字符串
   # export表示定义全局变量, 可以让其它文件使用
   export PATH=$PATH:$JAVA_HOME/bin
   ```

   

4.  



## 解压并配置环境变量

1. 创建新文件夹`mkdir opt/module`
2. 删除文件夹`rm -rf <filename>`
3. 



##### 解压文件到指定文件夹

```shell
tar -zxvf hadoop.tar.gz -C opt/modules/
```

##### 获取文件的路径

```shell
pwd hadoop
```

##### 添加环境变量

```shell
vim /etc/profile

HADOOP_HOME=/opt/modules/hadoop-3.2.1
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
```

##### 刷新环境变量设置

```shell
source /etc/profile
```

## Hadoop目录介绍

```shell
[root@izbp11cvux96riz6fo5klaz hadoop-3.2.1]# ls
bin  etc  include  lib  libexec  LICENSE.txt  NOTICE.txt  README.txt  sbin  share
```

sbin : 存放hadoop启动和停止的命令(重点)

```shell
[root@izbp11cvux96riz6fo5klaz hadoop-3.2.1]# cd sbin
[root@izbp11cvux96riz6fo5klaz sbin]# ls
distribute-exclude.sh    start-all.sh         stop-balancer.sh
FederationStateStore     start-balancer.sh    stop-dfs.cmd
hadoop-daemon.sh         start-dfs.cmd        stop-dfs.sh
hadoop-daemons.sh        start-dfs.sh         stop-secure-dns.sh
httpfs.sh                start-secure-dns.sh  stop-yarn.cmd
kms.sh                   start-yarn.cmd       stop-yarn.sh
mr-jobhistory-daemon.sh  start-yarn.sh        workers.sh
refresh-namenodes.sh     stop-all.cmd         yarn-daemon.sh
start-all.cmd            stop-all.sh          yarn-daemons.sh

```



bin

```shell
[root@izbp11cvux96riz6fo5klaz hadoop-3.2.1]# cd bin
[root@izbp11cvux96riz6fo5klaz bin]# ls
container-executor  hadoop  hadoop.cmd  hdfs  hdfs.cmd  mapred  mapred.cmd  oom-listener  test-container-executor  yarn  yarn.cmd

#介绍
dhfs :
hadoop : 管理hadoop集群
yarn : 管理资源调度
```

etc : 配置文件

```shell
[root@izbp11cvux96riz6fo5klaz hadoop-3.2.1]# cd etc
[root@izbp11cvux96riz6fo5klaz etc]# ls
hadoop
[root@izbp11cvux96riz6fo5klaz etc]# cd hadoop
[root@izbp11cvux96riz6fo5klaz hadoop]# ls
capacity-scheduler.xml            kms-log4j.properties
configuration.xsl                 kms-site.xml
container-executor.cfg            log4j.properties
core-site.xml                     mapred-env.cmd
hadoop-env.cmd                    mapred-env.sh
hadoop-env.sh                     mapred-queues.xml.template
hadoop-metrics2.properties        mapred-site.xml
hadoop-policy.xml                 shellprofile.d
hadoop-user-functions.sh.example  ssl-client.xml.example
hdfs-site.xml                     ssl-server.xml.example
httpfs-env.sh                     user_ec_policies.xml.template
httpfs-log4j.properties           workers
httpfs-signature.secret           yarn-env.cmd
httpfs-site.xml                   yarn-env.sh
kms-acls.xml                      yarnservice-log4j.properties
kms-env.sh                        yarn-site.xml
```

lib : 本地库

```shell
[root@izbp11cvux96riz6fo5klaz hadoop-3.2.1]# cd lib
[root@izbp11cvux96riz6fo5klaz lib]# ls
native
[root@izbp11cvux96riz6fo5klaz lib]# cd native
[root@izbp11cvux96riz6fo5klaz native]# ls
examples          libhadoop.so        libhdfs.a         libnativetask.a
libhadoop.a       libhadoop.so.1.0.0  libhdfs.so        libnativetask.so
libhadooppipes.a  libhadooputils.a    libhdfs.so.0.0.0  libnativetask.so.1.0.0

```

share : 官方提供的案例和说明文档

## Hadoop正式开始使用

```shell
#首先进入hadoop安装目录 cd opt/modules/hadoop-3.2.1
#在该目录下创建一个input文件夹
#将etc/hadoop/*.xml的文件移动到input文件夹中(正则表达式)
[root@izbp11cvux96riz6fo5klaz hadoop-3.2.1]# mkdir input
[root@izbp11cvux96riz6fo5klaz hadoop-3.2.1]# cp etc/hadoop/*.xml input

```

配置ssh免密登录

```shell
  $ ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
  $ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
  $ chmod 0600 ~/.ssh/authorized_keys
```

经典案例

```shell
hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.1.jar grep input/ output 'dfs[a-z.]+'
#输出符合指定正则表达式的结果,保存到output文件夹中,并且outpue文件事先不能存在
```

```shell
[root@izbp11cvux96riz6fo5klaz hadoop-3.2.1]# mkdir wcinput
[root@izbp11cvux96riz6fo5klaz hadoop-3.2.1]# cd wcinput
[root@izbp11cvux96riz6fo5klaz wcinput]# touch wc.input
[root@izbp11cvux96riz6fo5klaz wcinput]# vim wc.input
[root@izbp11cvux96riz6fo5klaz wcinput]# cd ..
[root@izbp11cvux96riz6fo5klaz hadoop-3.2.1]# hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.1.jar wordcount wcinput wcoutput
[root@izbp11cvux96riz6fo5klaz hadoop-3.2.1]# cat wcoutput/part-r-00000 

#输出结果(取决于自己在vim编辑的时候输入的内容)
hadoop	2
mapreduce	1
yarn	1
```

伪分布配置步骤

```shell
#java环境变量配置
vim /etc/profile
JAVA_HOME=/usr/java/jdk1.8.0_121
JRE_HOME=${JAVA_HOME}/jre
CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH

#另一个需要配置java的文件
vim ~/.bashrc
#在这个文件的最上面添上
export JAVA_HOME=/usr/java/jdk1.8.0_121
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH

#相当于刷新,让刚才的配置生效
source /etc/profile
source ~/.bashrc

#初始化命令
[root@izbp11cvux96riz6fo5klaz bin]# hdfs namenode -format

#dfs.sh文件在文件顶部添加上这个
HDFS_DATANODE_USER=root
HADOOP_SECURE_DN_USER=hdfs
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root

#yarn.sh
YARN_RESOURCEMANAGER_USER=root
HADOOP_SECURE_DN_USER=yarn
YARN_NODEMANAGER_USER=root

#执行如下命令
[root@izbp11cvux96riz6fo5klaz sbin]# start-dfs.sh

#检测是否成功启动
[root@izbp11cvux96riz6fo5klaz hadoop-3.2.1]# jps
10896 SecondaryNameNode
11041 Jps
10549 NameNode
17493 
10678 DataNode

#在远程服务器上创建一个目录/opt/modules/hadoop-3.2.1/
[root@izbp11cvux96riz6fo5klaz hadoop-3.2.1]# ./bin/hdfs dfs -mkdir -p /opt/modules/hadoop-3.2.1/

```





[Centos7安装]:Centos7安装
