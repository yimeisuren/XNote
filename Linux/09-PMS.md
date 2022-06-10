# 包管理系统PMS(package management system)

## 包管理基础

PMS使用一个数据库来记录各种相关内容:

+ Linux系统上<u>已经安装了什么软件包</u>
+ <u>每个包安装了什么文件</u>
+ 每个已安装软件包的<u>版本</u>

软件包存储在服务器上, 这些服务器称为<u>仓库(repository)</u>. 可以通过Linux系统中的PMS来下载或更新软件包.

## 基于Red Hat系统

### 列出已安装的包

+ 使用`yum list installed`在控制台输出, 或者使用`yum list installed > <filename>`<u>重定向输出</u>到文件中

+ 使用`yum list installed <packageName>`查看指定包名`<packageName>`的包是否安装

+ 使用`yum provides <filename>`查看`<filename>`文件属于哪个软件包

### 安装软件

+ 使用`yum install <packageName>`从仓库中下载软件包并安装

+ 也可以下载`rpm`安装包, 使用`yum localinstall <packageName.rpm>`进行本地安装

### 更新软件

+ 使用`yum list updates`列出所有已安装软件包的可用更新
+ 使用`yum update`更新所有安装包
+ 使用`yum update <packageName>`更新`<packageName>`软件包

### 卸载软件

+ 使用`yum remove <packageName>`卸载软件, 但保留配置文件和数据文件
+ 使用`yum erase <packgeName>`卸载软件并删除和它相关的所有文件

### 处理损坏的包依赖关系

在安装多个软件包时, 某个包的软件依赖关系可能会被另一个包的安装覆盖掉, 这叫做<u>损坏的包依赖关系(broken dependency)</u>.

如果系统出现该问题

1. 使用`yum clean all`进行尝试
2. 再使用`yum update`进行尝试
3. 最后使用`yum deplist <packageName>`查看包依赖关系, 知道依赖关系后可以手动进行安装
4. 如果还是无法解决, 使用`yum update --skip-broken`来跳过更新那些包依赖关系损坏的包而继续更新其它包

### 软件仓库

yum的仓库定义文件位于`/etc/yum.repos.d`, 通过编辑配置文件可以添加仓库, 一般需要配置正确的URL和加密密钥

+ 使用`yum repolist`查看目前配置可使用的仓库

## <u>通过源码进行软件安装</u>

1. 下载`xxx.tar.gz`压缩包
2. 使用`tar -zxvf xxx.tar.gz`解压缩
3. 进入到解压后的文件目录中, 使用`./configure`查看是否具备库依赖环境, 若缺少则使用`yum`进行下载
4. 在满足依赖环境后, 使用`make`进行编译, 再使用`make install`进行安装