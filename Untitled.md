# 新建仓库

## git init

```bash
#当前目录新建一个Git仓库
git init
#当前目录下新建一个目录，将其初始化为Git仓库
git init [project-name]
```

## git clone

git clone相当于执行了一系列命令（）：

- 首先初始化一个目录并初始化成git仓库 **git init [远程仓库名]**
- 与远程仓库建立关联 
  **git remote add origin git@gitee.com:test/git-learn.git**
- 默认拉取远程仓库所有分支数据
  **git fetch** 
- 在本地创建并切换到远程仓库的默认分支，并与其建立关联
  **git checkout -b [master] [origin/master]**

```bash
#克隆远程仓库到本地
git clone [url]
# -b 指定要克隆的分支，默认为master或main
git clone -b feature [url]
```

# 配置更改

## git config

`git config` 命令用于配置 Git 的各种设置和选项。通过这个命令，你可以设置用户信息、编辑器、合并工具、别名等。

```bash
#显示当前git配置
git config --list
#编辑git的配置文件
git config -e [--global]
```

设置提交代码时的用户名和邮箱，每次Git提交都会使用该信息

```bash
git config [--global] user.name "admin"
git config [--global] user.email "admin@xxx.com"
```

# 基础操作

## git status

`git status` 命令用于显示当前 Git 仓库的状态信息。它提供了以下几个重要信息：

- **当前分支**：显示你当前所在的分支。
- **工作目录状态**：显示哪些文件被修改或创建但还没有暂存（`modified`）。
- **暂存区状态**：显示哪些文件被修改并且已暂存（`staged`）等待提交。
- **合并冲突**：如果存在合并冲突，`git status` 会提示相关的冲突文件

```bash
#查看状态
git status
```

## git add

添加文件到暂存区，一般直接git add .

```bash
#添加当前目录所有文件到暂存区
git add .
```

```bash
#添加指定文件到暂存区
git add [file1] [file2]
#添加制定目录到暂存区，包括子目录
git add [dir]
#添加每个变化前，都会要求确认，对于懂一个文件到多处变化，可以实现分次提交
git add -p
```

## git rm/mv

```bash
#删除工作区和暂存区的该文件
git rm [file]
#只删除暂存区的该文件
git rm --cached [file]
#改名文件，并且将这个改名放入暂存区
git mv [file-original] [file-renamed]
```

## git commit

基本命令，-m 指定提交信息

```bash
#提交暂存区到本地仓库中
git commit -m [message]
#指定暂存区特定文件提交
git commit [file1] ... -m [message]
#直接将工作区文件添加到暂存区并提交
git commit -a
#提交时候显示所有diff
git commit -v

#重做上一次commit，并包括指定文件的新变化
#常用于提交后发现有遗漏或错误
git commit --amend -m [message]
```

# 分支相关

## 新建/删除/切换分支

```bash
#显示本地所有分支
git branch
#列出所有远程分支
git branch -r
#列出所有本地分支和远程分支
git branch -a
#新建一个分支，但是依然停留在当前分支
git branch [branch-name]

#删除分支
git branch -D [branch-name]
#删除远程分支
git push origin --delete [branch-name]
git branch -dr [remote/branch]

#切换到指定分支，并更新工作区
git checkout [branch-name]
#新建一个分支，并且切换到该分支
git checkout -b [branch]
#新建一个分支，并且切换到该分支，并指定这个分支的起始点
#不指定起点，默认就是当前分支
git checkout -b [branch] [branch]
#切换到上一个分支
git checkout -
```

## 分支合并

**git rebase [branch]**：变基操作，就是将当前的分支**自基底即公共祖先的所有提交迁移到目标分支末端**

**git merge [branch]**：合并操作，就是**将当前分支与目标分支的最新提交进行合并，创建一个新的合并提交。**

**git cherry-pick [commit]**：挑选操作，**挑选一个我们需要的 commit移植到当前分支**

```bash
#合并指定分支到当前分支
git merge [branch]
#变基
git rebase [branch]
#选择一个commit，合并到当前分支
git cherry-pick [commit]
```

## **冲突发生与解决**

**两个分支都==对同一个文件的同一个位置做了不同的更改==，当这两个分支合并的时候，Git不知道要采用哪一个更改，便发生了冲突。**

出现冲突时，git会标记冲突文件并显示冲突区域

- 编辑冲突文件，保留或合并所需的更改

