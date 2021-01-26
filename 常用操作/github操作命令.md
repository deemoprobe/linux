# GitHub常用操作命令

## Git和GitHub

git是分散型版本管理系统，GitHub为开发者提供Git仓库的托管服务，社会化协作代码管理平台。

## 准备

- 安装Git
- 安装vscode（Linux平台忽略此操作）
- 注册GitHub
- Git初始设置
- 配置SSH Key

### Git初始设置

```shell
# git全局配置
git config --global user.name your.name
git config --global user.email your.email
git config --global color.ui auto
# 查看配置
cat ~/.gitconfig
```

### 配置SSH Key

```shell
# 创建SSH Key
ssh-keygen -t rsa -C "your.email"
# Generating public/private rsa key pair...
# 三次回车即可生成 ssh key

# 在GitHub添加公钥（public key）
cat ~/.ssh/id_rsa.pub
# 复制公钥内容
在[设置页面](https://github.com/settings/keys)新建SSH Key认证，名称自定义，把公钥内容粘贴进去
# 公钥私钥认证通信（建立与GitHub连接）
ssh -T git@github.com
# 出现Hi your.name...等输出则配置成功
```

## Git操作

### 初步提交

```shell
# 准备好项目工程空目录/projects/github/
cd ./projects/github/
# 仓库初始化
git init
# 创建自己的工程文件
...
# 向本地暂存区添加自己的文件
git add file.name
# 提交到本地仓库
git commit -m "操作记录说明"

```

### 提交到远程仓库

```shell
# 创建远程仓库
在GitHub上创建一个空仓库-Create a new repository
# 关联远程仓库，origin可自定义别名
git remote add origin https://github.com/deemoprobe/github.git
# 查看仓库别名
git remote -v
# (可选)如果远程仓库已经有文件，先拉下来同步到本地仓库，以默认的main分支为例
git pull origin main
# 推送本地仓库到远程仓库
git push origin main
# (可选)若遇到冲突，可用下面命令先合并文件再提交
git pull --rebase origin main
```

### 分支操作

默认使用main分支，但当存在多人并行作业时可以创建分支进行操作。在合并之前，分支和main之间互不影响。  
注意此处的`origin`是远程仓库的别名(<remote_name>),使用`git remote -v`可以查看别名。

```shell
# 查看当前所在分支
git branch
# 创建分支
git checkout -b <branch_name>
# 切换分支，切换之前需要把分支上的操作先提交
git checkout <branch_name>
# 切换到上次用的分支
git checkout -
# 合并分支（切换到main上合并）
git merge --no-ff deemoprobe
# 查看本地分支和远程分支（也就是查看所有分支）
git branch -a
# 查看远程分支
git branch -r
# 查看本地和远程的分支关联关系
git brancch -vv
# 分支重命名
git branch -m <old_branch_name> <new_branch_name>
# 删除远程老分支
git push origin :<old_branch_name>
# 上传新分支
git push origin <new_branch_name>
# 删除未合并的本地分支
git branch -D -r <branch_name>
# 删除已合并的本地分支
git branch -d -r <branch_name>
# 删除远程分支
git push origin --delete <branch_name>

# 注意：如果是默认分支,是无法删除的,需要在GitHub-->Setting-->Branches更改
# 默认分支后再删除之前的分支
```

### 推送到分支

```shell
# 先切换到对应分支
git checkout deemoprobe
# (可选)在分支中修改文件
...
# 推送到分支
git push -u origin deemoprobe
```

### 回溯历史版本

```shell
# 查看仓库操作日志
git reflog
# 利用哈希值恢复历史版本
git reset --hard hash.code
```

### 克隆仓库到本地

```shell
# 克隆仓库(默认是main分支，标识默认是origin)
git clone repo_name.git
# 获取某一分支到本地
git clone -b <branch_name> repo_name.git
```

### 其他操作

```shell
# 查看仓库状态
git status
# 添加所有文件到本地暂存区
git add A
# 查看日志
git log
# 查看特定文件的git日志
git log file.name
# 以图表的形式查看分支
git log --graph
# 查看更改前后差别
git diff
# 修改提交信息
git commit --amend
```
