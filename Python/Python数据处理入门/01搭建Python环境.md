# 第一章: 从零开始搭建Anaconda + PyCharm环境

@[TOC](Python数据处理入门)


# 前言
首先简单类比下Anaconda和PyCharm的作用

如果将写代码的过程比作用画笔绘制画作, 则**Anaconda**相当于**颜料盒**, 里面存放着各种库, 这些库的作用就相当于颜料. 而**PyCharm**相当于一支**高级画笔**, 用起来顺手. 你也可以选择其它的画笔, 甚至颜料盒里面可能自带一支送的画笔, 但不好用. 


# 一、Anaconda下载和配置

## 下载Anaconda

[Anaconda下载地址](https://www.anaconda.com/)

## 安装Anaconda

**注意事项**

1. 记住安装路径, 之后需要使用
2. 在安装过程中除了`next`之外, 需要注意在该页面勾选这个两个选项, 可以避免需要手动添加环境变量的麻烦. 

![image-20220420133526508](https://namebucket.oss-cn-beijing.aliyuncs.com/img/Anaconda%E5%AE%89%E8%A3%85.png)


# 二、PyCharm下载和配置
## 下载PyCharm

[PyCharm下载地址](https://www.jetbrains.com/pycharm/download/#section=windows)

**注意事项**

1. 选择其它版本, 建议2021.1.3版本及以下, 高版本因为一些你自身的原因(穷)不建议使用
2. 下载专业版PyCharm Professional Edition

![image-20220420134121790](https://namebucket.oss-cn-beijing.aliyuncs.com/img/PyCharm%E4%B8%8B%E8%BD%BD%E6%AD%A5%E9%AA%A41.png)



## 安装PyCharm

所有能打勾的都勾选上并下一步即可

## 配置PyCharm(重点)

1. 有一个许可证的页面, 选择`Evaluate for free`和`	Evaluate`进行试用

   ![image-20220420135313519](https://namebucket.oss-cn-beijing.aliyuncs.com/img/PyCharm%E9%85%8D%E7%BD%AE%E6%AD%A5%E9%AA%A41.png) 

2. 随便新建一个项目`New Project`, 进入到如下所示主界面中

   ![image-20220420135502140](https://namebucket.oss-cn-beijing.aliyuncs.com/img/PyCharm%E9%85%8D%E7%BD%AE%E6%AD%A5%E9%AA%A42.png)

3. 依次选择`File`-->`New Project Settings`-->`Settings for New Project`-->`Python Interpreter`

   此时还没有将PyCharm和Anaconda联系起来, 因此此处显示空

   ![image-20220420135856742](https://namebucket.oss-cn-beijing.aliyuncs.com/img/PyCharm%E9%85%8D%E7%BD%AE%E6%AD%A5%E9%AA%A43.png)

4. 依次选择`右上角`的小齿轮⚙-->`add`-->`COnda Environment`-->`Existing environment`-->`Make available to all projects`, 此时页面设置如下(`Conda executable`会根据Anaconda的安装路径有所差别, 没有影响)

   ![image-20220420140317895](https://namebucket.oss-cn-beijing.aliyuncs.com/img/PyCharm%E9%85%8D%E7%BD%AE%E6%AD%A5%E9%AA%A44.png)

5. 选择`Interpreter`右边的`...`, 找到`Anaconda3\python.exe`所在的文件路径, 点击`OK`即可

   ![image-20220420141001515](https://namebucket.oss-cn-beijing.aliyuncs.com/img/PyCharm%E9%85%8D%E7%BD%AE%E6%AD%A5%E9%AA%A45.png)

6. 之后重新新建一个项目`File`-->`New Project`即可

# 三、配置阿里源镜像
在使用过程中, 我们不可避免的会使用到一些其他的库(颜料盒里面所没有的颜料), 如果使用默认的pip进行安装, 下载速度可能十分缓慢, 因此需要进行一些修改

在你的用户目录下, 即`C:\Users\你自己的用户名`下新建`.pip`目录, 在`.pip`目录下创建`pip.txt`文件, 写入下面内容
```ini
[global]

index-url = http://mirrors.aliyun.com/pypi/simple/
```
将`pip.txt`的修改为`pip.ini`即配置成功