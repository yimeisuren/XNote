# JavaWeb阶段错误处理

## 问题一

### 问题描述

在配置`Servlet`的编码时出现错误, 如下所示

`无法为上下文[/servlet]设置响应编码，因为该上下文已初始化`

### 原因分析

IDEA配置的热部署没有生效或者浏览器存在缓存

### 错误处理

手动重启tomcat服务器

## 问题二

### 问题描述

tomcat在控制台中输出中文乱码

### 原因分析

windows 10中文系统的字符编码集默认为gbk

### 错误处理

1. 修改windows 10系统的控制台的字符编码集

   `区域与语言设置`-->`管理语言设置`-->`更改系统区域设置`-->`勾选utf-8`

   ==仅仅修改上面的配置后, tomcat在控制台输出中文还是乱码==

2. 在tomcat服务器的配置选项, 即`Edit Configurations`中的`VM options`设置`-Dfile.encoding=UTF-8`

   ==控制台中文输出乱码的问题被解决, 但是控制台的日志输出还是乱码==

3. 配置IDEA启动JVM时的编码, 即在`Help`-->`Edit Custom VM options`中设置`-Dfile.encoding=UTF-8`

   ==控制台的中文乱码问题完全被解决, 但是发现网页显示出现乱码==

4. 下载插件`Charset`修改浏览器的默认编码为`utf-8`

   ==缺点: 某些网站的登录页面可能是gbk编码, 此时会乱码==

## 问题三

### 问题描述

在`webapp`目录下的`index.jsp`无法加入到`out`目录中, 导致网页无法定位到jsp文件

### 原因分析

因为将`webapp`目录移动到了main目录下, 但是项目projects structure中没有设置

### 错误处理

手动将所有的`.jsp`文件添加到生成的`out`目录中

## 问题四

### 问题描述

所有配置正常, 但是由tomcat生成的out目录中没有包含编译Servlet类后的字节码文件夹classes, 导致Servlet Not Found错误

### 原因分析

暂时未知, 可能是IDEA的bug, 或者是由于使用了maven父子工程来创建web项目

### 错误处理

1. 找到项目对应的`.iml`文件, 该文件可能会由于一些之前的设置被隐藏
2. 删除iml文件中的`<component name="NewModuleRootManager"></component>`部分的代码块
