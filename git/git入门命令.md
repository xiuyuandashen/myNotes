## 入门

去oneNote

 

 

 

## 删除文件

`git rm file` 从版本仓库删除至暂存区。

```shell
zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ git rm readme.txt
rm 'readme.txt'

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ git status 
On branch master        
Changes to be committed:   # 在暂存区，还未提交。说明rm 了会从版本仓库退回至暂存区
  (use "git restore --staged <file>..." to unstage) # 告诉你按照这个指令可以反悔
        deleted:    readme.txt


zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ git commit -m 'delete file' # 提交（将之前执行的 rm 给提交了 ）
[master c390b66] delete file 
 1 file changed, 5 deletions(-) # 一个文件更改，5个删除（删除5行吧，应该）
 delete mode 100644 readme.txt  # 删除了一个文件

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ ls  # 已经没有文件了

```

## 误删文件，回滚操作（回退版本）

git log 查看提交日志信息

```bash
zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ git log
commit c390b6669f94a29f56a49cc36c2bffb6b239b53a (HEAD -> master)  # 版本号 我们可以按照这个来回滚
Author: xiuyuandashen <1556450877@qq.com>
Date:   Thu Jul 9 13:15:18 2020 +0800

    delete file

commit 96dea0132ae1ec041a22d5c5993fe45503773ef7
Author: xiuyuandashen <1556450877@qq.com>
Date:   Thu Jul 9 13:04:59 2020 +0800


```

git reset -hard 版本号

```bash
## 找到心仪的版本号执行回滚操作
zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ git reset --hard 96dea0132ae1ec041a22d5c5993fe45503773ef7
HEAD is now at 96dea01 第五次修改文件

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ ls
readme.txt

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ vim readme.txt


```

##  回退版本后，我又想回到之前的版本（回退前的版本）

git reflog 

命令`git reflog`用来记录你的每一次命令

```bash
zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ git reflog
96dea01 (HEAD -> master) HEAD@{0}: reset: moving to 96dea0132ae1ec041a22d5c5993fe45503773ef7
c390b66 HEAD@{1}: commit: delete file 
96dea01 (HEAD -> master) HEAD@{2}: commit: 第五次修改文件
0ce5ae2 HEAD@{3}: commit: 第四次修改
8bc415c HEAD@{4}: reset: moving to 8bc415
217de41 HEAD@{5}: reset: moving to HEAD^
8bc415c HEAD@{6}: commit: 第三次修改
217de41 HEAD@{7}: commit: add 第二次修改该文件
bdf8a9d HEAD@{8}: commit (initial): wrote a readme file

zlf@xiuyuandashen MINGW64 /d/GitProject (master) 
$ git reset --hard c390b66    # 我又回到回退前的版本了
HEAD is now at c390b66 delete file

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ ls  # 文件还是消失了

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$

```

## 未提交之前的回滚

###  1、工作区修改了，未添加至暂存区

该文件的前提是已经被git给接管了！

`git checkout -- file`

 操作如下：

```bash
zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ vim newfile.txt  # 修改文件 

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ git status  # 查看状态
On branch master
Changes not staged for commit:    
  (use "git add <file>..." to update what will be committed)# 文件修改，但还在工作区，未添加至暂存区
  (use "git restore <file>..." to discard changes in working directory)
        modified:   newfile.txt

no changes added to commit (use "git add" and/or "git commit -a")

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ cat newfile.txt  # 查看修改后的文件 
hello world!
10,20,40,80,160
200,500

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ git checkout -- newfile.txt # 回滚，在工作区的文件

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ cat newfile.txt  # 查看文件，发现与回滚前的不同，成功回滚
hello world!
10,20,40,80,160

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$

```



### 2、文件在暂存区内，但未提交

我们修改了文件后，git add  加入到暂存区中，这时我们该如何返回呢。

我们可以先将文件退出暂存区，回到工作区，  `git reset HEAD newfile.txt`

然后按照工作区的回滚文件的方式来回滚。  `git checkout -- newfile.txt`

```bash
zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ vim newfile.txt  # 修改文件 

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   newfile.txt

no changes added to commit (use "git add" and/or "git commit -a")

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ git add newfile.txt  # 添加至暂存区

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ git status # 查看状态
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   newfile.txt


zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ git reset HEAD newfile.txt  # 退回至工作区
Unstaged changes after reset:
M       newfile.txt

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   newfile.txt

no changes added to commit (use "git add" and/or "git commit -a")

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ cat newfile.txt
hello world!
10,20,40,80,160
今天晚上吃什么呢？

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ git checkout -- newfile.txt  # 回滚修改的文件 

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$ cat newfile.txt  
hello world!
10,20,40,80,160

zlf@xiuyuandashen MINGW64 /d/GitProject (master)
$

```

#  git的分支管理

## 1，什么是分支

> 在项目开发过程中，可能会需要开发一些周期比较长并且不急于上线的功能，如果直接在当前主线上进行版本控制，可能会对当前主线的代码造成污染，这个时候，我们可以另开一个分支，从而实现并行开发，在开发完毕之后，再进行合并即可。

## 2，主分支（master分支）

