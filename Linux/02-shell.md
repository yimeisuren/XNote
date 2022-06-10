# shell

在图形化桌面出现之前, 与Unix系统进行交互的唯一方法是借助shell所提供的<u>文本命令行界面(command line interface, CLI)</u>. CLI只能接受文本输入, 也只能显示文本和基本的图形输出.

## 启动shell

登录时启动的shell依赖于用户账户的配置, 在`/etc/passwd`文件中包含了所有系统用户账户列表以及每个用户的基本配置信息.

```sh
  1 root:x:0:0:root:/root:/bin/bash
  2 bin:x:1:1:bin:/bin:/sbin/nologin
  3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
  4 adm:x:3:4:adm:/var/adm:/sbin/nologin
  5 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
  6 sync:x:5:0:sync:/sbin:/bin/sync
  7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
  8 halt:x:7:0:halt:/sbin:/sbin/halt
  9 mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
 10 operator:x:11:0:operator:/root:/sbin/nologin
 11 games:x:12:100:games:/usr/games:/sbin/nologin
 12 ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
 13 nobody:x:99:99:Nobody:/:/sbin/nologin
 14 systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
 15 dbus:x:81:81:System message bus:/:/sbin/nologin
 16 polkitd:x:999:998:User for polkitd:/:/sbin/nologin
 17 sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
 18 postfix:x:89:89::/var/spool/postfix:/sbin/nologin
 19 xiong:x:1000:1000:xiong:/home/xiong:/bin/bash
```

每个条目有7个字段, 中间用冒号`:`分隔. 最后一个字段指明了用户使用的shell程序.

<u>bash shell程序会在登录后自动启动</u>, 但是是否显示命令行界面取决于登录方式, 如果是控制台终端登录则显示, 如果是图形化界面则不显示.

## 文件系统

在Windows系统中, 会为每个物理磁盘驱动器分配一个盘符, 每个驱动器都会有自己的目录结构, 以便访问存储其中的文件.

Linux在路径名中不使用驱动器盘符(即磁盘分区C,D,E...), 将文件存储在单个目录结构中, 这个目录称为<u>虚拟目录(virtual directory)</u>. Linux虚拟目录结构只包含一个称为根目录(root)的基础目录, 使用`/`表示. 

Linux系统中的路径本身并没有提供任何有关文件究竟存放在哪个物理磁盘上的信息, 例如`/opt/module/test.txt`. 而在Windows系统中, 如果外接USB, 假设其盘符为F, 则能够从路径中清楚地看出一个文件是否属于F盘, 即是否存放在U盘上.

### Linux文件系统

在Linux系统上安装的<u>第一块硬盘称为根驱动器</u>. 根驱动器包含虚拟目录的核心, 其它目录都是从那里开始创建的. 

Linux会在根驱动器上创建一些特别的目录, 这些目录称为<u>挂载点(mount point)</u>. 挂载点是虚拟目录中用于分配额外存储设备的目录, 虚拟目录会让文件和目录出现在这些挂载点目录中, 然后实际上它们却存储在另外一个驱动器中. <u>挂载是将其他机器上的磁盘映射到本机器上来, 使得在本机器上可以访问其他机器的文件</u>.

常见的目录名均遵循<u>文件系统层级标准(FHS, filesystem hierarchy standard)</u>

<center>常见Linux目录名称</center>

