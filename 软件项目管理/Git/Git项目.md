# Git

## 配置ssh免密登录

右键进入git.bash

输入ssh-keygen -t rsa -C "xxxx@xxxx.com"

连续回车

输入cat ~/.ssh/id_rsa.pub

得到公钥后,登录gitee,载设置里面找到SSH KEY,将公钥粘贴进去

使用ssh -T git@gitee.com测试是否配置成功

![image-20200508090321305](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200508090321305.png)

## 码云上新建仓库

点击右上角的 + ,新建仓库

设置模板为maven, 模型为生产/开发模型

## idea导入项目

新建maven项目

file--->new--->project from version control-->git

从码云上复制项目地址

建立spring子项目, 设置如下

![image-20200508091650167](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200508091650167.png)

选择微服务的必要组件

![image-20200508091832556](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200508091832556.png)

这里暂时先不要点击, 直接叉掉即可

![image-20200508092023853](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200508092023853.png)

在任意一个子项目中复制一个pom文件粘贴到gulimall的总项目中, 作适当修改

![image-20200508093833847](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200508093833847.png)

删除<parent>和<dependencies>,添加<packaging>和<modules>

![image-20200508094204558](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200508094204558.png)

最终pom文件如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.xiong.gulimall</groupId>
    <artifactId>gulimall</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>gulimall</name>
    <description>聚合服务</description>
    <packaging>pom</packaging>
    <modules>
        <module>gulimall-coupon</module>
        <module>gulimall-member</module>
        <module>gulimall-order</module>
        <module>gulimall-product</module>
        <module>gulimall-ware</module>
    </modules>
</project>

```

点击进入maven, 刷新项目

再点击maven中的+,将总项目的pom.xml文件添加进来,此时pom.xml会出现maven项目的标志

## 设置忽略垃圾文件

配置.gitignore文件

```
**/mvnw
**/mvnw.cmd
**/.mvn
**/target/

.idea

**/.gitignore
```

## 提交文件给码云

alt+9(Git)

![image-20200508100145472](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200508100145472.png)

在这上面右键点击commit file, 再在弹出的窗口commit and push

## 设置Docker容器自启动

```shell
docker update mysql --restart=always
```

## 设置Nodejs的淘宝镜像

```shell
npm config set registry https://registry.npm.taobao.org
不想用他们道的，再设置回原来内的就可以容了：
npm config set registry https://registry.npmjs.org
```

## 全局安装Vue的工具

```shell
npm install -g @vue/cli-init
npm install -g webpack
```