> 从版本的历史记录可以看出来，我们的每次提交，git都会把他们串成一条时间线，这条时间线就是一个分支，目前为止，我们就一条时间线，这个时间线我们叫主分支，即master分支。 HEAD指向的就是当前分支。

## 3，新分支的创建，合并，删除

![img](https://pic3.zhimg.com/80/v2-c3c5746ad03b99c9f4454c1bf8d9899a_720w.jpg)

- 在dev分支上，对文件进行操作，执行add，commit
- 然后再切换回主分支，查看效果

![img](https://pic4.zhimg.com/80/v2-85cc993c6315e9f7c79f4683ecd07ce7_720w.jpg)

删除分支

![img](https://pic2.zhimg.com/80/v2-7b9496a38f0c52326badcabd9a6e8569_720w.png)

## 4，解决分支的冲突

- 有时候，两个分支修改同个地方，那么这个时候，就不能像之前那么顺利，下面我们演示下分支冲突以及解决办法

![img](https://pic4.zhimg.com/80/v2-5557891c5565521bec00dbb0f34b3bbb_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-1136bf4dbf7a2b46bcc9d6ce2446318f_720w.jpg)

- 在命令行模式，通过 Vim dev.txt 解决冲突(手动修改冲突部分)





# 远程仓库

## 1，什么是远程仓库

> 当我们需要多个人协助开发时，必然会遇到一个问题，就是双方的信息如何进行传递同步。 这个时候，就需要搭建一个远程服务器，每个人都从这个“远程服务器”仓库克隆一份到自己的电脑上，并且把自己需要提交推送到服务器仓库里，也可以从服务器仓库中拉取别人的提交。

## 2，GitHub

> 远程服务器可以自己搭建，也可以使用现成的。 著名的“全球最大男性社交网站-GitHub”就是我们可以使用的远程仓库。 GitHub 是最大的 Git 版本库托管商，是成千上万的开发者和项目能够合作进行的中心，我们只需要注册一个账号，就可以免费使用了。 当然，免费的潜在要求是项目必须开源，如果你的项目希望是私密的，那么就需要付费。 但在2018年底，私密的项目也开始免费了。

## 3，创建Github账户

[https://github.com/](https://link.zhihu.com/?target=https%3A//github.com/)
happyCoding2008

![img](https://pic1.zhimg.com/80/v2-710b2133aec4725708d93139303d75f4_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-4237779b55c8dd9ce2d92c9b30791392_720w.jpg)

## 4，github学习教程

[https://guides.github.com/activities/hello-world/](https://link.zhihu.com/?target=https%3A//guides.github.com/activities/hello-world/)

## 5，github帮助中心

[https://help.github.com/en](https://link.zhihu.com/?target=https%3A//help.github.com/en)

## 6，配置SSH

## 6.1 为什么要配置SSH

> 使用SSH协议，您可以连接和验证远程服务器和服务。使用SSH密钥，您可以在每次访问时无需提供用户名或密码即可连接到GitHub。

## 6.2 检查是否存在SSH密钥

$ ls -al ~/.ssh

![img](https://pic4.zhimg.com/80/v2-6dc5cfe5da639d4110b782b3460041a3_720w.png)

## 6.3 生成SSH密钥

ssh-keygen -t rsa -b 4096 -C ["your_email@example.com](https://link.zhihu.com/?target=http%3A//mailto%3A%22your_email%40example.com/)"

![img](https://pic2.zhimg.com/80/v2-cad249c5cb6b0a0c088dcd44d347731d_720w.jpg)



- 观察生成的公钥和私钥，id_rsa是私钥，id_rsa.pub是公钥

## 6.4 将生成的公钥信息配置到github上

注意：目前要直接从setting进入操作！

![img](https://pic1.zhimg.com/80/v2-81a17f0e6dffdbeac50fff12da23cf44_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-7dccd698cbda29083ca633da8ea10a29_720w.jpg)

> 然后，Edit Profile

![img](https://pic4.zhimg.com/80/v2-02a13b1022ab396035aa64f7a384c567_720w.jpg)

将公钥配置上，即可完成

## 7，github上创建仓库

![img](https://pic2.zhimg.com/80/v2-caafe9c00d2ebbf2936fc8bf952bfda5_720w.jpg)

## 8，将本地仓库上传到github上

![img](https://pic2.zhimg.com/80/v2-066a47e05d3dfacc0c53bd0573b15ba9_720w.jpg)



1. github 是给远程仓库取个别名
2. git remote add github [git@github.com](https://link.zhihu.com/?target=http%3A//mailto%3Agit%40github.com/):CoffeeAndLife/git-go.git
3. git remote -v 查看远程仓库信息

![img](https://pic4.zhimg.com/80/v2-d597c06e0f49f5131f3c34ae1ca9a40b_720w.jpg)



$ git push -u github master

![img](https://pic3.zhimg.com/80/v2-1bf732ca78052e80722438400b65aeda_720w.jpg)



## 9，拉取github仓库上的新版本

git pull github

git fetch github master

git merge github/master

## 10，将github上面的项目拷贝到本地

git clone 仓库地址

![img](https://pic3.zhimg.com/80/v2-67d201fb50b006a1c096a03c84b8fcd6_720w.jpg)