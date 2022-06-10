mysql内核

sql优化

mysql服务器优化

各个参数常量设定

查询语句优化

主从复制

软硬件升级

容灾备份

sql编程



# MySQL在Linux下的安装

## rpm安装方式

+ 进入mysql的下载地址https://downloads.mysql.com/archives/community/
+ 选择版本
  
+ 下载32位的服务端server和客户端client
  

## 启动mysql

+ 启动mysql服务 

  ```shell
  service mysql start
  或
  systemctl start mysqld.service
  ```

+ 查看mysql的初始密码

  ```shell
  grep "password" /var/log/mysqld.log
  ```

+ 登录数据库

  ```shell
  mysql -uroot -p
  ```

+ 修改mysql的默认密码

  > **说明** 新密码设置的时候如果设置的过于简单会报错，必须同时包含大小写英文字母、数字和特殊符号中的三类字符。

  ```shell
  ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
  ```

+ 执行如下命令，创建wordpress库

  ```shell
  create database wordpress; 
  ```

+ 查看是否创建成功

  ```shell
  show database
  ```

+ 输入`exit`退出数据库

修改MySQL配置文件(拷贝一份,不修改原配置文件)

```shell
cp my-huge.cnf /etc/my.cnf #5.5版本
cp my-default.cnf /etc/my.cnf #5.6版本配置文件名称不一样
```

如果先建表, 再配置文件改为utf-8, 还是会导致乱码

# 配置文件介绍

+ 二进制日志文件log-bin(主从复制)

+ 错误日志log-error
  默认是关闭, 记录严重的警告和错误信息, 每次启动和关闭的详细信息 

+ 查询日志log

  默认关闭, 记录查询的sql语句,如果开启会降低mysql的整体性能,

+ 数据文件
  myi文件(存放表索引)
  myd文件(存放表数据)

  myfrm(存放表结构)

+ 配置文件路径(linux下是在/etc/my.cnf, 而windows下是my.ini)

# 索引

提高了查询速度,但同时提高了增删改的成本,因为需要维护B+树这种数据结构

不需要建立索引的情况

> 1. 表记录太少的时候
> 2. 经常增删改的表
> 3. 某个数据列包含许多重复的内容,那这个表字段就没有必要建立索引

# explain作用

能干嘛?

> 1. 表的读取顺序
> 2. 数据读取操作的操作类型
> 3. 哪些索引可以使用
> 4. 哪些索引被实际使用
> 5. 表之间的引用
> 6. 每张表有多少行被优化器查询



字段解释:

1. id

   > id越大的越先执行,id相同的从上到下执行

2. table

   > derived2表示的就是id为2的表的衍生表,相当于临时变量

3. select_type

4. type(重点)

   > 最好到最差: system>const>eq_ref>ref>range>index>ALL
   >
   > 一般来说,得保证查询至少达到range级别,最好能达到ref,再往上比较理想,实际情况不太可能实现
   >
   > ref:
   >
   > range:
   >
   > index:全索引扫描
   >
   > all:全表扫描

5. possible_keys和key

   > possible_keys: 理论上可能用到的索引
   >
   > key: mysql实际使用的索引,如果为null则表示索引失效,没有使用索引; 如果查询中使用了覆盖索引,则该索引仅仅出现在key列表中

6. key_len

   > key_len显示的值为索引字段的最大可能长度,==并非实际使用长度==
   >
   > 在不损失精确度的情况下,长度越短越好

7. ref

   > 显示索引的哪一列被使用,可以的话最好是常量const

8. rows

   > 大致估算出找到所需的记录所需要读取的行数

9. Extra(重点)

   > 1. Using filesort:尽可能优化sql, 这是一个糟糕的信息
   > 2. Using temporary: 比上面的更糟糕,使用了临时表保存中间结果
   > 3. Using index: 效率不错
   >    1. 如果同时出现Using where表明索引被用来执行索引键值的查找
   >    2. 如果没有同时出现Using where表明索引用来读取数据而非执行查找动作
   > 4. Using where: 表明使用了where过滤
   > 5. using joing buffer: 使用了连接缓存,当sql语句中的join过多时,可以调大配置文件中的joining buffer
   > 6. impossible where: where子句的值是false, 相当于逻辑错误
   > 7. distinct: 找到第一匹配的元组后即立即停止找同样值得动作

# 索引优化

1. 单表索引优化案例

   > range类型的索引后面的索引失效 where ==id>1==
   >
   > id = 1 and ==name_length>2 order by name==这部分失效 

2. 双表索引优化案例

3. 三表索引优化案例

# 解决like   %数据%   失效的问题

使用覆盖索引