# SpringBoot阶段错误处理

---

## 问题一

### 问题描述

运行SpringBoot程序时报错, 报错代码如下

```log
Error running 'SprinbootDemoApplication': Command line is too long. Shorten command line for SprinbootDemoApplication or also for Spring Boot default configuration?
```

### 原因分析

+ 由于在新建SpringBoot工程时添加了Spring Native等, 这些工具需要Java 11的支持, 而我电脑中使用的是Java 8, 因此不能够正确运行

### 处理方法

删除掉Maven中暂时用不到的依赖

---

## 问题二

### 问题描述

需要添加`@ComponentScan`才能进行包扫描

### 原因分析

+ 主启动类没有和`controller`等包在同一目录下面

### 处理方法

