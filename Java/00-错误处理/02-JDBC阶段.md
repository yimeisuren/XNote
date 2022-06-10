# JDBC阶段错误处理

**MySQL 8.0+版本**

## 问题一

### 问题描述

在进行数据库连接时, 连接信息都已经"正确"配置, 结果报错

`java.sql.SQLException: Access denied for user '24563'@'localhost' (using password: YES)`

### 原因分析

之前设置的`url`如下

```java
String url = "jdbc:mysql://localhost:3306/jdbc";
```

而错误信息中的`24563`是我windows系统中的用户名, 并且我的数据库中只有`root`用户, 因此可能是因为没有在数据库前指定用户名而默认使用当前windows系统中的用户名. 

### 错误处理

修改`url`如下, 指定数据库的用户

```java
String url = "jdbc:mysql://root@localhost:3306/jdbc";
```

