# 教程

## 一、介绍

[关于git这一篇就够了](https://blog.csdn.net/bjbz_cxy/article/details/116703787?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522171435085416800226573581%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=171435085416800226573581&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-116703787-null-null.142^v100^pc_search_result_base2&utm_term=git&spm=1018.2226.3001.4187)

Git是一个免费的、开源的分布式版本控制系统，可以快速高效地处理从小型到大型的项目。

### 结构

**工作区—git add—>暂存区—git commit—>本地库**

### 初始化本地仓库

在Git中命令跟Linux是一样的。

1.创建文件夹 repo02

2.设置用户名和邮箱

```bash
git config --global user.name lll8c
git config --global user.email 977255673@qq.com
```

3.初始化仓库

git init初始化仓库生成.git文件夹

```bash
lll@DESKTOP-7T6S7TC MINGW64 /d/learn-git (main)
$ mkdir repo02

lll@DESKTOP-7T6S7TC MINGW64 /d/learn-git (main)
$ cd repo02

lll@DESKTOP-7T6S7TC MINGW64 /d/learn-git/repo02 (main)
$ git init
Initialized empty Git repository in D:/learn-git/repo02/.git/
```

### ssH配置

ssh方式好处：不用每次都进行身份验证

1.cd ~进入用户主目录

2.执行命令生成.ssh目录

`ssh-keygen -t rsa -C 9772555673@qq.com`

在.ssh目录下生成了id_rsa和id_rsa.pub两个文件

3.打开id_rsa.pub文件，复制其中的内容

4.复制到github中

## 二、常用命令

### 1.add和commit

git add将工作区文件添加到暂存区

```bash
lll@DESKTOP-7T6S7TC MINGW64 /d/learn-git/repo02 (main)
$ git add file1.txt
```

git commit将暂存区的所有内容提交到本地库，需要指定一个message，不指定就会进入vim编辑message。

```bash
lll@DESKTOP-7T6S7TC MINGW64 /d/learn-git/repo02 (main)
$ git commit -m "commit1"
```

### 2.git status

查看工作区和暂存区的状态

```bash
lll@DESKTOP-7T6S7TC MINGW64 /d/learn-git/repo02 (main)
$ git status
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        file2.txt

nothing added to commit but untracked files present (use "git add" to track)
```

### 3.log

展示提交日志

`git log`

`git log --oneline`: 显示简洁信息

`git reflog`: 多了信息HEAD@{数字},这个数字的含义是指针回到当前这个历史版本需要走多少步

### 4.reset

前进或后退历史版本

- `git reset --hard [索引]`
  不保存工作区和暂存区
- `git reset --mixed [索引]`
  保存工作区
- `git reset --soft [索引]`
  保存工作区和暂存区

### 5.rm 删除文件

`rm test.txt` 删除工作区的文件

`git add test.txt` 将删除同步到暂存区

### 6.diff 

`git diff test.txt` 将工作区中的文件和暂存区中文件进行比较 

## 三、分支

在版本控制过程中，使用多条线同时推进多个任务。这里面说的多条线，就是多个分支。

同时多个分支可以并行开发，互相不耽误，互相不影响，提高开发效率如果有一个分支功能开发失败，直接删除这个分支就可以了，不会对其他分支产生任何影响。

### 1.查看当前分支

`git branch -v`

```bash
lll@DESKTOP-7T6S7TC MINGW64 /d/learn-git/repo03 (main)
$ git branch -v
branch01 ade750b insert aaa
*main     ade750b insert aaa
```

当前在的分支带*号

### 2.创建分支

`git branch [分支名]`

```bash
lll@DESKTOP-7T6S7TC MINGW64 /d/learn-git/repo03 (main)
$ git branch branch01
```

### 3.切换分支

`git checkout [分支名]`

```bash
lll@DESKTOP-7T6S7TC MINGW64 /d/learn-git/repo03 (main)
$ git checkout branch01
Switched to branch 'branch01'
```

### 4.合并分支

`git merge branch01`

多个分支在同一个文件同一个位置进行修改在合并会发生冲突

commit可以解决这个问题。branch01添加了333，而main分支添加了444，故合并发生冲突，在commit后文件如下

```
111
222
<<<<<<< HEAD
444
=======
333
>>>>>>> branch02
```

## 四、本地库和远程库操作

### 1.初始化本地库

创建一个repo01，cd当仓库当前位置。

使用git init 命令初始化

创建test.txt文件并insert 111，暂存，提交

### 2.创建远程仓库

在github上创建远程仓库repo01

### 3.本地仓库推送

#### 将本地仓库关联到远程仓库

- 点击仓库，获取远程仓库地址，远程仓库地址较长，在关联该仓库时可以取别名
  `git remote add repo01 https://github.com/lll8c/repo01` 
- `git remote -v` 查看关联的仓库
- `git remote remove [仓库名]` 删除关联

#### 本地仓库推送

`git push repo01 main` 将本地库中修改过的内容推送的对应远程仓库对应分支

```bash
$ git push repo01 main
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), 208 bytes | 208.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/lll8c/repo01
 * [new branch]      main -> main
```

#### 加入团队

某个仓库不会允许所有人进行push，在github远程仓库中可以邀请别人加入团队允许push

### 4.克隆仓库

`git clone repo01 main` 将远程库克隆到本地

-b master可以选择分支

```bash
$ git clone https://github.com/lll8c/repo01.git
Cloning into 'repo01'...
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
```

克隆操作可以帮我们完成：
（1）初始化本地库
（2）将远程库内容完整的克隆到本地
（3）替我们创建远程库的别名origin（关联远程库）

```bash
lll@DESKTOP-7T6S7TC MINGW64 ~/Desktop/t/repo01 (main)
$ git remote -v
origin  https://github.com/lll8c/repo01.git (fetch)
origin  https://github.com/lll8c/repo01.git (push)
```

### 5.远程库拉取

#### fetch操作

**拉取操作相当于fetch+merge**

`git fetch repo01 main` 进行数据抓取，只是将远程库的内容下载到本地，但是工作区中的文件并没有更新。工作区中还是原先的内容

```bash
lll@DESKTOP-7T6S7TC MINGW64 ~/Desktop/t/repo01 (main)
$ git fetch origin
remote: Enumerating objects: 7, done.
remote: Counting objects: 100% (7/7), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 6 (delta 1), reused 5 (delta 0), pack-reused 0
Unpacking objects: 100% (6/6), 441 bytes | 33.00 KiB/s, done.
From https://github.com/lll8c/repo01
   b98613e..01034d4  main       -> origin/main
```

抓取后再进行合并，工作区内容就发生变化了

```bash
lll@DESKTOP-7T6S7TC MINGW64 ~/Desktop/t/repo01 (main)
$ git merge origin/main
Updating b98613e..01034d4
Fast-forward
 test2.txt | 1 +
 test3.txt | 1 +
 2 files changed, 2 insertions(+)
 create mode 100644 test2.txt
 create mode 100644 test3.txt
```

#### pull操作

远程库的拉取可以直接利用pull命令来完成

`git pull [远程仓库名] [远程分支名]` ：省略仓库名和分支后默认为origin main

**强制合并**：git pull origin main --allow-unrelated-histories

## 五、golang使用git

### 配置git目录

setting-Version Control-Git

### add和commit

快捷键ctrl+Alt+A 和 ctrl+K

<img src="C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240430181620215.png" alt="image-20240430181620215" style="zoom:67%;" />

当更改内容后，跟本地库不一致的地方会显示绿色

<img src="C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240430182229983.png" alt="image-20240430182229983" style="zoom: 80%;" />

### push 和 pull

![image-20240430184951227](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240430184951227.png)

设置远程库的地址

![image-20240430185909009](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240430185909009.png)

### 从远程库中进行克隆

点击Get from VCS

![image-20240430190639645](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240430190639645.png)

输入远程仓库url和本地存储的地址即可完成克隆

![image-20240430191219236](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240430191219236.png)

### 解决冲突



## 六、忽略文件

创建一个.gitignore文件，将要忽略的文件添加到其中即可

**常用命令：**

`git check-ignore -v <file>`（用于检查指定文件是否被Git忽略，并显示忽略规则的详细信息）

**注意：**

要让.gitignore文件忽略生效，不能是已经git add添加到暂存区中的文件。否则只能将其从暂存区中删除 `git rm --cached [文件名]`。

# 命令大全

## 名词

- master：默认开发分支
- origin：默认远程版本库
- Index/Stage：暂存区
- Workspace：工作区
- Repository：仓库/本地仓库
- Remote：远程仓库

## 新建仓库

```bash
#当前目录新建一个Git仓库
git init
#新建一个目录，将其初始化为Git仓库
git init [project-name]
#克隆远程仓库到本地
git clone [url]
```

## 配置

git的设置文件为.gitconfig，它可以在用户主目录下，可以在在项目目录下。

```bash
#显示当前git配置
git config --list
#编辑git的配置文件
git config -e [--global]
#设置提交代码时的用户信息
git config [--global] user.name "admin"
git config [--global] user.email "admin@xxx.com"
```

## 增加/删除/修改文件

```bash
#查看状态
git status
#查看变更内容
git diff
#添加制定文件到暂存区
git add [file1] [file2]
#添加制定目录到暂存区，包括子目录
git add [dir]
#添加当前目录所有文件到暂存区
git add .
#添加每个变化前，都会要求确认，对于懂一个文件到多处变化，可以实现分次提交
git add -p
#删除工作区文件，并且将这次删除放入暂存区
git rm [file1] [file2]
#停止追踪指定文件，并且将这次删除放入暂存区
git rm --cached [file]
#改名文件，并且将这个改名放入暂存区
git mv [file-original] [file-renamed]
```

## 提交

```bash
#提交暂存区到本地仓库中
git commit -m [message]
#提交暂群区到一些文件到本地仓库中
git commit [file1] [file2] [file3] ... -m [message]
#提交工作区自上次commit之后到变化，直接到本地仓库
git commit -a
#提交时候显示所有diff
git commit -v
#使用一次新的commit，代替上一次提交，如果代码没有任何新变化，则用来写上一次commit的提交信息
git commit --amend -m [message]
#重做上一次commit，并包括指定文件的新变化
git commit --amend [file1] [file2] ...
```

## 分支

```bash
#显示本地所有分支
git branch
#列出所有远程分支
git branch -r
#列出所有本地分支和远程分支
git branch -a
#新建一个分支，但是依然停留在当前分支
git branch [branch-name]
#新建一个分支，与指定的远程分支建立追踪关系
git branch --track [branch] [remote-branch]
#删除分支git branch -D [branch-name]
#删除远程分支
git push origin --delete [branch-name]
git branch -dr [remote/branch]
#新建一个分支，并且切换到该分支
git checkout -b [branch]
#切换到指定分支，并更新工作区
git checkout [branch-name]
#切换到上一个分支
git checkout -
#建立追踪关系，在现有分支与指定到远程分支之间
git branch --set-upstream [branch] [remote-branch]
#合并指定分支到当前分支
git merge [branch]
#选择一个commit，合并到当前分支
git cherry-pick [commit]
```

## 标签

  \#列出所有本地标签

  git tag

  \#基于最新提交创建标签

  git tag <tag-name>

  \#删除标签

  git tag -d <tag-name>

  \#删除远程tag

  git push origin :refs/tags/[tag-name]

  \#查看tag信息

  git show [tag]

  \#提交指定tag

  git push [remote] [tag]

  \#提交所有tag

  git push [remote] --tags

  \#新建一个分支，指向某个tag

  git checkout -b [branch] [tag]

## 查看信息

  \#显示有变更到文件

  git status

  \#显示当前分支到版本历史

  git log

  \#显示commit提交的历史，以及每次commit发生变更的文件

  git log --stat

  \#搜索提交历史，根据关键词

  git log -S [keyword]

  \#显示某个commit之后的所有变动，每个commit占据一行

  git log [tag] HEAD --pretty=format:%s

  \#显示指定文件相关的每一次diff

  git log -p [file]

  \# 显示过去5次提交

  git log -5 --pretty --oneline

  \#显示所有提交过的用户，按提交次数排序

  git shortlog -sn

  \#显示指定文件是什么人在什么时间修改过

  git blame [file]

  \#显示暂存区和工作区的差异

  git diff

  \#显示暂存区和上一个commit的差异

  git diff --cached [file]

  \#显示工作区与当前分支最新提交commit之间的差异

  git diff HEAD

  \#显示两次提交之间的差异

  git diff [first-branch] [secod-branch]

  \#显示你今天写了多少代码

  git diff --shortstat "@{0 day ago}"

  \#显示某次提交的元数据和内容变化

  git show [commit]

  \#显示某次提交发生的变化的文件

  git show --name-only [commit]

  \#显示当前分支的最近几次提交

  git reflog

## 远程操作

  \#现在远程仓库的所有变动

  git fetch

  \#取回远程仓库的变化，并与本地分支合并

  git pull [remote] [branch]

  \#显示所有远程仓库

  git remote -v

  \#显示某个远程仓库的信息

  git remote show [remote]

  \#增加一个远程仓库，并命名

  git remote add [shortname] [url]

  \#上传本地指定分支到远程仓库

  git push [remote] [branch]

  \#  强行推送当前分支到远程仓库，即使有冲突

  git push [remote] --force

  \#推送所有分支到远程仓库

  git push [remote] --all

  \#删除远程分支或者标签

  git push <retmote>:<branch/tag-name>

  \#上传所有标签

  git push --tags

## 撤销

  \#撤销工作目录中所有未提交文件的修改内容

  git reset --hard HEAD

  \#撤销指定的未提交文件的修改内容

  git checkout HEAD <file>

  \#撤销指定的提交

  git revert <commit>

  \#退回到之前1天到版本

  git log --before="1 days"

  \#恢复暂存区到指定文件到工作区

  git checkout [files]  

  \#恢复某个commit到指定文件到暂存区和工作去

  git checkout [commit] [file]

  \#恢复暂存区到所有文件到工作区

  git checkout .

  \#重置暂存区到指定文件，与上一次commit保持一致，但工作区不变

  git reset [file]

  \#重置暂存区与工作区，与上一次commit保持一致

  git reset --hard

  \#重置当前分支为指定但commit，同时重置暂存区，但工作区不变

  git reset [commit]

  \#重置当前分支为指定但commit，同时重置暂存区和工作区，与指定commit一致

  git reset --hard [commit]

  \#重置当前HEAD为指定commit，但保持暂存区和工作区不变

  git reset --keep [commit]

  \#新建一个commit，用来撤销指定commit，后者但所有变化都将被前者抵消，并且应用到当前分支

  git resvert [commit]

  \#暂时将未提交到变化移除，稍后在移入

  git stash

  git stash pop
