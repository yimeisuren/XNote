[TOC]

```
行式数据库和列式数据库
1. OLAP分析型数据库 (列式数据库, 数据分析)
   > 计算均值, 比较大小, 求最大最小...等分析数据的时候速度快, 是将一个属性的数据存储到一起.
   > 只有取出所有的属性值之后才得到某一个对象的整体信息
2. OLTP事务型数据库(行式数据库, 增删改查)
   > 将一个个对象作为存储的单位, 在取出一个对象的所有信息的时候速度快. 只有当去除所有的对象的属性值之后才能对数据进行求均值, 比大小等等	
```

## centos7下配置redis

1. 更改redis.conf配置文件

   ```bash
   vim /etc/redis.conf
   ```

   1. 允许任何主机ip访问

      > bind 0.0.0.0

   2. 关闭保护模式

      > protected-mode no

2. 开放端口

   ```bash
   vim /etc/sysconfig/iptables
   ```

   1. 在文件中开放6379端口

      > -A INPUT -m state --state NEW -m tcp -p tcp --dport 6379 -j ACCEPT

      

## Redis-key基本命令

注 : 有时候命令没有提示, 可能是因为少输入了一个s

```bash
cd /usr/local/bin				#进入到redis软件的安装目录
redis-cli -p 6379				#开启,进入到127.0.0.1.6379>

set key_1 xiong
set key_2 22

get key_1

expire key_1 10					#设置过期时间,自动过期,cookie,session登录,缓存
ttl key_1						#查看剩余时间

move key_1 xiong				#移除当前的key,一般不这么用

keys *							#查看所有的key

exists key_1					#查看当前的key是否存在

type key_1						#查看类型

flushdb							#清空数据库
```

## 基本数据类型

### String类型

```bash
append key_1 helloworld			#向key_1后面追加"helloworld"
								#如果当前key不存在,就相当于set key_1
strlen							#获取字符串的长度

incr key_2						#加1
decr key_2						#减1
incrby key_2 10					#设置每次增加的步长为10

getrange key_1 0 3				#获取0-3的字符
getrange key_1 0 -1				#获取全部的字符串
setrange key_1 1 xxx			#用xxx替换字符串中的字符,从1号位置开始,替换数目和xxx相等

#msetnx操作是原子性的,要么一起成功,要么一起失败,但是 redis中的事务不是原子性的,可以有些成功有些失败

mset k1 v1 k2 v2 k3 v3 			#批量设置,K1 V1
mget							#获取多个值
msetnx K1 V1 K4 V4				#此时由于K1已经存在,会导致操作失败,返回0


#设置对象
set user:1{name:zhangsan,age:22}
mset user:1:name zhangsan user:1:age 12	#这里的key_1为user:1:name,表示第一个user的name属性
#获取对象
mget user:1:name user:1:age

#组合命令
getset db redis    			#第一次由于db不存在,所以输出nil,同时这个方法在输出后还会对db进行设置
getset db MongDB			#由于上一次已经设置db为redis,所以会输出redis,同时设置为MongDB

127.0.0.1:6379> setnx mykey redis		#如果mykey的值不存在,则设置为redis
(integer) 1
127.0.0.1:6379> setnx mykey MongDB		#如果mykey的值不存在,则设置为MongDB,如果存在则设置失败
(integer) 0
127.0.0.1:6379> SETEX key2 5 hello		#设置key2的值为hello,5秒后过期
OK
127.0.0.1:6379> ttl key2
(integer) 1

```

### List类型

