# Swagger

前后端分离

+ 后端: 后端控制层, 服务层, 数据访问层
+ 前端: 前端控制层, 视图层
  + 测试阶段: 伪造后端数据, 先把工程跑起来

Swagger用于API文档的实时更新, 减少沟通协调成本

```xml
<!--        swagger用于API文档的实时更新-->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.7.0</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.7.0</version>
</dependency>
```

