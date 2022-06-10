# Linux内核

## 内核功能

Linux系统的核心是内核. 内核控制着计算机系统上的所有硬件和软件, 在必要时分配硬件, 并根据需要执行软件.

内核主要负责以下4种功能:

+ <u>系统内存管理</u>

  内核不仅管理服务器上的可用物理内存, 还可以创建和管理虚拟内存(外存虚拟). 内核通过硬盘上的存储空间来实现虚拟内存, 这块区域称为==交换空间(swap space)==. 内核不断地交换<u>实际的物理内存和外存中交换空间</u>之间虚拟内存中的内容. 这使得系统以为它拥有比物理内存更多的可用内存.

  内存存储单元按组划分成很多块, 这些块称为<u>页面(page)</u>. 内核将页面放在物理内存或交换空间中. 然后内核维护一个内存页面表, 指明哪些页面位于物理内存中, 哪些页面被交换到交换空间中.

  内核会记录哪些内存页面正在使用, 并自动把一段时间未访问的内存页面复制到交换空间区域, 即使还有可用内存, 该过程称为<u>换出(swapping out)</u>. 当程序要访问一个在交换空间上的页面时, 内核需要为其分配物理内存或者换出另外一个内存页面.

+ <u>软件程序管理</u>

  内核创建<u>第一个进程(init进程)</u>, 从而启动系统上的其它所有进程. 当内核启动时, 它会将init进程加载到虚拟内存中. 内核在启动任何其它进程时, 都会在虚拟内存中给新进程分配一块专有区域来存储该进程用到的数据和代码.

  Linux操作系统采用了<u>运行级</u>. 运行级决定了init进程运行`/etc/inittab`文件或`/etc/rc[1-5].d`目录中定义好的某些特定类型的进程. Linux操作系统共有5个启动运行级.

  + 运行级为1时, 只启动基本的系统进程以及一个控制台终端进程, 我们称之为<u>单用户模式</u>. 单用户模式通常用来在系统有问题时进行紧急的文件系统维护, 在这种模式下, 只能由系统管理员登录到系统上操作数据.
  + 标准的启动运行级是3, 在这个运行级上大多数应用软件都会启动
  + 另一个常见的运行级是5, 在这个运行级上系统会启动图形化的X Window系统, 允许用户通过图形化桌面窗口登录系统.

+ <u>硬件设备管理</u>

  内核的另一职责是管理硬件设备, 任何Linux系统需要与之通信的设备, 都需要在内核代码中加入其驱动程序代码. 驱动程序代码相当于应用程序和硬件设备之间的中介, 允许内核与设备之间进行数据交换. 在Linux内核中有两种方式插入设备驱动代码:

  + 编译进内核的设备驱动代码
  + 可插入内核的设备驱动模块

  以前插入设备驱动代码的唯一途径是重新编译内核, 每次给系统添加新设备, 都需要重新编译一遍内核代码. 随着Linux内核支持的硬件设备越来越多, 这个过程变得越来越低效, Linux开发人员设计出一种更好的将驱动代码插入到运行中的内核的方法, 提出了<u>内核模块</u>的概念, **它允许将驱动代码插入到运行中的内核而无需重新编译内核, 同时当设备不再使用时也可以将内核模块从内核中移走**. 这种方式极大地简化和扩展了硬件设备在Linux上的使用.

  Linux系统将硬件设备当作特殊的文件, 称为设备文件. 设备文件共有3种分类:

  + **字符型设备文件**

    字符型设备文件是指处理数据时每次只能处理一个字符的设备. 大多数类型的调制解调器和终端都是作为字符型设备文件创建的.

  + **块设备文件**

    块设备文件是指处理数据时每次能处理大块数据的设备, 比如硬盘.

  + **网络设备文件**

    网络设备文件是指采用数据包发送和接收数据的设备, 包括各种网卡和一个特殊的回环设备. <u>这个回环设备允许Linux系统使用常见的网络编程协议同自身通信.</u>

  Linux为系统上的<u>每个设备</u>都创建一种称为<u>节点</u>的特殊文件. 与设备的所有通信都通过设备节点完成, 每个节点都有唯一的数值对作为其标识符, 包括一个主设备号和一个次设备号. 类似的设备被划分到同样的主设备号下, 次设备号用于标识主设备组下的某个特定设备.

+ <u>文件系统管理</u>

  Linux内核<u>支持通过不同类型的文件系统</u>从硬盘中读写数据. 内核必须在编译时就加入所有可能用到的文件系统的支持.

  Linux服务器中访问的所有硬盘都必须格式化成下表所列文件系统类型中的一种. Linux内核采用虚拟文件系统(VFS)作为和每个文件系统交互的接口. 这为Linux内核同任何类型文件系统通信提供了一个标准接口. 当每个文件系统都被挂载和使用时, VFS将信息都缓存在内存中.

