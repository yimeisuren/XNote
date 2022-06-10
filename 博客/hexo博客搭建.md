

# hexo搭建个人博客

## 下载nodejs使用npm

## 全局安装hexo

**全局安装的作用是以后没有网络的时候也能建立工程**

1. 全局安装淘宝的cnpm, 方便加速

   ```shell
   npm install -g cnpm
   ```

2. 全局安装hexo

   ```shell
   cnpm install -g hexo
   ```

## hexo使用流程

1. 在IDEA中新建工程`blogtest`
  
1. 打开命令行终端, 进入resources文件夹, 使用`hexo init`命令建立hexo项目. **必须为空文件夹才能用来初始化hexo项目**
  
   ![image-20220127041335213](https://gitee.com/andewkarsa/pictures/raw/master/image-20220127041335213.png)
   
3. 使用`hexo n`命令新建文章, 存放在生成的`source`->`_posts`文件夹下
   ![image-20220126231641532](https://gitee.com/andewkarsa/pictures/raw/master/image-20220126231641532.png)

4. 使用`Typora`软件对文章进行修改后保存

4. 使用`hexo g`命令对`_post`文件夹下的文章进行处理生成`html`文件, 以便放到网站上展示

6. 使用`hexo s`命令启动项目进行本地预览

7. 在github上新建仓库, 必须将其命名为:  **<用户名>.github.io** (不需要尖括号“<”)

8. 使用`cnpm install --save hexo-deployer-git`命令下载部署插件

9. 修改`_config.yml`配置文件

   ```yaml
   # Deployment
   ## Docs: https://hexo.io/docs/deployment.html
   deploy:
     type: git
     repo: https://github.com/<用户名>/<用户名>.github.io #如果部署到gitee上, 则将github该层gitee即可
     branch: main
   ```

10. 防止一些莫名奇妙的问题

   ```shell
   git config --global http.sslVerify "false"
   ```

   

11. 使用`hexo d`命令推送到远端

12. 输入网址 https://yimeisuren.github.io 对个人博客进行访问

## 修改博客主题

+ 网上搜索hexo主题,找到github的地址, 并克隆到blog文件夹下. 下载慢速的情况下加入`.cnpmjs.org`后缀

  ```shell
  git clone https://github.com.cnpmjs.org/next-theme/hexo-theme-next.git themes/next
  ```

  ![image-20220127001135016](https://gitee.com/andewkarsa/pictures/raw/master/image-20220127001135016.png)

  其中`themes/next`为`blog`文件夹下的子文件夹

+ 修改配置文件`_config.yml`
  theme部分修改为

  ```yaml
  theme: next #和文件夹保持一致
  ```

  

## hexo操作

1. **必须使用hexo n来新建文章, 否则文章的名字属于“无标题”**

2. 在`hexo d`推送之前, 要先下载部署器

```shell
hexo clean#清楚public里面的文件(待推送的文件)
hexo n#新建markdown文档
hexo g#由source/_post中的文件生成public中的html文件
hexo s#启动hexo服务
hexo d #推送到github上
```

