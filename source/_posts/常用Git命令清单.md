---
title: "常用Git命令清单"
date: 2022-05-18 21:34:58
updated: 2022-09-18 15:53:16
categories:
  - "随笔"
tags:
  - "Git"
aging: true
aging_days: 30
---

我每天使用 `Git`, 但是很多命令记不住. 一般来说, 日常使用只要记住下图6个命令, 就可以了. 但是熟练使用, 恐怕要记住60～100个命令.

<!--more-->

![流程图]()  
下面是我整理的常用`Git`命令清单. 几个专用名词的译名如下.

- Workspace：工作区
- Index / Stage：暂存区
- Repository：仓库区（或本地仓库）
- Remote：远程仓库

详细教程参考: [Git 教程 缪雪峰](https://www.liaoxuefeng.com/wiki/896043488029600)  
转载: [常用 Git 命令清单 阮一峰](https://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)

### 新建代码库

```plaintext
# 在当前目录新建一个Git代码库
$ git init

# 新建一个目录，将其初始化为Git代码库
$ git init [project-name]

# 下载一个项目和它的整个代码历史
$ git clone [url]
```

### 配置

Git 的设置文件为 `.gitconfig`, 它可以在用户主目录下(全局配置)，也可以在项目目录下(项目配置)

```plaintext
# 显示当前的Git配置
$ git config --list

# 编辑Git配置文件
$ git config -e [--global]

# 设置提交代码时的用户信息
$ git config [--global] user.name "[name]"
$ git config [--global] user.email "[email address]"
```

### 添加/删除文件

```plaintext
# 添加指定文件到暂存区
$ git add [file1] [file2] ...

# 添加指定目录到暂存区，包括子目录
$ git add [dir]

# 添加当前目录的所有文件到暂存区
$ git add .

# 添加每个变化前，都会要求确认
# 对于同一个文件的多处变化，可以实现分次提交
$ git add -p

# 删除工作区文件，并且将这次删除放入暂存区
$ git rm [file1] [file2] ...

# 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [file]

# 改名文件，并且将这个改名放入暂存区
$ git mv [file-original] [file-renamed]
```

对于文件删除操作使用`git restore --staged <deleted file>`(撤销删除操作)  
丢弃暂存区该次删除操作, 在`git checkout -- <delete file>` (丢弃删除操作)

test.txt（2行文字） ——add——commit——rm——checkout——这样删除了你会获得test.txt（2行文字）

test.txt （2行文字）——add——commit——修改增加test.txt的内容（3行文字）——rm——checkout——这样删除了你会获得test.txt（2行文字）

PS:  
只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容

说白了就是最后一次提交以后，你做出的修改因为没有提交到版本库，所以删除文件再从版本库中进行恢复，恢复的是提交的最新版本，这里面并不包括你刚刚做出的修改。

### 代码提交

```plaintext
# 提交暂存区到仓库区
$ git commit -m [message]

# 提交暂存区的指定文件到仓库区
$ git commit [file1] [file2] ... -m [message]

# 提交工作区自上次commit之后的变化，直接到仓库区
$ git commit -a

# 提交时显示所有diff信息
$ git commit -v

# 使用一次新的commit，替代上一次提交
# 如果代码没有任何新变化，则用来改写上一次commit的提交信息
$ git commit --amend -m [message]

# 重做上一次commit，并包括指定文件的新变化
$ git commit --amend [file1] [file2] ...
```

### 分支

```plaintext
# 列出所有本地分支
$ git branch

# 列出所有远程分支
$ git branch -r

# 列出所有本地分支和远程分支
$ git branch -a

# 新建一个分支，但依然停留在当前分支
$ git branch [branch-name]

# 新建一个分支，并切换到该分支
$ git switch -c <name>

# 新建一个分支，指向指定commit
$ git branch [branch] [commit]

# 新建一个分支，与指定的远程分支建立追踪关系
$ git branch --track [branch] [remote-branch]

# 切换到指定分支，并更新工作区
$ git switch <name>

# 切换到上一个分支
$ git checkout -

# 建立追踪关系，在现有分支与指定的远程分支之间
$ git branch --set-upstream [branch] [remote-branch]

# 合并指定分支到当前分支
$ git merge [branch]

# 选择一个commit，合并进当前分支
$ git cherry-pick [commit]

# 删除分支
$ git branch -d [branch-name]

# 删除远程分支
$ git push origin --delete [branch-name]
$ git branch -dr [remote/branch]
```

### 标签

```plaintext
# 列出所有tag
$ git tag

# 新建一个tag在当前commit
$ git tag [tag]

# 新建一个tag在指定commit
$ git tag [tag] [commit]

# 删除本地tag
$ git tag -d [tag]

# 删除远程tag
$ git push origin :refs/tags/[tagName]

# 查看tag信息
$ git show [tag]

# 提交指定tag
$ git push [remote] [tag]

# 提交所有tag
$ git push [remote] --tags

# 新建一个分支，指向某个tag
$ git checkout -b [branch] [tag]
```

### 查看信息

```plaintext
# 显示有变更的文件
$ git status

# 显示当前分支的版本历史
$ git log

# 显示commit历史，以及每次commit发生变更的文件
$ git log --stat

# 搜索提交历史，根据关键词
$ git log -S [keyword]

# 显示某个commit之后的所有变动，每个commit占据一行
$ git log [tag] HEAD --pretty=format:%s

# 显示某个commit之后的所有变动，其"提交说明"必须符合搜索条件
$ git log [tag] HEAD --grep feature

# 显示某个文件的版本历史，包括文件改名
$ git log --follow [file]
$ git whatchanged [file]

# 显示指定文件相关的每一次diff
$ git log -p [file]

# 显示过去5次提交
$ git log -5 --pretty --oneline

# 显示所有提交过的用户，按提交次数排序
$ git shortlog -sn

# 显示指定文件是什么人在什么时间修改过
$ git blame [file]

# 显示暂存区和工作区的差异
$ git diff

# 显示暂存区和上一个commit的差异
$ git diff --cached [file]

# 显示工作区与当前分支最新commit之间的差异
$ git diff HEAD

# 显示两次提交之间的差异
$ git diff [first-branch]...[second-branch]

# 显示今天你写了多少行代码
$ git diff --shortstat "@{0 day ago}"

# 显示某次提交的元数据和内容变化
$ git show [commit]

# 显示某次提交发生变化的文件
$ git show --name-only [commit]

# 显示某次提交时，某个文件的内容
$ git show [commit]:[filename]

# 显示当前分支的最近几次提交
$ git reflog
```

### 远程同步

**关联**  
要关联一个远程库，使用命令`git remote add origin git@server-name:path/repo-name.git`  
关联一个远程库时必须给远程库指定一个名字，origin是默认习惯命名；  
关联后，使用命令`git push -u origin master`第一次推送master分支的所有内容；

此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改；  
如果添加的时候地址写错了，或者就是想删除远程库，可以用`git remote rm <name>`命令。使用前，建议先用`git remote -v`查看远程库信息  
此处的“删除”其实是解除了本地和远程的绑定关系，并不是物理上删除了远程库。远程库本身并没有任何改动。要真正删除远程库，需要登录到GitHub，在后台页面找到删除按钮再删除。

**克隆**  
要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone`命令克隆。

现在，假设我们从零开发，那么最好的方式是先创建远程库，然后，从远程库克隆。  
`git clone git@github.com:michaelliao/gitskills.git`

PS: Git支持多种协议，包括https，但ssh协议速度最快。

```plaintext
# 下载远程仓库的所有变动
$ git fetch [remote]

# 显示所有远程仓库
$ git remote -v

# 显示某个远程仓库的信息
$ git remote show [remote]

# 增加一个新的远程仓库，并命名
$ git remote add [shortname] [url]

# 取回远程仓库的变化，并与本地分支合并
$ git pull [remote] [branch]

# 上传本地指定分支到远程仓库
$ git push [remote] [branch]

# 强行推送当前分支到远程仓库，即使有冲突
$ git push [remote] --force

# 推送所有分支到远程仓库
$ git push [remote] --all
```

### 撤销/时光回溯

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git restore <file>`

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git restore --staged <file>`，就回到了场景1，第二步按场景1操作(这里也就解释了是撤销的add这个操作 工作区没变 所以需要再次使用第一步)。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，git reset –hard commit_id。结果：工作区文件、暂存区文件、本地仓库都回退，不过前提是没有推送到远程库。

HEAD指向的版本是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset –hard commit_id。  
(HEAD^ HEAD^^……..HEAD~100 : 利用数字回退版本 上面是利用commit_id)

穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。 (此时在commit时携带的m就有用了 知道干了什么才能准确回退)

要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。

```plaintext
# 恢复暂存区的指定文件到工作区   白话文: 把workspace的修改全部撤销(是把未add之前在workspace的修改全部撤销)
$ git restore <file> 

# 把暂存区的修改回退到工作区(这里的修改是指add这个操作！！！ 撤销的是操作一定记住！ 并不是文档内的内容 , git add git rm 这些添加删除都叫修改！)
$ git restore --staged <file>  

# 恢复某个commit的指定文件到暂存区和工作区
$ git checkout [commit] [file]

# 恢复暂存区的所有文件到工作区
$ git checkout .

# 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
$ git reset [file]

# 重置暂存区与工作区，与上一次commit保持一致
$ git reset --hard

# 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
$ git reset [commit]

# 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
$ git reset --hard [commit]

# 重置当前HEAD为指定commit，但保持暂存区和工作区不变
$ git reset --keep [commit]

# 新建一个commit，用来撤销指定commit
# 后者的所有变化都将被前者抵消，并且应用到当前分支
$ git revert [commit]

# 暂时将未提交的变化移除，稍后再移入
$ git stash
$ git stash pop
```

### 其他

```plaintext
# 生成一个可供发布的压缩包
$ git archive
```

### 待续……

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

用`git log --graph`命令可以看到分支合并图。  
用带参数的`git log`也可以看到分支的合并情况：

`git log --graph --pretty=oneline --abbrev-commit`

通常，合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。

如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

使用`Fast forward`模式，指针直接指向dev  
禁用`Fast forward`模式，指针向后平移一个新的commit

开发一个新feature，最好新建一个分支；

如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。