<center>Linux文件系统</center>

| 文件系统 | 描述                                            |
| -------- | ----------------------------------------------- |
| ext      | Linux扩展文件系统, 最早的Linux文件系统          |
| ext2     | 第二扩展文件系统, 在ext的基础上提供了更多的功能 |
| ext3     | 第三扩展文件系统, 支持日志功能                  |
| ext4     | 第四扩展文件系统, 支持高级日志功能              |
| hpfs     | OS/2高性能文件系统                              |
| jfs      | ISM日志文件系统                                 |
|          |                                                 |
|          |                                                 |
|          |                                                 |

## GNU工具

核心GNU工具(core utilities)包括三个部分:

+ 管理进程的工具
+ 操作文本的工具
+ 处理文件的工具

shell工具为用户提供了启动程序, 管理文件系统中的文件以及运行在Linux系统上的进程的途径. 如果需要将多个shell命令放在一起工作执行, 则可以使用<u>shell脚本</u>.

# 主机规划与磁盘分区

<u>内容</u>

+ 磁盘分区
+ 文件系统
+ Linux操作较频繁的目录



<u>在Linux中, 一切皆文件</u>

<center>常见设备及其在Linux系统中的文件名</center>

| 设备              | 设备在Linux内的文件名                                        |
| ----------------- | ------------------------------------------------------------ |
| SCSI/SATA/USB硬盘 | `/dev/sd[a-p]`                                               |
| VirtI/O界面       | `/dev/vd[a-p]`                                               |
| 软盘              | `/dev/fd[0-7]`                                               |
| 打印机            | `/dev/lp[0-2]`<br />`/dev/usb/lp[0-15]`                      |
| 鼠标              | `/dev/input/mouse[0-15]`<br />`/dev/psaux`<br />`/dev/mouse` |
| CDROM<br />DVDROM | `/dev/scd[0-1]`<br />`/dev/sr[0-1]`<br />`/dev/cdrom`        |
| 磁带机            | `/dev/ht0`<br />`/dev/st0`<br />`/dev/tape`                  |

## 磁盘分区

以Windows系统的观点来看, 将一块磁盘分为C,D,E三个盘区, 则C,D,E就是<u>分区(partition)</u>.

> 例题: 如果你的PC上面有两个SATA磁盘以及一个USB磁盘, 而主板上面有六个SATA的插槽. 这两个SATA磁盘分别安插在主板上的SATA1, SATA5插槽上, 请问这三个磁盘在Linux中的设备文件名为何？

答: 由于是<u>使用侦测到的顺序来决定设备文件名, 并非与实际插槽代号有关</u>, 因此设备的文件名如下: 

1. SATA1插槽上的文件名: `/dev/sda`
2. SATA5插槽上的文件名: `/dev/sdb`
3. USB磁盘（开机完成后才被系统捉到）: `/dev/sdc`



### 开机流程中的BIOS与UEFI开机检测程序

操作系统会控制所有的硬件并向上层应用程序提供核心功能, 因此计算机就能够认识硬盘内的文件系统, 并进一步读取硬盘内的软件文件与执行.

**问题是, 操作系统也是软件, 那么计算机又是如何认识这个操作系统软件并且执行它的？ 明明开机时计算机中还没有任何软件系统, 那它要如何读取硬盘内的操作系统文件呢？这就得要牵涉到计算机的开机启动程序了！**

目前主要有两种方式: 早期的BIOS和新的UEFI

CMOS是记录各项硬件参数且嵌入在主板上面的储存器, BIOS则是一个写入到主板上的一个固件（固件就是写入到硬件上的一个软件程序）。

#### BIOS搭配MBR/GPT的开机流程

1. 计算机系统会主动执行<u>BIOS程序</u>.

2. BIOS会分析计算机里面有哪些存储设备, 根据用户的设置, 到该硬盘里面区读取第一个扇区的<u>MBR</u>位置.

3. MBR这个仅有446B的硬盘容量里面会放置最基本的<u>开机管理程序(boot loader)</u>, 接下来就是MBR内的开机管理程序的工作了.

4. 开机管理程序加载<u>核心文件</u>

   开机管理程序是操作系统在安装的时候所提供的, 它会认识硬盘内的文件系统格式, 因此能够读取核心文件.

5. 操作系统正常工作

如果分区表是GPT格式, 那么BIOS也能够从LBA0的MBR相容的区块读取第一阶段的开机管理程序码. 能否正常开机取决于boot loader是否能够识别GPT格式, 如果不能识别GPT, 则无法读取操作系统核心文件, 故而开机失败.



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



# Vim编辑器

vi编辑器是Unix系统最初始的编辑器, 在GNU项目将vi编辑器一直到开源世界后, 将其进行一些改进, 并重命名为vim(vi improved)

## vim安装