|      目录       |                             功能                             | 对应单词               |
| :-------------: | :----------------------------------------------------------: | ---------------------- |
|       `/`       |        与启动系统有关, 根目录, 通常不会在这里存储文件        | root                   |
|     `/bin`      |             二进制目录, 存放许多用户级的GNU工具              | binary                 |
|     `/boot`     |                    启动目录, 存放启动文件                    |                        |
|     `/etc`      |                  配置文件目录，如账号密码等                  |                        |
|     `/dev`      |              设备目录, Linux在这里创建设备节点               |                        |
|     `/home`     |                     普通用户的家(主)目录                     |                        |
|     `/lib`      |              库目录, 存放系统和应用程序的库文件              |                        |
|    `/media`     |                    可移动媒体的常用挂载点                    |                        |
|     `/mnt`      |                         另一个挂载点                         |                        |
|     `/opt`      |                    第三方软件包和数据文件                    |                        |
|     `/proc`     | 进程目录, 存放现有硬件和当前进程信息, 虚拟文件系统, 不要往里面写东西 |                        |
|     `/root`     |                       root用户的主目录                       |                        |
|     `/sbin`     | 系统二进制目录, 存放许多GNU管理员级工具, 只允许超级用户查看  |                        |
|     `/run`      |             运行目录, 存放系统运作时的运行时数据             |                        |
|     `/srv`      |               服务目录, 存放本地服务相关的文件               |                        |
|     `/sys`      | 虚拟文件系统，不要往里面写东西。系统目录, 保存硬件和内核相关信息 |                        |
|     `/tmp`      |        临时目录, 可以在该目录中创建和删除临时工作文件        |                        |
|     `/usr`      | 用户存放系统软件资源目录, 软件存放处.大量用户级GNU工具和数据文件都在这 | unix software resource |
|     `/var`      |       动态数据保存位置, 与系统运行过程有关的日志文件等       | variable               |
|    /etc/opt     |                    放置/opt的相关配置文件                    |                        |
|    /etc/X11     |  与x window有关的各种配置文件, 尤其是xorg.conf这个配置文件   |                        |
|    /etc/sgml    |                 与sgml格式有关的各项配置文件                 |                        |
|    /etc/xml     |                 与xml格式有关的各项配置文件                  |                        |
|   /usr/local    |                   第三方安装的软件保存位置                   |                        |
|    /var/mail    |                           用户邮箱                           |                        |
|    /var/run     |                           程序相关                           |                        |
| /var/spool/news |                            新闻组                            |                        |
|    /var/lock    |                           程序相关                           |                        |
|   lost+found    |                           文件碎片                           |                        |
|      /misc      |                             盘符                             |                        |
|    /usr/sbin    |                        只允许超级用户                        |                        |
|    /usr/bin     | 在单用户模式(类似windows系统下的安全模式，用最少的资源恢复系统)下不能执行 |                        |

### 访问目录`cd`

可以使用<u>绝对路径或相对路径</u>

### 查看文件和目录`ls`

+ `pwd`: 查看当前目录的路径

+ `ls`: 查看当前目录下的文件和子目录(列按字典序排列)

+ `ls -a`: 查看包括<u>隐藏文件</u>在内的所有文件和子目录

+ `ls -F`: 如果没有彩色终端仿真器的情况下, 可以区分出目录和文件

+ `ls -R`: <u>递归显示</u>文件和目录, 即子目录下的文件和目录也会显示

+ `ls -l`: 显示<u>文件详情</u>, 长列表

+ `ls -l <filename>`: 过滤器, 只显示`<filename>`相关的文件信息

  可以使用`?, *, [], !, -`等通配符来匹配文件, 使用通配符来进行模式匹配的过程称为<u>文件扩展匹配(file globbing)</u>.

<center>文件详情中的信息</center>

|                        |                                                              |
| ---------------------- | ------------------------------------------------------------ |
| 文件类型               | `d`: 目录<br />`-`: 文件<br />`c`: 字符型文件<br />`b`: 块设备 |
| 文件权限               |                                                              |
| 文件的硬链接总数       |                                                              |
| 文件的拥有者           |                                                              |
| 文件的拥有组           |                                                              |
| 文件的大小(单位: Byte) |                                                              |
| 文件的最近修改时间     |                                                              |
| 文件名(或目录名)       |                                                              |

**输出示例:**

```bash
drwxr-xr-x.  2 root root      6 Apr 11  2018 xinetd.d
drwxr-xr-x.  6 root root    100 Jun  5 14:11 yum
-rw-r--r--.  1 root root    970 Oct  2  2020 yum.conf
drwxr-xr-x.  2 root root    262 Jun  7 20:14 yum.repos.d
```

### 处理文件

+ `touch <filename>`: 创建名为`<filename>`的空文件

+ `cp -i <source> <destination>`: 将`<source>`文件复制到`<destination>`文件中, 使用`-i`选项会提示是否覆盖已有文件

+ `cp -R <sourceDir> <destinationDir>`: 将目录文件递归的复制到另一个目录中, `-R`表示递归复制

  在cp命令中<u>可以使用通配符</u>

+ `ln -s <filename> <linkname>`: 创建名为`<linkname>`的软链接

