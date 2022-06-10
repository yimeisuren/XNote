# Zookeeper安装

## 安装

1. 安装java环境`yum install -y java`
2. 下载zookeeper压缩包并解压到`/opt/module`目录下
   + 解压缩命令`tar zxvf`
   + 移动文件命令`mv`

## 配置

1. 将<u>zookeeper目录下</u>的配置文件目录`/opt/module/zookeeper-3.5.7/conf`中的配置文件`zoo_sample.cfg`重命名为`zoo.cfg`
2. 修改配置文件`zoo.cfg`中的数据保存路径, 添加`dataDir=/opt/module/zookeeper-3.5.7/zookeeperData`, 并创建相应目录

## 配置文件参数解释

```properties
# 心跳时间2000ms, 即2s
tickTime=2000
# 集群开始启动时的通信时间, 10个心跳时间, 即10*2=20s
initLimit=10
# 集群正常启动之后的通信时间, 5个心跳时间, 即5*2=10s
syncLimit=5
# 存储数据的地方
dataDir=/opt/module/zookeeper-3.5.7/zookeeperData
# the port at which the clients will connect
clientPort=2181
```



## 启动

1. <u>启动文件</u>`/opt/module/zookeeper-3.5.7/bin/zkServer.sh`, 启动需要在文件后面添加`start` 

2. 使用jps来查看进程是否启动

   如果显示`jps command not found`, 则需要使用`yum install -y java-1.8.0-openjdk-devel `

   ```bash
   [root@localhost zookeeper-3.5.7]# jps
   1835 QuorumPeerMain
   2141 Jps
   ```

   

3. <u>查看</u>Zookeeper状态, 在`zkServer.sh`文件后面添加`status`

   ```bash
   [root@localhost zookeeper-3.5.7]# bin/zkServer.sh status
   /usr/bin/java
   ZooKeeper JMX enabled by default
   Using config: /opt/module/zookeeper-3.5.7/bin/../conf/zoo.cfg
   Client port found: 2181. Client address: localhost.
   Mode: standalone#standalone表示单机模式
   ```

   

4. <u>启动客户端</u>, 执行`zkCli.sh`文件即可

5. <u>退出客户端</u>`quit`

6. <u>停止</u>Zookeeper, 在`zkServer.sh`文件后面添加`stop`

# Zookeeper分布式实战(开发重点)

## 分布式安装部署

1. 集群规划

   确定在哪些服务器上安装部署Zookeeper

2. 解压安装, 并同步到其它服务器上

   + 同步操作`xsync zookeeper-3.5.7`

3. 配置服务器编号

   1. 创建`zookeeperData`目录
   2. 在该目录下创建`myid`文件
   3. 在文件中添加与服务器相对应的编号, 例如server-1添加1, server-2添加2
   4. 同步(内容完全复制)到其它服务器上, 并修改文件中对应的内容

4. 配置`zoo.cfg`文件

   1. 修改数据存储文件地址

   2. 添加如下涉及集群的配置`server.A=B:C:D`

      + A: 表示第几号服务器
      + B: 服务器的ip地址
      + C: 服务器与集群中的Leader服务器交换信息的端口号
      + D: 如果Leader服务器挂了, 需要使用一个端口号来重新进行选举新的Leader, 该端口号用于重新选举Leader服务器

      ```properties
      #2,3,4分别对应上面的myid文件中设置的值
      #zookeeper2, zookeeper3, zookeeper4分别对应3台服务器的名字
      server.2=zookeeper2:2888:3888
      server.3=zookeeper3:2888:3888
      server.4=zookeeper4:2888:3888
      ```

      

## 客户端执行命令