在Centos7系统中, 使用`yum install -y vim`进行安装

在Ubuntu系统中, 使用`sudo apt-get install vim`进行安装

## vim基础

vim在内存缓冲区中处理数据

+ 未指定文件名或文件不存在, 则开辟一段新的缓冲区域来编辑
+ 指定文件存在, 则将文件的整个内容读入到缓冲区中

vim编辑器有两种操作模式: <u>普通模式和插入模式</u>. 在普通模式下, 输入的按键将会被解释为命令(<u>区分大小写</u>); 在插入模式下则输入文本内容.

<center>vim操作模式的相互转换</center> 

<img src="https://namebucket.oss-cn-beijing.aliyuncs.com/img/%E6%99%AE%E9%80%9A%E6%A8%A1%E5%BC%8F%E5%92%8C%E6%8F%92%E5%85%A5%E6%A8%A1%E5%BC%8F.svg" style="zoom:25%;" />

### 普通模式

+ `方向键`: 上下左右
+ `<PageDown>/<PageUp>`: 向下/上翻页
+ `G`: 移到文本的最后一行
+ `gg`: 移动到文本的第一行
+ `<num> G`: 移动到文本的第`num`行
+ `:`: <u>命令行模式</u>
  + `q`: 如果未修改缓冲区数据, 则可以退出
  + `q!`: 取消对数据的修改并退出, 强制退出
  + `w <filename>`: 将数据保存到一个名为`filename`的文件中
  + `wq`: 保存数据修改并退出

#### 编辑命令

<center>vim编辑命令</center>

| 命令         | 描述                                                         | 拓展使用 |
| ------------ | ------------------------------------------------------------ | -------- |
| `x/<Delete>` | <u>删除</u>当前光标所在位置的字符                            | `2x`     |
| `dd`         | <u>删除</u>当前光标所在<u>行</u>的字符串                     | `5dd`    |
| `dw`         | <u>删除</u>当前光标所在位置的<u>单词</u>                     |          |
| `d$`         | <u>删除</u>当前光标至<u>行尾(`$`)</u>的内容                  |          |
| `J`          | <u>删除</u>当前光标所在行行尾的<u>换行符(即拼接行)</u>       |          |
| `u`          | <u>撤销</u>                                                  |          |
| `a`          | 在当前光标位置后<u>追加数据</u>                              |          |
| `A`          | 在当前光标所在行<u>行尾追加数据</u>                          |          |
| `r <char>`   | 用`<char>`字符替换当前光标所在位置的<u>单个字符</u>          |          |
| `R <text>`   | 用`<text>`覆盖当前光标所在位置的数据, 直到按下esc键(<u>进入到REPLACE模式</u>) |          |

<u>删除命令</u>会将删除数据存放到寄存器中, 使用`p`命令可以取回数据, 搭配使用相当于`剪切 + 粘贴`

#### 复制和粘贴

复制命令和删除命令相类似, 只需要将`d`改为`y`即可. 由于复制不会影响到复制的文本, 因此只有当使用`p`命令进行粘贴之后才能清楚实际复制了什么内容. 当然, 这个问题可以通过<u>可视模式</u>进行解决, 按下`v`键会高亮文本, <u>高亮的内容即为选中的文本</u>

#### 查找和替换

1. 输入`/`, 再输入需要查找的内容(<u>区分大小写</u>), 按下回车键即可查找
2. 找到内容后, 使用`n`可以查看下一个
3. 在<u>命令行模式</u>下才可以使用替换功能, 格式为`:s /<old>/<new>`

<center>替换功能格式</center>

| 格式                   | 功能                                                   |
| ---------------------- | ------------------------------------------------------ |
| `:s /<old>/<new>`      | 从当前光标到文件末尾查找`<old>`并将第一个替换为`<new>` |
| `:s /<old>/<new>/g`    | 将当前光标所在行的`<old>`替换为`<new>`                 |
| `:n,ms /<old>/<new>/g` | 替换从行号`n`到行号`m`之间所有的`<old>`                |
| `:%s /<old>/<new>/g`   | 替换整个文件中所有的`<old>`                            |
| `:%s /<old>/<new>/gc`  | 替换整个文件中所有的`<old>`且每次进行提示确认          |

## Vim其它设置

通过`vim ~/.vimrc`命令创建(或打开)`.vimrc`文件, 具体配置信息如下

<center>.vimrc文件配置及其含义</center>

| 文件配置        | 含义                 |
| --------------- | -------------------- |
| set hlsearch    | 高亮度反白           |
| set backspace=2 | 可随时用退格键删除   |
| set autoindent  | 自动缩进             |
| set ruler       | 可显示最后一行的状态 |
| set showmode    | 左下角那一行的状态   |
| set nu          | 显示行号             |
| set bg=dark     | 显示不同的底色       |
| syntax on       | 语法高亮             |