- 通过git add标记冲突文件已解决

- merge操作通过git commit创建一个合并提交


rebase操作解决冲突后，可以通过git rebase --conitnue继续执行，

cherry-pick操作也可以使用git cherrry-pick --continue继续执行

## 注意

### 切换分支或远程pull时提醒本地文件会被覆盖

```bash
$ git checkout -
error: Your local changes to the following files would be overwritten by checkout:
        111.txt
Please commit your changes or stash them before you switch branches.
Aborting
```

git stash存储缓存区，git checkout [branch]后，可以选择git stash pop

# 查看信息

```bash
#显示有变更到文件
git status
#显示当前分支到版本历史
git log
#显示commit提交的历史，以及每次commit发生变更的文件
git log --stat

#搜索提交历史，根据关键词
git log -S [keyword]
#显示某个commit之后的所有变动，每个commit占据一行
git log [tag] HEAD --pretty=format:%s
#显示指定文件相关的每一次diff
git log -p [file]
#显示过去5次提交
git log -5 --pretty-oneline
#显示所有提交过的用户，按提交次数排序
git shortlog -sn

#显示指定文件是什么人在什么时间修改过
git blame [file]

#显示暂存区和工作区的差异
git diff
#显示暂存区和上一个commit的差异
git diff --cached [file]
#显示工作区与当前分支最新提交commit之间的差异
git diff HEAD
#显示两次提交之间的差异
git diff [first-branch] [secod-branch]
#显示你今天写了多少代码
git diff --shortstat "@{0 day ago}"

#显示某次提交的元数据和内容变化
git show [commit]
#显示某次提交发生的变化的文件
git show --name-only [commit]
#显示当前分支的最近几次提交
git reflog
```

# 远程操作

## 远程分支

当我们从远程仓库中拉取到数据后，远程分支在本地是以 **`origin/[branch]`** 的形式存在于本地作为远程跟踪分支，它们是**远程分支状态的引用**。

我们通常在本地会有一个与远程分支相关联的本地分支，来进行push操作。

- fetch操作的时候，所有分支的提交记录都会更新到本地 origin/[branch] 远程分支中
- push操作的时候，我们将本地的一个分支推送到远程仓库后，在本地对应的分支origin/[branch] 也会被更新

![image-20241210140740120](D:\E\学习\Go\笔记\assets\image-20241210140740120-1733824508023-2.png)

在一个分支上我们通过 git push 不指定远程仓库和分支就可以直接推送，是因为本地仓库与远程仓库以及本地分支与远程分支建立了关联。

## 建立关联关系

### 关联仓库

将本地仓库与远程仓库建立关联关系，为远程仓库的地址添加一个别名，**这样可以简化操作，让后续的git操作可以直接引用该远程仓库。**

```bash
#显示所有关联的远程仓库
git remote -v
#显示某个远程仓库的信息
git remote show [remote]
#关联一个远程仓库，并命名
git remote add [shortname] [url]
```

### 关联分支

在当前分支上运行 `git status`，如果该分支与远程分支关联，Git 会显示当前分支的状态以及与远程分支的差异。关联方式有以下几种，**一旦关联成功，就可以直接使用git push 和 git pull 而无需指定远程分支**

#### git checkout

创建并切换到对应分支，设置分支的起点为origin/branch，这时候关联关系就已经建立好了。这个是最好用的方法。

```bash
git checkout -b [branch] [origin/branch]
#或者不切换到这个分支上
git branch [branch] [origin/branch]
```

#### git push

**git push**命令本身时推送远程分支的命令，借助`--set-upstream（-u）` 参数，可以指定关联上远程分支，如果对应远程分支不存在会直接创建。

```bash
# 将本地分支上传到远端，同时关联本地分支和远端branch分支
git push -u origin [local branch]
```

#### git branch

将现有的一个分支与远程分支建立关联关系：--set-upstream-to 或 -u

```bash
git branch -u [origin/branch] [branch]
#如果当前在这个分支分支上，可以省略branch
gi branch -u [origin/branch]
```

## git push

git push将本地分支的提交记录同步到远程仓库，命令完整格式如下

```bash
git push origin <本地分支名>:<远程分支名>
```

如果省略远程分支名，则表示将本地分支推送与之存在"追踪关系"的远程分支（通常两者同名），如果该远程分支不存在，则会被新建。

```bash
git push origin [本地分支名]
```