```bash
#可以把List当成栈,队列,阻塞队列,实际上是一个链表

#所有的list命令都是以l开头的
lpush key_1 one					#向key为key_1的队列左边加上一个值one
lpop  key_1						#从左边pop出栈
rpop  key_1						#从右边pop出栈
lindex key_1 2					#通过下标获取值,相当于list[2],下标从0开始,和编程语言保持一致
llen key_1						#获取列表的长度
lrem key_1 2 xiong 				#在key_1中移除掉2个xiong,精确匹配
lrange mylist 0 -1				#相当于遍历mylist
ltrim mylist 0 3 				#相当于截断mylist,会造成mylist的改变
rpoplpush mylist myotherlist    #=rpop+lpush,从右边pop出列表中的第一个元素,并将它放入到新的列表中
								#如果myotherlist不存在,会直接新建myotherlist

linsert							#向列表中插入一个值


ex1:
127.0.0.1:6379> lpush key_1 one 1 "xiong" 
(integer) 3
127.0.0.1:6379> lrange key_1 0 -1
1) "xiong"
2) "1"
3) "one"
127.0.0.1:6379> lpop key_1
"xiong"
127.0.0.1:6379> rpop key_1
"one"
127.0.0.1:6379> lrange key_1 0 -1
1) "1"

ex2:
127.0.0.1:6379> lpush key_1 5 7 8 4 5 one right king xiong kuang sun xu
(integer) 13
127.0.0.1:6379> lrange key_1 0 -1
 1) "xu"
 2) "sun"
 3) "kuang"
 4) "xiong"
 5) "king"
 6) "right"
 7) "one"
 8) "5"
 9) "4"
10) "8"
11) "7"
12) "5"
13) "1"
127.0.0.1:6379> lindex key_1 2
"kuang"
127.0.0.1:6379> lindex key xiong
(nil)


ex3:
127.0.0.1:6379> lrange mylist 0 -1
1) "hello"
2) "hello1"
3) "hello1"
4) "hello2"
5) "hello3"
6) "hello3"
127.0.0.1:6379> ltrim mylist 00 3			#这里写00会造成错误
(error) ERR value is not an integer or out of range
127.0.0.1:6379> ltrim mylist 0 3 
OK
127.0.0.1:6379> lrange mylist 0 -1
1) "hello"
2) "hello1"
3) "hello1"
4) "hello2"
```

### set类型

```bash
#命令以s开头
#set里面没有重复的数据,属于无序不重复集合

#用途 : 共同关注,共同爱好,推荐好友...
#比如查看共同关注 : 将A关注的人放到一个集合中,B关注的一个人放到一个集合中,二者求交集

sadd myset hello					#向myset中添加元素hello
smembers	myset					#查看myset中的元素
scard myset							#获取myset集合中的元素个数
srem myset hello					#移除myset中的hello
sismember myset hello				#判断某一个值是否在myset中
srandmember myset 2					#随机获取myset中的2个元素
spop myset							#随机删除掉一个元素
smove myset myset2 hello			#将myset中的hello移到myset2中

sdiff myset myset2					#差集myset-myset2
sinter myset myset2					#交集
sunion myset myset2					#并集

```

### Zest有序集合

```bash
#排行榜,分数成绩单排名
#带权重的,例如重要消息等级设置为2,普通消息等级设置为1


zadd myset 1 one 0 two 4 three						#添加多个值,并按照给定的序号进行排序
(integer) 3
zrange myset 0 -1									#从这里可以看出,序号是从1开始,连续递增的
1) "two"
2) "one"
3) "three"

zrem	#移除


zadd salary 2500 zhangsan
(integer) 1
zadd salary 5000 lisi
(integer) 1
zadd salary 100 xiong
(integer) 1
keys *
1) "salary"
2) "myset"
zrange salary 0 -1
1) "xiong"
2) "zhangsan"
3) "lisi"

zrangebyscore salary -inf inf						#实现排序,-inf到inf表示全排序
1) "xiong"											#可以指定范围 例如 -1000 200
2) "zhangsan"
3) "lisi"
zrangebyscore salary -1000 100
1) "xiong"
zrangebyscore salary 0 100 withscores				#同时会将具体的值打印出来
1) "xiong"
2) "100"
zrevrange salary 0 -1								#从大到小排序
zcount myset 1 4									#获取myset中[1,4]的变量的个数
```



### hash类型

```bash
#hash更适合对象的存储;
#hash用来存储容易发生变更的数据,尤其是用户信息,一条field对应一个属性,例如username password age...

hset myhash field1 xiong					#设置属性和值,相当于key和value
hget myhash field1							#获取属性对应的值
hmset myhash field1 kuang field2 xiong		#设置多个属性值,并且会将同名属性的值给覆盖掉
hmget myhash field1 field2

hdel muhash field1							#删除myhash中的field1属性和对应的值
hlen myhash									#获取myhash中的长度
hexists myhash field3						#判断myhash中field3是否存在
hgetall myhash								#获取所有属性和它们对应的值
hkeys myhash								#获取所有的key
hvals myhash								#获取所有的value

myhash field3 5								
hincrby myhash field3 2						#给field3对应的值加2,此时field3对应的值为7
hdecrby myhash field3 1						#相当于+(-1)=-1
hsetnx myset field4 hello					#当field4不存在才进行设置,否则失败
```

