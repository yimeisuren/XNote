# 第二章: PyCharm相关配置

@[TOC](Python数据处理入门)

---

# 配置忽略大小写

依次选择`File-->Settings`进入如下界面, 搜索框中输入**case**

![image-20220420143625009](https://namebucket.oss-cn-beijing.aliyuncs.com/img/PyCharm%E7%9B%B8%E5%85%B3%E9%85%8D%E7%BD%AE%E5%BF%BD%E7%95%A5%E5%A4%A7%E5%B0%8F%E5%86%99.png)

# 无限试用

1. 依次选择`File-->Settings-->Plugins`进入到插件页面

   ![image-20220420144115473](https://namebucket.oss-cn-beijing.aliyuncs.com/img/PyCharm%E6%8F%92%E4%BB%B6%E9%A1%B5%E9%9D%A2.png)

2. 选择中间的小齿轮⚙, 选择`Manage Plugin Repositories`, 再选择`+`, 添加如下地址后点击`OK`

   ![image-20220420144405955](https://namebucket.oss-cn-beijing.aliyuncs.com/img/PyCharm%E6%8F%92%E4%BB%B6%E5%9C%B0%E5%9D%80.png)

3. 在插件商店`Marketplace`中下载如下插件, 点击`Apply`和`OK`

   ![image-20220420144547859](https://namebucket.oss-cn-beijing.aliyuncs.com/img/IDE%20Eval%20Reset.png)

4. 回到主界面, 会有一个弹窗, 如果没有弹出, 则点击`Help-->Eval Reset`

   勾选`Auto reset per restart`并点击`Reset`完成配置

   ![image-20220420145014100](https://namebucket.oss-cn-beijing.aliyuncs.com/img/EVAL%20Reset%E8%AE%BE%E7%BD%AE.png)

5. 由于软件的试用期是30天, 如果PyCharm的两次使用时间间隔大于30天, 则需要按照上面设置插件的步骤重新Reset. 如果在30天内登录, 插件会在关闭或打开软件的时候在背后执行这个过程, 不需要手动设置了.