---
title: Git常用操作笔记
date: 2020-12-3 12:02:00
tags: Git
categories: Git
---

# 基础操作

## 拉取和同步

    git clone http://xxx.xxx.git //http方式, 从远程clone仓库
    git pull //拉取远程分支
    git branch //查看本地
    git branch -a //查看远程和本地
    git checkout xxxbranch //本地切到某分支
    git checkout xxx/xxx //仅拉取部分目录或文件

## 推送到远程

    git add -A //推送所有修改到本地仓库
    git commit -m "change logs" //提交到本地仓库（记录修改信息）
    git push //推送本地分支到远程的同名分支，需要先关联
    git push origin <本地分支名> //推送本地分支到远程同名分支
    git push origin <本地分支名>:<远程分支名> //推送本地分支到远程指定分支

## 加tag/删tag

    git tag -a TAGNAME -m "TAG LOG" //加tag
    git push origin TAGNAME //推送tag到远程
    git tag -d TAGNAME //删除本地tag
    git push origin :refs/tags/TAGNAME //删除远程tag

## 创建/删除/修改分支

创建分支并关联远程

    git checkout -b BRANCH_NAME //本地创建分支
    git push origin BRANCH_NAME //推送到远程
    git push --set-upstream origin BRANCH_NAME //关联远程，便于以后分支pull/push

删除本地分支    

    git branch -d branch_name
    git branch -D branch_name //强制删除

删除远程分支

    git push origin -d branch_name

分支重命名(本地)

    git branch -m OLD_NAME NEW_NAME

## 版本比较

    git diff COMMIT_ID //比较本地和某commit_id的内容
    git diff ID1 ID2 //比较两个提交的内容，比较新增时，旧版本在前，新版本在后
    git diff --name-only ID1 ID2 //只显示文件名

## 版本回退

    git reset --hard HEAD^ //回退到上个版本
    git reset --hard HEAD^^ //回退到上上个版本
    git reset --hard COMMIT_ID //回退到指定提交
    git push -f //强制提交，覆盖远程，使远程也回退
    git push origin master -f //强制推送到远程的master分支

## 合并分支
两个分支A和B，要把分支B的所有提交合并到A分支上

    git checkout <branch A> //切到待合并分支A
    git merge <branch B> //拉取分支B，合并到当前分支A
    git merge <branch B>  --squash //合并分支，将B的多个提交融合成一个再合并到A，而不是B的所有提交记录都照搬到A（这个更常用）
    git merge --abort //终止合并

如果有`merge conflict`,手动修改冲突文件->保存文件->`git add -A`提交修改->`git commit -m "xxx"`提交该合并

如果本地仓库已经处于待merge状态，又想取消merge,同步成远程仓库状态，只需要reset本地仓库到当前commit-id

    git reset --hard HEAD

也可以reset到指定commit-id:

```
git reflog && git reset --hard commit-id
```

## 合并提交

### 合并当前提交

如果当前修改还未提交, 想合并到最近的一次提交里，例如最近提交有个错误，可以用`--amend`修订提交

    git add -A
    git commit --amend
    git push -f //amend后通常强制推送，因为没有新增commit

### 合并历史提交

有时同一个功能分多次提交，提交过于频繁，需要合并成一个提交。
如下有三次提交

    $git log
    commit_3: xxxxx
        message_3 ....
    commit_2: xxxxx
        message_2 ....
    commit_1: xxxxx
        message_1 ....

现在想把commit_3 和 commit_2合并成一个commit.

    git rebase -i commit_1 //重定位到要合并的前一个提交

进入commit信息编辑模式：

    pick commit_2 message_2...
    pick commit_3 message_3...

将要合并的commit_3前的属性`pick`（选用）改为`squash`（压扁），`wq`保存，进入当前合并commit的信息提交界面，再次`wq`保存, 查看合并后提交记录如下：

    $git log
    commit_4: xxxxx
        message_3 ....
        message_2 ....
    commit_1: xxxxx
        message_1 ....

两次提交已合成一次（新的）提交



# 多人提交的冲突解决办法

