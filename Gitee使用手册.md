# Gitee使用手册

## Git和Github/Gitee

- Git是一个分布式的版本控制系统，只是软件，需要你下载装到电脑上，实现git功能。
- Github、BitBucket、Gitee基于git的项目托管平台，说白了是云服务器或云盘，存储分享你的代码，查看追更别人的代码。 理解了这些，大概就能明白有一堆程序员所在的Github为什么被戏称是全球最大的同性交友平台这个梗了。Github、BitBucket是国外的，连接速度因人而异；另外Github收费用户才能创建私有项目。

## 前提

1. [注册码云(Gitee)](https://gitee.com/)，创建一个项目，得到项目url：`https://gitee.com/YourGiteeName/projectname`
2. [下载git安装](https://git-scm.com/downloads)， 全都按下一步就行了
3. [下载VSCode安装](https://code.visualstudio.com/)

## 添加ssh公钥

1. 打开命令行窗口或者Git Bash
2. 生成sshkey

    ```shell
    # -C 后面自定义
    ssh-keygen -t rsa -C "youremail@xxx.com"

    # Generating public/private rsa key pair...
    # 三次回车即可生成 ssh key
    ```

3. 添加public key

    ```shell
    cat ~/.ssh/id_rsa.pub
    # ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC6eNtGpNGwstc....

    #打开码云SSH公钥管理页面 https://gitee.com/profile/sshkeys

    #填写标题：自定义

    #公钥：ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC6eNtGpNGwstc...

    #添加后，回到Git Bash中输入

    ssh -T git@gitee.com
    ```

## 初始化Git

```shell
# 你的名字或昵称
git config --global user.name yourname
# 你的邮箱
git config --global user.email youremail@xxx.com
```

## 创建仓库并初始化

```shell
mkdir YourProjName
cd YourProjName
# 初始化仓库
git init

```

## 同步仓库

```shell
# 拉取(同步)远程已存在仓库
git remote add origin https://gitee.com/YourGiteeName/YourProjName.git
# 其中origin代表的是你远程的仓库，习惯如此命名，可以通过命令 git remote -v 查看

git remote -v
# origin  https://gitee.com/YourGiteeName/YourProjName.git (fetch)
# origin  https://gitee.com/YourGiteeName/YourProjName.git (push)

# 同步，也可以称之为拉取，在Git中是非常频繁的操作，和SVN不同，Git的所有仓库之间是平等的，所以，为了保证代码一致性，尽可能的在每次操作前进行一次同步操作，具体的为在工作目录下执行如下命令:

git pull origin master
# master是分支名，如果你本地是其他分支，请换成其他分支的名字，另，因为远程仓库与你本地仓库可能存在冲突

# 冲突时，先将远程库和本地合并
git pull --rebase origin master
# 再执行推送
git push origin master

```

## 提交

```shell
# git作为支持分布式版本管理的工具，它管理的库（repository）分为本地库、远程库。

# 这里我们把 add(暂存)、提交(commit)、推送(push)，放到一起说，因为每次上传代码都需要执行这三步（关于冲突处理、分支合并等以后用到了再研究，本文只说基础部分）。

git add     # 加入到暂存区
git commit  # 提交到本地库
git push    # 发送给远程库
# 首先，我们打开 README.md ，在里面稍稍加上几个字，保存。这样文件就做了修改。

# 再来查看git状态

git status
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#         modified:   README.md
#
# no changes added to commit (use "git add" and/or "git commit -a")
# 会提示你modified: README.md ，意思是这个文件被修改了。no changes added to commit 是说目前暂时没有文件放到暂存区。

# 所以我们将文件加入暂存区。

git add -A
# -A表示将所有文件的修改，文件的删除，文件的新建，都添加到暂存区。

# 然后提交到本地库，并附加注释。

git commit -m "第一次提交"
# [master 1cc3dd5] 第一次提交
#  1 file changed, 1 insertion(+), 1 deletion(-)
# -m后面的是本次提交的说明，通常可以备注你改了什么，便于以后翻看历史记录时，能直观知道这是哪个版本，这个版本改了些什么东西。

# 最后推送到远程库，也就是Gitee上的项目里。

git push origin master
# Counting objects: 3, done.
# Writing objects: 100% (3/3), 297 bytes | 297.00 KiB/s, done.
# Total 3 (delta 0), reused 0 (delta 0)
# To https://gitee.comYourGiteeName/YourProjName.git
#    5464c11..1cc3dd5  master -> master

```
