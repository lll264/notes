# 一、介绍

[关于git这一篇就够了](https://blog.csdn.net/bjbz_cxy/article/details/116703787?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522171435085416800226573581%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=171435085416800226573581&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-116703787-null-null.142^v100^pc_search_result_base2&utm_term=git&spm=1018.2226.3001.4187)

Git是一个免费的、开源的分布式版本控制系统，可以快速高效地处理从小型到大型的项目。

## 结构

**工作区—git add—>暂存区—git commit—>本地库**

## 初始化本地仓库

在Git中命令跟Linux是一样的。

1.创建文件夹 repo02

2。设置用户名和邮箱

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

## ssH配置

ssh方式好处：不用每次都进行身份验证

1.cd ~进入用户主目录

2.执行命令生成.ssh目录

`ssh-keygen -t rsa -C 9772555673@qq.com`

在.ssh目录下生成了id_rsa和id_rsa.pub两个文件

3.打开id_rsa.pub文件，复制其中的内容

4.复制到github中

# 二、常用命令

## 1.add和commit

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

## 2.git status

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

## 3.log

展示提交日志

`git log`

`git log --oneline`: 显示简洁信息

`git reflog`: 多了信息HEAD@{数字},这个数字的含义是指针回到当前这个历史版本需要走多少步

## 4.reset

前进或后退历史版本

- `git reset --hard [索引]`
  不保存工作区和暂存区
- `git reset --mixed [索引]`
  保存工作区
- `git reset --soft [索引]`
  保存工作区和暂存区

## 5.rm 删除文件

`rm test.txt` 删除工作区的文件

`git add test.txt` 将删除同步到暂存区

## 6.diff 

`git diff test.txt` 将工作区中的文件和暂存区中文件进行比较 

# 三、分支

在版本控制过程中，使用多条线同时推进多个任务。这里面说的多条线，就是多个分支。

同时多个分支可以并行开发，互相不耽误，互相不影响，提高开发效率如果有一个分支功能开发失败，直接删除这个分支就可以了，不会对其他分支产生任何影响。

## 1.查看当前分支

`git branch -v`

```bash
lll@DESKTOP-7T6S7TC MINGW64 /d/learn-git/repo03 (main)
$ git branch -v
branch01 ade750b insert aaa
*main     ade750b insert aaa
```

当前在的分支带*号

## 2.创建分支

`git branch [分支名]`

```bash
lll@DESKTOP-7T6S7TC MINGW64 /d/learn-git/repo03 (main)
$ git branch branch01
```

## 3.切换分支

`git checkout [分支名]`

```bash
lll@DESKTOP-7T6S7TC MINGW64 /d/learn-git/repo03 (main)
$ git checkout branch01
Switched to branch 'branch01'
```

## 4.合并分支

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

# 四、本地库和远程库操作

## 1.初始化本地库

创建一个repo01，cd当仓库当前位置。

使用git init 命令初始化

创建test.txt文件并insert 111，暂存，提交

## 2.创建远程仓库

在github上创建远程仓库repo01

## 3.本地仓库推送

### 将本地仓库关联到远程仓库

- 点击仓库，获取远程仓库地址，远程仓库地址较长，在关联该仓库时可以取别名
  `git remote add repo01 https://github.com/lll8c/repo01` 
- `git remote -v` 查看关联的仓库
- `git remote remove [仓库名]` 删除关联

### 本地仓库推送

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

### 加入团队

某个仓库不会允许所有人进行push，在github远程仓库中可以邀请别人加入团队允许push

## 4.克隆仓库

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

## 5.远程库拉取

### fetch操作

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

### pull操作

远程库的拉取可以直接利用pull命令来完成

`git pull [远程仓库名] [远程分支名]` ：省略仓库名和分支后默认为origin main

**强制合并**：git pull origin main --allow-unrelated-histories

# 五、golang使用git

## 配置git目录

setting-Version Control-Git

## add和commit

快捷键ctrl+Alt+A 和 ctrl+K

<img src="C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240430181620215.png" alt="image-20240430181620215" style="zoom:67%;" />

当更改内容后，跟本地库不一致的地方会显示绿色

<img src="C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240430182229983.png" alt="image-20240430182229983" style="zoom: 80%;" />

## push 和 pull

![image-20240430184951227](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240430184951227.png)

设置远程库的地址

![image-20240430185909009](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240430185909009.png)

## 从远程库中进行克隆

点击Get from VCS

![image-20240430190639645](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240430190639645.png)

输入远程仓库url和本地存储的地址即可完成克隆

![image-20240430191219236](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20240430191219236.png)

## 解决冲突



# 六、忽略文件

创建一个.gitignore文件，将要忽略的文件添加到其中即可

**常用命令：**

`git check-ignore -v <file>`（用于检查指定文件是否被Git忽略，并显示忽略规则的详细信息）

**注意：**

要让.gitignore文件忽略生效，不能是已经git add添加到暂存区中的文件。否则只能将其从暂存区中删除 `git rm --cached [文件名]`。