A和B同时开发某项目的同一个分支，A拉取最新版本1.0后，在本地新增功能，此时B也在1.0上修改并提交到了新版本1.1到远程仓库。A在B提交之后再提交，发现自己本地的修改已是旧版本，无法直接提交，如下图是A的add,commit,push三连的结果

![1631249531971_115](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051005366.png)

![image-20221205100655798](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051006842.png)

![image-20221205100726224](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051007268.png)

### 手动解决conflict

`git pull` 拉取远程仓库最新版本，此时有两种情况

 - 代码有冲突，需手动修改冲突区域的代码块，二选一，然后重新add-commit-push三连提交
 - 无冲突，pull代码会自动合并，直接重新三连提交即可

以下是有冲突的情况
![image-20221205100836563](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051008610.png)

找到冲突源码，冲突的符号定义如下：

 - `<<<<< HEAD`：当前本地的代码块
 - `======`：分割冲突块
 - `>>>>>>b699a7fc`：远程最新hash版本号的代码块

![image-20221205100855921](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051008970.png)

修改方法：先拷贝冲突关键语句，再删除所有冲突域符号，最后只保留如下代码
![image-20221205101900769](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051019814.png)

修改完后，`git add, git commit, git push`，成功提交
![image-20221205102021265](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051020311.png)

查看提交后版本：`git log`

### 修改某次提交的commit信息
有时需要修改commit信息便于区分哪个是解决冲突后的提交
解决方案：

 - 修改最新的commit，只需要amend修改commit信息后，再push
 - 修改历史的commit，需要先rebase修改属性为edit后，再commit --amend

下面讲修改历史commit
如下图，想修改9877的commit信息
![image-20221205102348131](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051023197.png)

先rebase到之前的commit
![image-20221205102432433](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051024479.png)
显示其后的版本属性如下
![image-20221205102447476](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051024531.png)
修改9877的属性为edit(待编辑模式)，将原始commit改成如下内容,`:wq`保存:

![image-20221205102658821](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051026868.png)

然后`commit --amend, rebase --continue`
![image-20221205102751155](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051027201.png)
再查看下git og修改成功
最后`git push`同步到远程仓库



# 从另一个分支拉取指定的几个commit内容

A和B都在git的master分支提交代码，一天发现master某个版本有问题，回退n各版本都找不到是谁提交引起的问题，由于master还要作稳定测试等其他用途，决定先回退master分支到较早的指定版本，而master最新版和稳定版之间提交的内容，分别由各自A和B“认领”，拉取master上自己提交的功能到自己的分支，debug好以后在合并回master。
需求：
如何在开发者分支上拉取master分支的指定几个commit的内容，注意不是某个commit以前的内容，是commit内的内容？

## 创建自己分支，回退master
首先切到master分支上，创建一个自己的分支thomas，自己分支是master的拷贝

    git checkout master //当前在那个分支，决定创建分支的内容
    git checkout -b thomas //做两件事：在本地创建thomas分支，内容和master一样；切到thomas分支
    git push --set-upstream origin thomas //推送分支到远程，这步很容易漏掉
    git branch //查看当前在哪个分支
    git branch -a //查看所有分支

以上操作完成后，自己分支就创建好了，注意动作只影响到本地仓库的.git文件，要同步远程仓库还要push到远程
下面备份master, 再回退master

    git checkout master
    git checkout -b master_backup //先备份master,上面有自己分支要拉取的内容
    git checkout master //切到master,准备回退
    git reset --hard COMMIT_ID //回退到稳定版本commit_id
    git push -f //由于是回退，提交比远程的还早，一般需要强制提交，这个操作也会把本地的.git修改一同提交到远程

这样就有三个分支：

    master: 包含稳定版本的旧代码
    master_backup: master的备份，包含稳定版和之后的A、B的一些提交
    thomas: 开发者A的个人分支，现在和master稳定版完全一样

下面只需要从master_backup拉取自己相关的提交到thomas分支即可。

## cherry-pick拉取指定commit
先把要拉取的commit id存起来：

    git checkout master_backup
    git log > ../master_backup.log