1. 查看有哪些可执行命令`help`

   ```bash
   [zk: localhost:2181(CONNECTED) 0] help #输入help命令, 显示可用命令
   ZooKeeper -server host:port cmd args
   	addauth scheme auth
   	close 
   	config [-c] [-w] [-s]
   	connect host:port
   	# 创建节点, 默认情况下创建持久化节点, 
   	# 使用-e表示创建临时节点. 临时节点在客户端退出(断开连接)后清除
   	# 使用-s表示创建带有序号的节点
   	create [-s] [-e] [-c] [-t ttl] path [data] [acl]
   	delete [-v version] path
   	deleteall path
   	delquota [-n|-b] path
   	# 默认情况获取节点的值
   	# 使用-w表示监听该节点的值, 当节点的值发生变化时会接收到通知, 但是是一次性的, 接收到通知后需要重新设置监听
   	get [-s] [-w] path
   	getAcl [-s] path
   	history 
   	listquota path
   	ls [-s] [-w] [-R] path
   	ls2 path [watch]
   	printwatches on|off
   	# 退出客户端
   	quit 
   	reconfig [-s] [-v version] [[-file path] | [-members serverID=host:port1:port2;port3[,...]*]] | [-add serverId=host:port1:port2;port3[,...]]* [-remove serverId[,...]*]
   	redo cmdno
   	removewatches path [-c|-d|-a] [-l]
   	# 递归删除节点
   	rmr path
   	# 修改节点的值
   	set [-s] [-v version] path data
   	setAcl [-s] [-v version] [-R] path acl
   	setquota -n|-b val path
   	stat [-w] path
   	sync path
   Command not found: Command not found help
   ```



## API应用







# Zookeeper内部原理

## 选举机制(面试重点)

Zookeeper虽然在配置文件中并没有指定Master和Slave, 但是在Zookeeper工作时, <u>有一个节点为Leader, 其它节点为Follower</u>, Leader是通过内部的选举机制临时产生的.

### <u>半数机制</u>

集群中半数以上机器存活, 集群可用. 所以Zookeeper适合安装奇数台服务器.

### 选举过程

每台服务器一开始先选择自己, 当发现自己所得票数过半时, 成为Leader. 如果自己票数不超过一半, 则下次投给id最大的服务器. 因此成为Leader的服务器是<u>第(n/2 + 1)台服务器</u>.

## 节点类型

### 持久化节点(Persistent)

客户端和服务器断开连接后, 创建的节点不会被删除, 用来保存客户端信息.

<u>持久化目录节点</u>



<u>持久化顺序编号目录节点</u>

Zookeeper为节点名称进行顺序编号, 即添加顺序标识, 由父节点维护. 在分布式系统中, 顺序号可以被用于为所有的事件进行全局排序, 这样客户端可以通过顺序号推断事件的顺序.



### 短暂节点(Ephemeral)

客户端和服务器断开连接后, 创建的节点会被删除

<u>临时目录节点</u>



<u>临时顺序编号目录节点</u>

## Stat结构体

在zookeeper客户端中输入`stat <path>`即可查看`path`路径对应节点的Stat结构体信息

```bash
[zk: localhost:2181(CONNECTED) 1] stat / # 查看/根节点的Stat结构体
cZxid = 0x0 #创建节点的事务zxid
ctime = Thu Jan 01 08:00:00 CST 1970 #被创建的毫秒数(从1970年开始)
mZxid = 0x0 #最后更新的事务zxid
mtime = Thu Jan 01 08:00:00 CST 1970 #最后修改的毫秒数(从1970年开始)
pZxid = 0x0 #最后更新的子节点
cversion = -1 #子节点变化号
dataVersion = 0 #数据变化号
aclVersion = 0 #访问控制列表的变化号
ephemeralOwner = 0x0 #如果是临时节点,这个是znode拥有session id。如果不是临时节点则是0
dataLength = 0 #数据长度
numChildren = 1 #子节点数量
```

## 监听器原理(面试重点)

1. 首先要有一个main线程
2. 在main线程中创建<u>Zookeeper客户端</u>, 这时会创建两个线程, <u>一个负责网络的连接通信(connect), 一个负责监听(listener)</u>
3. 通过connect线程将注册的<u>监听事件发送给Zookeeper服务器</u>
4. 在Zookeeper服务器的<u>注册监听器列表中添加监听事件</u>
5. Zookeeper服务器监听到路径或数据发生变化, 会将消息发送到Zookeeper客户端的listener线程
6. listener线程<u>调用相应的process()方法</u>, process方法由程序员自定义业务处理逻辑

## 写数据流程

1. 客户端向<u>某一个服务器A</u>发送写数据请求
2. 该服务器向Leader服务器转发该请求
3. Leader服务器向所有Zookeeper服务器广播该请求
4. Follower服务器接收到Leader服务器的写请求后, 执行写操作, 成功后返回状态
5. 当Leader服务器接收到超过半数以上的服务器写成功后, 向服务器A响应写成功
6. 服务器A向客户端响应写成功

<img src="https://namebucket.oss-cn-beijing.aliyuncs.com/img/%E5%86%99%E6%95%B0%E6%8D%AE%E6%B5%81%E7%A8%8B.svg" style="zoom:25%;" />