+ `ln <filename> <linkname>`: 创建名为`<linkname>`的硬链接

  链接文件包括硬链接和软链接(符号链接), 符号链接相当于创建了一个原文件的快捷方式. 

  <u>软链接文件保存的信息是原文件的文件路径, 软链接文件和原文件是两个完全不相同的文件.</u>

  硬链接文件和原文件可以认为是同一个东西, 都是硬盘上的文件资源的一个入口, 新建硬链接会使得该资源的inode节点加1, 删除硬链接和原文件都会使得inode节点数减1, 只有当inode节点数为0时该资源才会被删除. 需要注意的是<u>硬链接不会使得原来硬盘上的资源拷贝复制一份</u>. 即原来占用1MB的文件不会因为创建硬链接而变为占用2MB.

+ `mv <source> <destination>`: 移动文件, 如果是同一目录下, 则可以实现**重命名文件**

+ `rm <filename>`: 删除文件

### 处理目录

+ `mkdir <dirname>`: 创建目录
+ `mkdir -p <dirname>/<subdirname>`: 选项`-p`帮助创建目录及其子目录
+ `rmdir <dirname>`: 删除**空目录**
+ `rm -rf <dirname>`: 删除带有内容的目录(递归删除)
+ `tree <dirname>`: 以树形方式展示目录结构

## 监测程序

+ `ps`: 查看进程
+ `top`: 实时显示<u>进程</u>
+ `kill <pid>`: 根据`<pid>`结束进程

## 监测磁盘空间

### 挂载

Linux文件系统将所有的磁盘都放入到虚拟目录下, 所以在使用新的存储媒体之前需要将其放到虚拟目录下, 这个过程称为<u>挂载(mounting)</u>. 大多数的Linux发行版都支持自动挂载可移动存储媒体, 例如CD-ROM, 软盘, U盘等.

+ `mount`: 默认输出当前系统中挂载的设备列表

  mount命令输出的信息由4个部分组成:

  + 媒体的设备文件名
  + 媒体挂载到虚拟目录的挂载点
  + 文件系统类型
  + 已挂载媒体的访问状态

  手动在虚拟目录中挂载设备, 需要root权限, 基本命令为`mount -t <type> <device> <directory>`: 表示将设备`<device>`以`<type>`的文件格式化类型挂载到`<directory>`目录

  ```bash
  [root@hadoop100 ~]# mount
  sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime,seclabel)
  proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
  devtmpfs on /dev type devtmpfs (rw,nosuid,seclabel,size=1919884k,nr_inodes=479971,mode=755)
  securityfs on /sys/kernel/security type securityfs (rw,nosuid,nodev,noexec,relatime)
  tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev,seclabel)
  devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,seclabel,gid=5,mode=620,ptmxmode=000)
  tmpfs on /run type tmpfs (rw,nosuid,nodev,seclabel,mode=755)
  tmpfs on /sys/fs/cgroup type tmpfs (ro,nosuid,nodev,noexec,seclabel,mode=755)
  cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,xattr,release_agent=/usr/lib/systemd/systemd-cgroups-agent,name=systemd)
  pstore on /sys/fs/pstore type pstore (rw,nosuid,nodev,noexec,relatime)
  cgroup on /sys/fs/cgroup/net_cls,net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,net_prio,net_cls)
  cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,perf_event)
  cgroup on /sys/fs/cgroup/pids type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,pids)
  cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,hugetlb)
  cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,memory)
  cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,cpuset)
  cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,cpuacct,cpu)
  cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,devices)
  cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,blkio)
  cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,freezer)
  configfs on /sys/kernel/config type configfs (rw,relatime)
  /dev/sda3 on / type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
  selinuxfs on /sys/fs/selinux type selinuxfs (rw,relatime)
  systemd-1 on /proc/sys/fs/binfmt_misc type autofs (rw,relatime,fd=23,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=12625)
  mqueue on /dev/mqueue type mqueue (rw,relatime,seclabel)
  hugetlbfs on /dev/hugepages type hugetlbfs (rw,relatime,seclabel)
  debugfs on /sys/kernel/debug type debugfs (rw,relatime)
  fusectl on /sys/fs/fuse/connections type fusectl (rw,relatime)
  /dev/sda1 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
  tmpfs on /run/user/0 type tmpfs (rw,nosuid,nodev,relatime,seclabel,size=386132k,mode=700)
  binfmt_misc on /proc/sys/fs/binfmt_misc type binfmt_misc (rw,relatime)
  ```

  

+ `umount <directory>`: 卸载指定设备

  从Linux系统上移除一个可移动设备时, 不能直接从系统上移出, 而应该先卸载. 使用umount来卸载设备, 如果有任何程序正在使用设备上的文件, 系统不会允许你卸载它.