## 三种特殊数据类型

### geospatial地理位置

```bash
#朋友的定位,附近的人,打车距离计算
#这个功能可以推算出地理位置的信息,两地之间的距离,方圆几里的人

#两级无法直接添加,一般会下载城市数据,直接通过java程序一次性导入

geoadd china:city 116.40 39.90 beijing		#添加地理位置,要注意经度和维度
geopos china:city beijing					#获取指定地理位置的经度和维度

geodist china:city beijing shanghai km      #北京到上海的直线距离(以km为单位,默认是以m为单位)
georadius china:city 110 30 1000 km			#以110 30为中心,半径为1000km,在集合china:city中查找符合要求的城市

georadius china:city 110 30 500 km withdist				#带上这些城市到这个中心点的实际距离
georadius china:city 110 30 500 km withcoord			#带上这些城市的经度和纬度

georadius china:city 110 30 500 km withdist count 5 	#指定查找数量为5,则最多只显示5个

georadiusbymember chian:city beijing 500 km withdist	#用beijing来替代经度和纬度坐标

#底层是Zset,所以可以用Zset中的命令来对Geospatial进行操作
```

### Hyperloglog基数

```bash
#基数:不重复的元素,可以接收误差
#比如用来统计网页的浏览量,同一个id访问多次只能算作一次

#传统方法:用set保存用户id,由于set不能有重复的数据,所有只会算作是一次
#缺点: 这样保存了大量的用户id,比较占用资源

hypeloglog: 只需要12kB的内存,但有0.83%的错误率,需要允许一定的容错,专业解决计数问题

127.0.0.1:6379> pfadd mykey a b x k ds  kf f kfk
(integer) 1
127.0.0.1:6379> pfadd mykey a d d d fj f j ks d e d v x c 
(integer) 1
127.0.0.1:6379> pfcount mykey
(integer) 15
127.0.0.1:6379> pfadd mykey2 saj fsa f ds kdk sd sde g  gds x f
(integer) 1
127.0.0.1:6379> pfcount mykey2
(integer) 10
127.0.0.1:6379> PFMERGE mykey3 mykey mykey2
OK
127.0.0.1:6379> PFCOUNT mykey3
(integer) 22

```

### Bitmaps位存储

```bash
#用来解决只有两个状态的数据的存储,是否,有无....

#设置
127.0.0.1:6379> setbit sign 0 1
(integer) 0
127.0.0.1:6379> setbit sign 1 0
(integer) 0
127.0.0.1:6379> setbit sign 2 1
(integer) 0
127.0.0.1:6379> setbit sign 3 0 
(integer) 0
127.0.0.1:6379> setbit sign 4 1 
(integer) 0
127.0.0.1:6379> setbit sign 5 0
(integer) 0
127.0.0.1:6379> setbit sign 6 0
(integer) 0

#查看
127.0.0.1:6379> getbit sign 3
(integer) 0
127.0.0.1:6379> getbit sign 2
(integer) 1

#查看打开的天数
127.0.0.1:6379> bitcount sign
(integer) 3
```

## 事务

```bash
#Redis单条命令是保证原子性,但是事务是不保证原子性的
#Redis事务没有隔离级别的概念

#redis的事务:
	开启事务 : multi
	命令入队
	执行事务 : exec
	
#正常流程
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> get k1
QUEUED
127.0.0.1:6379> exec
1) OK
2) OK
3) "v1"
#执行完事务后,这组事务就不存在了,后面使用的时候需要重新开启


#放弃事务
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 2
QUEUED
127.0.0.1:6379> set k2 3
QUEUED
127.0.0.1:6379> set k3 k
QUEUED
127.0.0.1:6379> set k4 5
QUEUED
127.0.0.1:6379> discard
OK
#此时再去获取k1,k2,k3,k4,就已经不存在了
127.0.0.1:6379> get k4
(nil)

```

## 乐观锁

`watch`相当于mysql中的version, 如果在执行前另外一个线程修改了乐观锁中的值, 则会执行失败

`unwatch`发现事务执行失败后, 需要解锁后再执行