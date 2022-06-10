ip地址配置

主机名配置

1. `vim /etc/hostname`修改主机名

2. `vim /etc/hosts`修改主机名映射, 相当于为主机ip地址定义一个变量, 一般使用主机名

   ```properties
   192.168.67.100 hadoop100
   192.168.67.102 hadoop102
   192.168.67.103 hadoop103
   192.168.67.104 hadoop104
   192.168.67.105 hadoop105
   192.168.67.106 hadoop106
   ```

3. 修改Windows系统中的`host文件`, 实现主机映射. 之后在Xshell等软件中可以使用hadoop100等价于使用ip地址. 相当于在本地实现域名到主机的映射.

   ```properties
   192.168.67.100 hadoop100
   192.168.67.102 hadoop102
   192.168.67.103 hadoop103
   192.168.67.104 hadoop104
   192.168.67.105 hadoop105
   192.168.67.106 hadoop106
   ```

4. 使用`yum install -y epel-release`安装`epel-release`, 相当于一个额外的软件仓库, 大多数rpm包在官方的仓库中找不到

5. 使用`yum install -y net-tools`安装`net-tool`工具包集合

6. 关闭防火墙`systemctl stop firewalld`, 关闭防火墙开机自启动`systemctl disable firewalld.service`

