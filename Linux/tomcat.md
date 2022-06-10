# tomcat问题解决汇总(centos7)

| 出现的问题                                   | 解决方案                                                     | 链接                                                         |
| -------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 使用yum安装tomcat                            | 1.官网找到安装包的超链接, 右键选择复制链接地址<br />2.在centos中使用wget命令, 例如wget 链接地址<br />3.在/usr/share中可以找到应用程序, 解压安装 |                                                              |
| 如何启动tomcat                               | 1.进入到tomcat程序的bin目录<br />2.使用./startup.sh启动tomcat服务 |                                                              |
| ./startup.sh出现权限不够                     | 在bin目录下执行chmod u+x *.sh命令                            | 1. chmod是权限管理命令change the permissions mode of a file的缩写<br />2. u代表所有者，x代表执行权限。 + 表示增加权限。<br />3. chmod u+x file.sh 就表示对当前目录下的file.sh文件的所有者增加可执行权限 |
| 配置两台tomcat服务器, 并修改端口为8080和8081 | 1.进入到tomcat的配置文件server.xml<br />2.将port=8080的改为8081, 并将另外两处port的端口号进行修改(这里可以不是8081)<br />3.配置防火墙开放端口,或者关闭防火墙systemctl stop firewalld |                                                              |
|                                              |                                                              |                                                              |

