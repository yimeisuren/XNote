

# Git仓库介绍

由Git管理的项目, 分为本地仓库和远程仓库, 二者本质上类似, <u>都可以有多个分支</u>.

仓库的URL为: `https://github.com/<owner>/<repositoryName>`



![image-20220209042638957](https://namebucket.oss-cn-beijing.aliyuncs.com/img/GitHub%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.png)





# Git操作流程

1. 使用`git init`初始化本地项目

   此时会生成`.git`文件夹, 用来管理当前目录

2. 默认分支名为`master`, 但此时使用`git branch`无法显示, 通过`git log`的输出信息可以大致猜测

   ```bash
   fatal: your current branch 'master' does not have any commits yet
   ```

3. 使用`git add *`将当前工作区(当前目录)中的内容全部添加到暂存区中

4. 使用`git commit -m "提示信息"`将暂存区的内容保存到本地仓库中, 此时再次输入`git branch`即可查看到分支名

   ```bash
   C:\Users\24563\Downloads\Documents\book>git branch
   * master
   ```

5. 创建远程仓库

   1. 可以登录到GitHub上手动创建远程仓库, <u>保持本地项目名和远程仓库名一致</u>

      <img src="https://namebucket.oss-cn-beijing.aliyuncs.com/img/github%E5%88%9B%E5%BB%BA%E6%96%B0%E4%BB%93%E5%BA%93.png" alt="image-20220610131041077" style="zoom:70%;" />

   2. 通过命令行创建一个远程仓库

      ```bash
      echo "# book" >> README.md 										# 创建README.md文件
      git init 														# 初始化项目
      git add README.md												# 添加README.md文件到暂存区
      git commit -m "first commit"									# 提交到本地仓库
      git branch -M main												# 分支为main
      git remote add book https://github.com/yimeisuren/book.git		# 为远程仓库URL命名为book
      git push -u book main											# 将本地仓库推送到book
      ```

      

   

   

<img src="https://namebucket.oss-cn-beijing.aliyuncs.com/img/Git%E6%93%8D%E4%BD%9C%E6%B5%81%E7%A8%8B.svg" style="zoom:20%;" />



# Git设置

1. 设置姓名和邮箱地址

   ```bash
   $ git config --global user.name "yimeisuren"
   $ git config --global user.email "2456387473@qq.com"
   ```

   

   ![image-20220209043550212](https://namebucket.oss-cn-beijing.aliyuncs.com/img/git%E8%AE%BE%E7%BD%AE%E5%A7%93%E5%90%8D%E5%92%8C%E9%82%AE%E7%AE%B1.png)

   这条命令会在`C:\Users\Administrator\.gitconfig`中以如下形式输出设置文件  

   ```.gitconfig
   [user]
   	name = yimeisuren
   	email = 2456387473@qq.com
   ```

   

2. 提高命令输出的可读性

   ```bash
   $ git config --global color.ui auto
   ```

   这条命令也会对配置文件进行如下修改

   ```.gitconfig
   [color]
   	ui = auto
   ```



# GitHub设置

1. 设置 SSH Key (连接仓库需要)

   ```bash
   $ ssh-keygen -t rsa -C "2456387473@qq.com" #设置ssh key命令
   Generating public/private rsa key pair.
   Enter file in which to save the key
   (/Users/your_user_directory/.ssh/id_rsa): #按回车键
   Enter passphrase (empty for no passphrase): #输入密码
   Enter same passphrase again: #再次输入密码
   ```

   输入密码之后出现

   ```bash
   Your identification has been saved in C:\Users\Administrator\.ssh\id_rsa.
   Your public key has been saved in C:\Users\Administrator\.ssh\id_rsa.pub.
   The key fingerprint is:
   fingerprint值 2456387473@qq.com
   The key's randomart image is:
   ```

   其中`id_rsa`是私钥, `id_rsa.pub`是公钥

2.   在GitHub中添加公钥
   <img src="https://namebucket.oss-cn-beijing.aliyuncs.com/img/GitHub%E6%B7%BB%E5%8A%A0%E5%85%AC%E9%92%A5.png" alt="image-20220209050202842"  />
   `id_rsa`和`id_rsa.pub`都可以使用记事本直接打开查看

3. 使用私钥和GitHub进行认证和通信

   ```bash
   $ ssh -T git@github.com
   The authenticity of host 'github.com (207.97.227.239)' can't be established.
   RSA key fingerprint is fingerprint值 .
   Are you sure you want to continue connecting (yes/no)? #输入yes
   Enter passphrase for key '/c/Users/Administrator/.ssh/id_rsa':#输入github账户密码
   ```

   出现如下语句代表操作成功

   ```bash
   $ Hi yimeisuren! You've successfully authenticated, but GitHub does not provide shell access.
   ```

# Git基本操作

### git init—初始化仓库

要使用 Git 进行版本管理, 必须先初始化仓库  

```bash
$ mkdir git-tutorial 	#创建git-tutorial文件夹
$ cd git-tutorial		#进入文件夹
$ git init				#初始化仓库
```

<img src="https://namebucket.oss-cn-beijing.aliyuncs.com/img/git%E5%88%9D%E5%A7%8B%E5%8C%96%E4%BB%93%E5%BA%93.png" alt="image-20220209060616158" style="zoom: 80%;" />

生成的`.git`目录称为<u>附属于该仓库的工作树</u>



### git status—查看仓库的状态

1. 使用`touch`命令创建文件

   ```bash
   touch README.md
   touch sometest.txt
   ```

2. 查看仓库状态

   ```bash
   git status
   ```

   <img src="https://namebucket.oss-cn-beijing.aliyuncs.com/img/git%E6%9F%A5%E7%9C%8B%E4%BB%93%E5%BA%93%E7%8A%B6%E6%80%81.png" alt="image-20220209061117746" style="zoom:80%;" />

   + on branch master: 处于master分支
   + commits: 待提交的文件
   + untracked files: git-tutorial目录下*存在*但是*没有被git仓库管理的对象*

### git add—向缓冲区中添加

```bash
git add * #表示添加所有文件
```



<img src="https://namebucket.oss-cn-beijing.aliyuncs.com/img/git%E5%90%91%E6%9A%82%E5%AD%98%E5%8C%BA%E4%B8%AD%E6%B7%BB%E5%8A%A0%E6%96%87%E4%BB%B6.png" alt="image-20220209061731693" style="zoom:80%;" />

### git commit—保存仓库历史记录

```bash
git commit -m "任意一条描述信息"
```

将当前暂存区中的文件实际保存到仓库的历史记录中。通过这些记录, 我们就可以在工作树中复原文件  

<img src="https://namebucket.oss-cn-beijing.aliyuncs.com/img/git%E6%8F%90%E4%BA%A4%E5%B9%B6%E4%BF%9D%E5%AD%98%E4%BB%93%E5%BA%93%E5%8E%86%E5%8F%B2%E8%AE%B0%E5%BD%95.png" alt="image-20220209062325331" style="zoom:80%;" />

### git log—查看提交日志

```bash
git log
```

<img src="https://namebucket.oss-cn-beijing.aliyuncs.com/img/git%E6%9F%A5%E7%9C%8B%E6%8F%90%E4%BA%A4%E6%97%A5%E5%BF%97.png" alt="image-20220209062416281" style="zoom:80%;" />

### git remote add—关联远程仓库

```bash
git remote add <name> <project_url>
```

1. 在GitHub上建立与本地仓库名相同的远程仓库<u>(注意不要勾选自动生成README.md文件)</u>

2. 将本地仓库和远程仓库关联起来

   ```bash
   git remote add origin https://github.com/yimeisuren/origin #origin为远程仓库名
   ```

### git push—推送至远程仓库

```bash
git push -u origin master #将本地仓库中的当前分支推送到orgin中的master分支中
```

`git push`操作只允许操作最新版本的远程库, 否则会push失败. 

> 以一个实际场景为例进行说明
>
> 1. 在配置好本地仓库和远程仓库之后, 将本地文件push到远程仓库中
> 2. 发现不小心将一些隐私文件进行了上传, 登录到github上进行手动删除
> 3. 在之后某天, 对本地仓库的一些文件进行增删改之后, 重新push, 此时发现push失败
>
> 上面失败的原因在于, 当在github上手动删除文件时, 此时本地仓库的版本和远程仓库中的版本不一致, 因此无法直接push, 需要先pull, 然后再进行push操作. 

### 版本回退

1. 使用`git log`或者`git reflog`命令查看`commitID`属性
2. 使用`git reset --hard <commitID>`来进行版本回退

### 文件忽略

在当前git目录下创建`.gitignore`文件, 在其中配置想要忽略的文件

# 分支

### 创建分支

<u>语法</u>: `git branch <branchName>`



### 查看分支

<u>语法</u>: `git branch`

从下面的示例可以看出, 创建了一个名为`X`的分支, 并显示了出来

```bash
C:\Users\24563\Downloads\Documents\XNote>git branch X
C:\Users\24563\Downloads\Documents\XNote>git branch
  X
* master
```

### 切换分支和当前分支Head

当前分支是指在本地工作区中操作的那个分支. Head指向哪个分支, 则那个分支即为<u>当前分支</u>. 可以使用`git log`查看当前分支, 如下所示当前分支为master分支. 

```bash
C:\Users\24563\Downloads\Documents\XNote>git log
commit 93a491c96359852a42ffff8c7300ac3640416cd7 (HEAD -> master, XNote/master, X)
Author: yimeisuren <2456387473@qq.com>
Date:   Fri Jun 10 10:16:07 2022 +0800

    重新提交
```

使用`git checkout -b <branchName>`进行切换分支, 添加`-b`选项表示如果分支不存在, 则先进行创建分支, 再进行切换, 如下所示

```bash
C:\Users\24563\Downloads\Documents\XNote>git checkout X
Switched to branch 'X'
M       vue-admin-template
M       "\350\275\257\344\273\266\351\241\271\347\233\256\347\256\241\347\220\206/Git\345\222\214GitHub.md"

C:\Users\24563\Downloads\Documents\XNote>git log
commit 93a491c96359852a42ffff8c7300ac3640416cd7 (HEAD -> X, XNote/master, master)
Author: yimeisuren <2456387473@qq.com>
Date:   Fri Jun 10 10:16:07 2022 +0800

    重新提交
```

### 合并分支

<u>语法</u>: `git merge <branchName>`

将`<branchName>`分支中的提交内容合并到当前分支中