截取commit log片段如图
![image-20221205103857374](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051038438.png)

切到thomas分支，拉取master_backup的commit

    git checkout thomas
    git cherry-pick 3d6b3be
这种方法只拉了一个commit, 更好的方式是按功能，一次拉多个commit,甚至一次把所有的commit都拉完。
cherry-pick支持多个pick一步到位
例如git log如下

    commit4 id4
    commit3 id3
    commit2 id2
    commit1 id1

离散拉取：只拉取id1和id4：

    git cherry-pick id1 id4

！注意，提交顺序很重要，旧版本写在前新版本写在后
如果是区间拉取,即全部的id1，id2, id3，id4

    git cherry-pick id1..id4 //加两个点即为区间拉取

为了验证是不是真的拉取了多个版本，可以`git diff --name-only id1 id4`看下拉取后的修改哪些文件，对比被拉取分支的修改，如果一致，说明确实拉取多个commit
对于上图的commit，建议按功能多次cherry-pick并commit+push，便于后续debug。

## cherry-pick的冲突问题
cherry-pick也是合并，只要是合并代码，就可能有冲突
![image-20221205103926181](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051039238.png)
合并单个commit,使用使用常规的冲突解决办法即可：

 - 到源码改冲突， `<<<< ===== >>>>`三个标记之间代码块二选一
 - `git status`查看哪些待提交
 - `git add -A`提交修改后的源码到本地.git

### 单个提交的冲突解决
由于是从其他分支的commit id合并到当前分支（HEAD）,可以不加考虑的删掉`<<<<HEAD`和`====`之间的内容，采用`====`和`commit_id`之间的内容，随后删掉三个标记即可。
![image-20221205103951579](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051039681.png)
有可能出现冲突代码块有重叠区的情况

    <<<< HEAD
    code 1
    =====
    <<<< commit_id 1
    code 2
    >>>> commit_id 2
    code 3
    =====
    code 4
    >>>> commit_id 3

只要确定一个原则：<<<<是冲突块的起始点，====是分界，>>>>是终止点，分两步删代码就可以了。

### 多个提交的冲突解决：
如果是cherry-pick多个commit，冲突的解决方法就不一样了。
其区别在于，多个commit_id的cherry-pick，一旦遇到冲突，就会停下pick,需要手动解决冲突后，用`cherry-pick --continue`继续接下来的commit合并，直到由遇到冲突，再次手动解决。也就是说冲突会阻塞多个commit的cherry-pick，它不会一次性合并所有commit,让你一次性解决冲突。具体流程如下：

 - `cherry-pick id1 id2 id3 id4 .... idn`
 - 冲突报错，到源码手动解决
 - `git add -A` 添加解决冲突后的文件到.git
 - `cherry-pick --continue` 继续后面的合并,cherry-pick成功会自动提交commit信息
 - 再遇到冲突，再次解决....
 - 所有id1 ... idn全部pick完成

批量cherry-pick每次成功后都会有一次commit信息，有时候会报错，需要手动commit之后再continue

### 特殊的冲突情况
提示有一个commit是合并的提交，即这个提交是两个分支的交汇，cherry-pick不知道以哪个分支为准
![image-20221205104005491](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051040545.png)
![image-20221205104017399](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051040453.png)

如何解决：cherry-pick添加-m 1选项

    For example, if your commit tree is like below:
    
    - A - D - E - F -   master
       \     /
        B - C           branch one
    then git cherry-pick E will produce the issue you faced.
    
    git cherry-pick E -m 1 means using D-E, while git cherry-pick E -m 2 means using B-C-E

例如选择cherry-pick commid_id -m 1, 结果如下，可手动解决冲突了
![image-20221205104053730](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051040782.png)
注意有merge的commit,会包含其他人的更新，如果只是pick自己的代码，不需要pick带merge的commit.

# 跨仓库合并代码

假设某公司windows driver主线仓库为storport, 为了某产品定制的driver仓库为gg8, 现在gg8的所有feature已充分测试，准备合并到主线仓库storport, 这两个仓库的代码差异非常大，维护者众多，如何处理？

