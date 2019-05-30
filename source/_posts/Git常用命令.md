---
title: Git使用总结
date: 2019-04-26 10:08:12
categories: 
   - [Git]
tags:
---
## 克隆现有的仓库
```
$ git clone https://github.com/libgit2/libgit2
```

## 在现有项目中初始化仓库，需要使用如下几个命令
```
$ git init 

$ git remote add origin git@github.com:congkaiwu/test.git 

$ git remote -v 
origin  git@github.com:congkaiwu/test.git (fetch)
origin  git@github.com:congkaiwu/test.git (push)

$ git fetch
remote: Enumerating objects: 9, done.
remote: Counting objects: 100% (9/9), done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 9 (delta 0), reused 6 (delta 0), pack-reused 0
Unpacking objects: 100% (9/9), done.
From github.com:congkaiwu/test
 * [new branch]      master     -> origin/master

$ git checkout master

```
   1. `$ git init` 初始化仓库，在当前目录下创建一个.git文件。
   2. `$ git remote add [name] [url]` 添加一个新的远程 Git 仓库，同时指定一个仓库简写名称，使用clone命令时默认名称为 origin 。 可以添加多个远程仓库。
   3. `$ git remote -v` 列出远程仓库使用的 Git 保存的简写与其对应的 URL。 不使用-v选项则只列出仓库的简写名称
   4. `$ git fetch` 这个命令会访问远程仓库，从中拉取所有你还没有的数据。 执行完成后，你将会拥有那个远程仓库中所有分支的引用，可以随时合并或查看。
   5. `$ git checkout master` 检出 master 分支。

## 跟踪/暂存/提交
```
$ git status

$ git add newFile.txt

$ git commit -m "add newFile.txt"

$ git rm newFile.txt

```
   1. `$ git status` 检查当前文件状态。
   2. 项目中<font color=red>添加或修改文件</font>，需要使用命令 `$ git add` 跟踪并暂存本次修改。
   3. `git commit -m "add new File.txt"` 提交当前改动，-m选项添加本次提交的描述。
   4. `git rm newFile.txt` 删除文件

## 查看提交历史，回退
```
$ git log --since=2.weeks --pretty=oneline
30a3e17c27196f086b45e31cc0446d0f8b113ef1 (HEAD -> master) del new.txt
6614fd3a91290f343b416f5450a6fcda56f40b2a add newFile.txt
adaa9611013e8cb93261e2a134d3e0e52fa18731 (origin/master) add 1111
9fb179a35d8ef8c3424fe50e02fe1ecb6eb5d86f a
ace7c393e245a6ace32cc200a52486d3fcd8bff4 Initial commit

$ git log --pretty=format:"%h - %an, %ar : %s" -2
30a3e17 - congkaiwu, 5 minutes ago : del new.txt
6614fd3 - congkaiwu, 41 minutes ago : add newFile.txt

$ git log --grep=add --author=congkaiwu --all-match
commit 6614fd3a91290f343b416f5450a6fcda56f40b2a
Author: congkaiwu <congkaiwu@gmail.com>
Date:   Fri Apr 26 08:03:06 2019 +0800

    add newFile.txt

commit adaa9611013e8cb93261e2a134d3e0e52fa18731 (origin/master)
Author: congkaiwu <congkaiwu@gmail.com>
Date:   Thu Apr 25 22:37:40 2019 +0800

    add 1111

$ git reset --hard ace7c
HEAD is now at ace7c39 Initial commit

```
   1. `$ git log --since=2.weeks --pretty=oneline` 查询提交历史，按时间列出所有改动，最近的时间在上面。`--since=2.weeks` 查询2周以内的提交。 `--pretty=oneline` 每条结果显示为1行。
   2. `$ git log --pretty=format:"%h - %an, %ar : %s" -2` 也可以格式化查询结果。-2表示显示最近的2条数据。
   3. `$ git log --grep=add --author=congkaiwu --all-match` 查询关键字 `--grep=`，作者 `--author=` ，同时满足作者和关键字条件需要增加选项`--all-match` 。
   4. `$ git reset --hard ace7c` 该命令回退到ace7c这个节点。

## 分支新建与合并，删除
```
$ git branch -a
* master
  remotes/origin/master

$ git checkout -b iss53
Switched to a new branch 'iss53'

$ git checkout master
Switched to branch 'master'
Your branch is behind 'origin/master' by 2 commits, and can be fast-forwarded.
  (use "git pull" to update your local branch)

$ git merge iss53
Updating 8d49b2b..6853154
Fast-forward
 newFile.txt | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 newFile.txt

$ git branch -d iss53
Deleted branch iss53 (was 8d49b2b).

```
   1. `$ git branch -a` 列出本地分支和远程分支，-r 则单独列出远程分支，-v 可以查询每个分支最后一次提交。
   2. `$ git checkout -b iss53` 创建分支 iss53 并切换到该分支。
   3. `$ git checkout master` 切换到分支 master。
   4. `$ git merge iss53` 合并分支。
   5. `$ git branch -d iss53` 删除分支iss53。

## 第一次将本地项目推送到github
```
$ git init
Initialized empty Git repository in D:/WebProjects/express-demo/.git/

$ git add .
warning: LF will be replaced by CRLF in package-lock.json.
The file will have its original line endings in your working directory
warning: LF will be replaced by CRLF in package.json.
The file will have its original line endings in your working directory

$ git commit -m 'First commit.'
[master 128688c] First commit.
 9 files changed, 636 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 index.js
 create mode 100644 middleware/authentication.js
 create mode 100644 middleware/logger.js
 create mode 100644 package-lock.json
 create mode 100644 package.json
 create mode 100644 public/readme.txt
 create mode 100644 routes/courses.js
 create mode 100644 routes/home.js

$ git remote add origin git@github.com:congkaiwu/express-demo.git

$ git pull --rebase origin master
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:congkaiwu/express-demo
 * branch            master     -> FETCH_HEAD
 * [new branch]      master     -> origin/master

$ git push origin master -u
Enumerating objects: 15, done.
Counting objects: 100% (15/15), done.
Delta compression using up to 8 threads
Compressing objects: 100% (11/11), done.
Writing objects: 100% (14/14), 7.21 KiB | 738.00 KiB/s, done.
Total 14 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), done.
To github.com:congkaiwu/express-demo.git
   eb1c5dd..128688c  master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.

```
   1. 在本地项目下使用`git init`初始化，`git add .`暂存改动，`git commit -m 'First commit.'`提交。
   2. 在GitHub上创建仓库并链接到本地项目`$ git remote add origin git@github.com:congkaiwu/express-demo.git`。
   3. 创建时如果勾选了README文件，则用`$ git pull --rebase origin master`命令合并到本地。
   4. 首次推送使用`$ git push origin master -u`。


> More info: [Git官方文档](https://git-scm.com/book/en/v2)