# Mybatis基础

**SQL Maps**：数据库对象和Java对象之间的映射

Mybatis封装JDBC的过程

ORM（Object Relation Mapping）：实体关系映射，Java对象和SQL对象之间的映射。

Mybatis中有两个配置文件：

+ **核心配置文件**`mybatis-config.xml`主要用于连接数据库的环境和Mybatis的全局配置信息
+ **映射配置文件**专门编写SQL语句, 同时使用id标识java中的方法, 用resultType表示sql返回对象对应java中的类型

框架的本质：jar包+配置文件

**mapper接口**：仅仅作为接口，而不需要提供实现类

按照mybatis中的方法去调用mapper接口，就会自动去执行对应的SQL语句，不需要实现类



1. 配置UserMapper.xml的namespace：实现UserMapper接口到UserMapper.xml文件的映射
2. UserMapper接口中的方法和UserMapper.xml中SQL语句的id标签相对应



sqlSession：java程序和数据库之间的会话，相当于一个事务，在执行完sql语句之后需要提交事务sqlSession.commit

httpSession：java程序和http页面之间的会话



配置文件之后都会交给SpringBoot进行管理



## log4j

使用日志文件来监测sql执行的情况

```xml
<!--添加依赖-->

```



## Mybatis获取值

`${}`和`#{}`对应MyBatis的两种赋值方式，

+ 前者表示拼接，不会自动添加单引号
+ 后者表示占位符, 会自动添加单引号

一般来说，可以使用#{}的情况下就不要使用${}，因为${}有sql注入的风险，但有时候可能必须得使用${}方式进行字符串拼接

模糊查询中不能使用#{}, 因为使用?进行占位符的表示, 但是由于模糊查询需要使用单引号''将?包围, 因此?会被解释成一个字符, 而不是占位符, 可以使用双引号"%"#{}"%"来处理模糊查询

批量删除的时候只能使用${}而不能使用#{}

### 单个参数

实际上{}里面的变量名根本不重要, 可以取任意名字, 不一定要和传入的参数保持一致, 但那样肯定是不规范的

${}本质上是字符串的拼接, 在sql语句中在拼接字符串需要使用单引号`''`, 因此一般使用`'${}'`

### 多个参数

多个参数会存储成一个map, 传入的参数作为map的值, 并且有一些固定名称的键名, arg0, arg1, arg2, ..., 需要通过键去获取到map中的值

#{arg0}, #{arg1}

### 传入map

键名可以自定义, 名称不再固定为arg0和arg1

### 传入对象

可以直接使用对象中的属性名作为变量, 但需要注意的是这里的属性只表示提供了get()和set()方法的那些属性

### 使用@Param注解

在Mapper接口的方法中, 对传入的形参添加@Param('xxx')注解. 可以使用@Param注解的值作为键, 也可以使用param1, param2, ...等作为键

可以分为两类, 除实体类对象之外, 其他都可以使用@Param注解方式



**查询对象的返回值可能有多条, 因此对Mapper接口的定义最好统一定义成返回值为List**

可能查询结果只有某几个属性, 或者由多张表的属性构成, 此时不能找到一个Mapper接口和结果直接对应, 需要返回一个map, 其中键为属性名, 值为属性值. 如果有多条返回数据, 则对应一个map集合, 实际上map集合就是json对象, 二者在逻辑上都属于关系模型

如果使用**@MapKey()**注解, 相当于Map<key , Map<String, Object>>, 强行添加了一个key构成map



使用useGeneratedKey="true"表示使用自动递增的主键, 使用keyProperty="id"表示递增的为属性列id

mybatis-config.xml中可以通过setting标签设置全局懒加载策略, 如果对于某个具体的属性想要设置立即加载, 可以在mapper映射表中添加fetchType="eager"进行调整
