### Git

git 是一个分布式版本控制软件

三种工作区域

1. Git 的本地仓库：在 .git 目录中
2. 工作区：用户操作目录
3. 暂存区:在 .git 目录中

![12](/Users/wx/project/interview/docs/秘籍/images/12.jpeg)

三种状态

1. 已提交（committed）:该文件已经被安全地保存在本地仓库中
2. 已修改（modified）:修改了某个文件，但还没有提交保存
3. 已暂存（staged）:把已修改的文件放在下次提交时要保存的清单中

![13](/Users/wx/project/interview/docs/秘籍/images/13.jpeg)

Git 分支初识

1. Git 中的分支，其实本质上仅仅是个指向 commit 对象的可变指针。
2. Git 会使用 master 作为分支的默认名字。在若干次提交后，你其实已经有了一个指向最后一次提交对象的 master 分支，它在每次提交的时候都会自动向前移动。
3. Git 鼓励在工作流程中频繁使用分支与合并。

![11](/Users/wx/project/interview/docs/秘籍/images/11.png)



#### 1.分支

```shell
# 新建一个分支，但依然停留在当前分支
$ git branch [branch-name

# 删除分支
$ git branch -d [branch-name]

# 切换到指定分支，并更新工作区
$ git checkout [branch-name]

# 切换到上一个分支
$ git checkout -

# 合并指定分支到当前分支
$ git merge [branch]
```

#### 2.提交

```shell
# 提交暂存区到仓库区
$ git commit -m [message]

# 提交暂存区的指定文件到仓库区
$ git commit [file1] [file2] ... -m [message]
```

#### 3.添加删除文件

```shell
# 添加指定文件到暂存区
$ git add [file1] [file2] ...

# 添加指定目录到暂存区，包括子目录
$ git add [dir]

# 添加当前目录的所有文件到暂存区
$ git add .

# 删除工作区文件，并且将这次删除放入暂存区
$ git rm [file1] [file2] ...

# 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [file]
```

#### 4.远程同步

```shell
# 下载远程仓库的所有变动
$ git fetch [remote]

# 取回远程仓库的变化，并与本地分支合并
$ git pull [remote] [branch]

# 上传本地指定分支到远程仓库
$ git push [remote] [branch]

# 强行推送当前分支到远程仓库，即使有冲突
$ git push [remote] --force

# 推送所有分支到远程仓库
$ git push [remote] --all
```

#### 5.撤销

```shell
# 恢复暂存区的指定文件到工作区
$ git checkout [file]

# 恢复某个commit的指定文件到暂存区和工作区
$ git checkout [commit] [file]

# 恢复暂存区的所有文件到工作区
$ git checkout .

# 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
$ git reset [commit]
```

###