如果当前分支就是要推送的分支，就可以进一步省略本地分支名

```bash
git push origin
```

如果当前分支只有一个追踪分支，那么主机名都可以省略

```bash
git push
```

**其他参数**

```bash
#强行推送当前分支到远程仓库，即使有冲突
git push [remote] --force
#推送所有分支到远程仓库
git push [remote] --all
```

## git fetch

 拉取远程仓库的指定分支的所有提交记录到指定的本地分支，如果本地分支不存在则会直接创建

```bash
git fetch origin <远程分支名>:<本地分支名>
git fetch origin master:test
```

从远程仓库 `origin` 获取指定的远程分支 `[remote branch]` 的更新，并将其存储在本地的远程跟踪分支中

```bash
git fetch origin <远程分支名>
git fetch origin master
```

不指定分支默认拉取指定仓库的所有分支

```bash
git fetch origin
```

不指定仓库将更新git remote 中所有的远程仓库的所有分支

```bash
git fetch
```

## git pull

通过 git fetch 拉取远程仓库的master分支，并与本地test分支合并

```bash
git pull origin master:test
```

拉取远程仓库的master分支，并与当前分支合并

```bash
git pull origin master
```

如果当前分支与远程分支有跟踪关系，则可以省略远程分支

```bash
git pull origin 
```

如果当前分支只有一个追踪分支，连远程主机名都可以省略。

```bash
git pull
```



git pull 是 git pull --merge 的简写，还有一个git pull --rebase的指令，它们的区别显而易见

**`git pull --merge = git fetch + git merge o/main`** 

**`git pull --rebase = git fetch + git rebase o/main`**

## **注意**

### push没有远端分支

```bash
fatal: The current branch feature/file has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin feature/file
```

```
推送错误:当前分支特性/文件没有上游分支。  
要推动当前分支并将远端设置为上游，请使用 ：
git push --set-upstream origin feature/file
```

在于当前这个分支没有和任何的仓库发生关联，origin是仓库的一个指针，需要将当前的分支与这个指针之间形成关联。执行如下命令之后会在远程仓库上自动新建本分支。

```bash
git push --set-upstream origin feature/file
```

### 本地分支落后远程分支无法push

直接git push --force，但也有可能出错，这是远程仓库为了防止提交历史丢失，不能push，需要先pull

```bash
User@WIN-LS10VRVA6GR MINGW64 /d/Go/project/src/code-repo/code-go (bugfix-xingchuang_webhook4)
$ git push --force
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
remote: error: denying non-fast-forward refs/heads/bugfix-xingchuang_webhook4 (you should pull first)
To https://osc.gitee.work/enterprise/enterprise__Tomato-Scrum/code-go.git
 ! [remote rejected]     bugfix-xingchuang_webhook4 -> bugfix-xingchuang_webhook4 (non-fast-forward)
error: failed to push some refs to 'https://osc.gitee.work/enterprise/enterprise__Tomato-Scrum/code-go.git'
```



# 撤销

## git checkout

```bash
#恢复暂存区的指定文件到工作区
git checkout [files]  
#恢复某个commit到(可指定文件)到暂存区和工作区
git chekcout [commit]
git checkout [commit] [file]
#退回到之前1天到版本
git log --before="1 days"
```

## git reset 

```bash
#重置当前分支为指定commit，但保持暂存区和工作区不变
git reset --keep [commit/head/master]
#重置当前分支为指定commit，同时重置暂存区，但工作区不变
git reset [commit/head/master]
#重置当前分支为指定commit，同时重置暂存区和工作区
git reset --hard [commit/head/master]
#默认重置与上一次commit保持一致
git reset --hard
```

## git revert

```bash
#新建一个commit，用来撤销指定commit，后者所有变化都将被前者抵消，并且应用到当前分支
git resvert [commit]
```

## git stash

```bash
#暂时将未提交到变化保存起来，稍后在移入
git stash
git stash pop
git stash clear
git stash list
```



# 标签

```bash
#列出所有本地标签
git tag
#基于最新提交创建标签
git tag <tag-name>
#删除标签
git tag -d <tag-name>
#删除远程tag
git push origin :refstags/[tag-name]
#查看tag信息
git show [tag]
#提交指定tag
git push [remote] [tag]
#提交所有tag
git push [remote] --tags
#新建一个分支，指向某个tag
git checkout -b [branch] [tag]
```
