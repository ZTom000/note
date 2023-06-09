# Git 食用指南

## [toc]

## 1. 常用指令

### 1.1. 更改 git 用户

```shell
git config --global user.mail "ztmooN@qq.com"
```

### 1.2. 上传与拉取 git 项目代码

```shell
# 上传代码
# 1. 添加所有已修改文件
git add ./
# 2. commit 
git commit -m "commitName"
# 3. 上传
git push
# 拉取代码
git pull
```

### 1.3. 解决版本冲突

```shell
# 查看版本代码差异
git diff 
```

### 1.4. 分支管理

```shell
# 添加远程分支
git remote add origin/branch_name url

# 查看本地分支
git branch

# 创建本地分支
git branch -d branch_name

# 删除本地分支
git branch -D branch_name

# 切换本地分支
git checkout -b local_branch_name remote_branch_name
# 本地切换分支失败时，可尝试先更新远程分支
git remote update origin --prune
```

### 1.5. 撤销提交

说明：`HEAD^` 表示上一个版本,如果撤销两次`commit`则可以使用`HEAD~2`

`--soft` 不删除工作空间的改动代码,只撤销`commit`,不撤销`git add file`

`--hard` 删除工作空间的改动代码,撤销`commit`且撤销`add`

```shell
# 撤销 commit 
git reset --soft HEAD^
# 修改 commit 注释
git commit --amend
# 撤销 commit && add
git reset --hard HEAD^
# 撤销操作到指定版本
git reset --hard commitid
##########################################################################
# 查看 commit 记录
git reflog
# 撤销到指定版本 commit 
git reset --hard commit_id
```

### 1.6. 远程仓库管理

```shell
# 查看远程仓库
git remote -v

# 添加远程仓库
git remote add name url

# 删除远程仓库
git remote remove name
```

### 1.7. 版本控制

### 1.7.1 Release 版本

### 1.7.2 Tags

### 1.8. GIT 主分支同步到其他分支

```shell
# 将本地项目切换到主分支
git checkout main 

# 拉取远程项目最新的代码
git pull

# 切换到自己的分支
git checkout dev

# 将 main 主分支的代码合并到 dev 分支上
git merge main

# 将主分支代码推送到远程分支
git push origin dev
```