首先划分代码各部分归谁负责：
每个人用git，找出其在gg8仓库的个人修改，用winmerge手动合并到主线仓库storport
那么具体如何高效，可靠的合并：

git部分：
用git只找差异部分，具体操作：

    git diff commit_a commit_b //找所有文件+代码差异
    git diff commit_a commit_b --stat //只显示有差异的文件名，这个信息对应winmerge手动合并很重要
    git diff commit_a commit_b 指定文件路径 //只显示指定文件的内容差异，这个信息对应winmerge手动合并很重要

winmerge部分：
winmerge可以比较两个仓库所有差异，但是有些差异可能不需要合并，例如换行，修改时间等。总之winmerge的差异有很多“误报”
如果只一个个打开有差异的文件去比效率太低，需要借助git定位到哪些该开发者负责的文件有改变，以及文件内哪些代码是该开发者改变的。

找出某开发者A的提交改了哪些文件：
![image-20221205104623191](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051046241.png)

找出具体代码：
![](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051046320.png)

winmerge直接合并：
只是一句打印差异，但是如果不用git先定位，要从左侧差异栏找出此代码，相当困难
![image-20221205104642794](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212051046886.png)

这样，开发者A在代码合并过程中，完全不受其他开发者B, C的差异代码干扰

# 强制覆盖本地代码

git本地代码有时checkout到旧版本代码，想回到最新版本时，直接pull无法成功，且强制pull也不行。有以下两种方式解决：

## 重新克隆

最简单是直接删掉本地项目，再重新`git clone`

## fetch覆盖

    git fetch --all //拉取远程repo所有branch到本地，但不合并到本地repo
    git reset --hard origin/master //本地repo强制同步远程repo的master分支
    git pull -f //强制拉取远程repo最新代码

注意，如果本地旧版本代码有xxx.c，而远程最新代码没这个文件，本地需要手动删掉这个文件。因为以上操作不会删除本地文件，只会拉取本地没有的，或者覆盖不同的文件到本地。为了确保旧版本多出的文件删除，直接删除目录下除了`.git`以外的所有项目文件，再`fetch,reset,pull`

# 将本地未初始化git的项目上传到远程已初始化的git仓库

有一些项目代码是基于开源的庞大项目基础上开发，例如UEFI EDK2, Linux kernel.

项目开发时，可能基于不同的开源项目版本，例如：

远程git仓库是EDK2版本A0 + 自定义功能B0；本地的新功能是基于EDK2版本A1 + 自定义功能B1，且本地项目还没有初始化git。这种情况如何将本地项目直接上传到远程已有的项目上面去？

1.首先在本地建立git仓库

在本地新项目目录初始化git仓库：

```
git init
git add .
git commit -m "commit信息"
```

2.将本地git仓库关联到远程已有的git仓库

```
git remote add origin http://远程仓库地址.git
```

3.拉取远程仓库到本地

注意`--allow-unrelated-histories`是忽略本地项目和远程项目没有历史关联的关键参数，否则不能pull成功

```
git pull origin master --allow-unrelated-histories
```

合并代码通常会有冲突，手动解决冲突后再`git add, git commit -m "fix merge conflict"`

4.最后推送本地仓库到远程

```
git push -u origin master
```



# 链接外部repo作为子模块

在github的项目仓库中，通常看到如下有@符号的外部仓库链接，点进去可能打开其他的项目仓库。这种外部仓库相当于当前项目仓库的子模块。

类似于Linux的软链接，子模块方式可以链接到其他项目仓库，并自动同步其他仓库最新的代码。

![image-20221209110659056](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212091106114.png)

1.如何创建外部repo的链接:

```
git submodule add "外部repo地址.git" 外部repo文件夹名
```

本地就clone了外部仓库到外部repo文件夹名中, 提交本项目和正常的提交流程相同

2.如何clone带外部repo的项目：

git clone 的时候需要加上`--recursive`，否则外部repo文件夹是空文件夹

```
git clone --recursive "项目地址.git"
```

如果已经忘记加`--recursive`，可以手动初始化子模块

```
git submodule update --init --recursive
```
