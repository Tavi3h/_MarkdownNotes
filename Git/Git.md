[TOC]

# Git常用命令及使用

>
摘自[廖雪峰的Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000){:target="_blank"}。

## 安装Git及配置

Git[下载地址](https://git-scm.com/downloads){:target="_blank"}。

安装选项默认即可。

安装完毕后，打开Git Bash对user.name和user.email进行配置：

    git config --global user.name "Your Name"
    git config --global user.email "email@example.com"

注意`git config`命令的`--global`参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

## 创建版本库

    cd E:
    cd myCode/
    mkdir learnGit
    cd learnGit/
    git init

此时会在E:\myCode\learnGit创建空的仓库，该目录下会出现一个.git隐藏目录。

**添加文件到版本库：**

在仓库目录（learnGit）或其子目录中创建新文件readme.txt。

写入内容：

    Git is a version control system.
    Git is free software.

使用命令`git add`将文件添加到仓库：

    git add readme.txt

使用命令`git commit`将文件提交到仓库：

    git commit -m "create a readme file"

`-m`参数用于描述本次提交的说明。

返回信息：

    $ git commit -m "create a readme file"
    [master (root-commit) d86f0cf] create a readme file
     1 file changed, 2 insertions(+)
     create mode 100644 readme.txt

可以添加多个文件，然后进行一次提交。

## 版本管理

### 修改、查看改动及再提交

修改readme.txt的内容：

    Git is a distributed version control system.
    Git is free software.

然后使用`git status`命令查看结果：

    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   readme.txt

    no changes added to commit (use "git add" and/or "git commit -a")

`git status`命令可以让我们时刻掌握仓库当前的状态。上述输出告诉我们readme.txt被修改了但还没有提交。

使用`git diff`命令可以查看具体修改的内容：

    $ git diff
    diff --git a/readme.txt b/readme.txt
    index d8036c1..013b5bc 100644
    --- a/readme.txt
    +++ b/readme.txt
    @@ -1,2 +1,2 @@
    -Git is a version control system.
    +Git is a distributed version control system.
     Git is free software.
    \ No newline at end of file

此时进行提交和提交新文件一样，先`git add readme.txt`再`git commit`：

在执行`git add`后，再执行`git status`：

    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            modified:   readme.txt

此时可以得知将要被提交的修改包括readme.txt。

执行`git commit`，提交：

    $ git commit -m "add distributed"
    [master 72cd03d] add distributed
     1 file changed, 1 insertion(+), 1 deletion(-)

提交后再次查看状态：

    $ git status
    On branch master
    nothing to commit, working tree clean

上述输出告诉我们此时没有需要提交的修改。

再次将readme.txt修改为：

    Git is a distributed version control system.
    Git is free software distributed under the GPL.

再次提交：

    git add readme.txt
    git commit -m "append GPL"
    [master 02e134c] append GPL
     1 file changed, 1 insertion(+), 1 deletion(-)

>
像这样，你不断对文件进行修改，然后不断提交修改到版本库里，就好比玩RPG游戏时，每通过一关就会自动把游戏状态存盘，如果某一关没过去，你还可以选择读取前一关的状态。有些时候，在打Boss之前，你会手动存盘，以便万一打Boss失败了，可以从最近的地方重新开始。Git也是一样，每当你觉得文件修改到一定程度的时候，就可以“保存一个快照”，这个快照在Git中被称为`commit`。一旦你把文件改乱了，或者误删了文件，还可以从最近的一个`commit`恢复，然后继续工作，而不是把几个月的工作成果全部丢失。

### 查看版本及回退

使用`git log`命令查看历史纪录：

    $ git log
    commit 02e134cface9abfa55dc7b28f35e58a96e1e1b56 (HEAD -> master)
    Author: Tavish <bit_zjm@hotmail.com>
    Date:   Wed Jun 6 22:13:15 2018 +0800

        append GPL

    commit 72cd03d5a5bd1de3c97a581b371ca796299bc79d
    Author: Tavish <bit_zjm@hotmail.com>
    Date:   Wed Jun 6 22:04:07 2018 +0800

        add distributed

    commit d86f0cf1fccbafe51f0fdfd4691e0c66834273ba
    Author: Tavish <bit_zjm@hotmail.com>
    Date:   Wed Jun 6 21:51:34 2018 +0800

        create a readme file

`git log`命令显示从最近到最远的提交日志，我们可以看到3次提交，最近的一次是`append GPL`，上一次是`add distributed`，最早的一次是`create a readme file`。
log信息分别给出了每次提交的版本号、作者、日期和提交描述。其中HEAD指向当前版本。

可以使用参数`--pretty=oneline`精简输出信息：

    $ git log --pretty=oneline
    02e134cface9abfa55dc7b28f35e58a96e1e1b56 (HEAD -> master) append GPL
    72cd03d5a5bd1de3c97a581b371ca796299bc79d add distributed
    d86f0cf1fccbafe51f0fdfd4691e0c66834273ba create a readme file

现在我们要回退readme.txt到`add distributed`这个版本，可以使用`git reset`命令：

这里由于当前版本是`HEAD`，它的上一个版本就是`head^`，再上一个版本是`head^^`...依次类推。当然前100个版本之类的可以写成`head~100`。

    $ git reset --hard HEAD^
    HEAD is now at 72cd03d add distributed

现在查看readme.txt的内容，发现已经回退到`add distributed`：

    $ cat readme.txt
    Git is a distributed version control system.
    Git is free software.

此时查看当前版本库的状态：

    $ git log
    commit 72cd03d5a5bd1de3c97a581b371ca796299bc79d (HEAD -> master)
    Author: Tavish <bit_zjm@hotmail.com>
    Date:   Wed Jun 6 22:04:07 2018 +0800

        add distributed

    commit d86f0cf1fccbafe51f0fdfd4691e0c66834273ba
    Author: Tavish <bit_zjm@hotmail.com>
    Date:   Wed Jun 6 21:51:34 2018 +0800

        create a readme file

此时输出的信息里已经不包含`append GPL`这个版本了。

但此时我们要再回到`append GPL`这个版本怎么办？

如果此时命令行还没有关闭，可以向上寻找`append GPL`对应的版本号（02e134cface9abfa55dc7b28f35e58a96e1e1b56 ），然后同样使用`git reset`命令：

    $ git reset --hard 02e13
    HEAD is now at 02e134c append GPL

>
版本号没必要写全，前几位就可以了，Git会自动去找。当然也不能只写前一两位，因为Git可能会找到多个版本号，就无法确定是哪一个了。
Git的版本回退速度非常快，因为Git在内部有个指向当前版本的HEAD指针，当你回退版本的时候，Git仅仅是把`HEAD`从指向`append GPL`改为`add distributed`，之后对工作区的文件进行更新。
所以你让HEAD指向哪个版本号，你就把当前版本定位在哪。

但是如果在回退到某个版本后关闭了命令行，此时想要恢复到新版本怎么办？

>
在Git中，总是有后悔药可以吃的。当你用`git reset --hard HEAD^`回退到`add distributed`版本时，再想恢复到`append GPL`，就必须找到`append GPL`的`commit id`。Git提供了一个命令git reflog用来记录你的每一次命令：

    $ git reflog
    02e134c (HEAD -> master) HEAD@{0}: reset: moving to 02e13
    72cd03d HEAD@{1}: reset: moving to HEAD^
    02e134c (HEAD -> master) HEAD@{2}: commit: append GPL
    72cd03d HEAD@{3}: commit: add distributed
    d86f0cf HEAD@{4}: commit (initial): create a readme file

这里可以找到`append GPL`的版本号。

总结来说：

- HEAD指向当前版本，使用`git reset --hard commit_id`可以在版本之间转换。
- 回退时需要使用`git log`查看提交历史，以便确定要回退到哪个版本。
- 恢复时需要使用`git reflog`查看命令历史，以便确定要恢复到哪个版本。

### 工作区和暂存区

- 工作区：指.git文件夹所在的目录，例如learnGit文件夹就是一个工作区。
- 版本库：.git文件夹就是Git的版本库。

>
Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

当使用`git add`命令向版本库中添加文件时，实际上就是将文件添加到暂存区。当使用`git commit`命令提交时，实际上就是把暂存区的所有内容提交到当前分支。

继续修改readme.txt：

    Git is a distributed version control system.
    Git is free software distributed under the GPL.
    Git has a mutable index called stage.

并在工作区新增一个LICENSE文件：

    All Right Reserved.

查看状态：

    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   readme.txt

    Untracked files:
      (use "git add <file>..." to include in what will be committed)

            LICENSE

    no changes added to commit (use "git add" and/or "git commit -a")

上述信息告诉我们readme.txt被修改，而LICENSE还没有被添加过，所以状态是`UntracKed`。

将readme.txt和LICENSE添加到暂存区：

    git add readme.txt
    git add LICENSE

查看状态：

    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            new file:   LICENSE
            modified:   readme.txt

提交：

    $ git commit -m "understand how stage works"
    [master 11a8d7e] understand how stage works
     2 files changed, 2 insertions(+)
     create mode 100644 LICENSE

提交后，暂存区就没有任何内容了。

### 管理修改

>
下面，我们要讨论的就是，为什么Git比其他版本控制系统设计得优秀，因为Git跟踪并管理的是修改，而非文件。
你会问，什么是修改？比如你新增了一行，这就是一个修改，删除了一行，也是一个修改，更改了某些字符，也是一个修改，删了一些又加了一些，也是一个修改，甚至创建一个新文件，也算一个修改。

下面验证Git管理的是修改还是文件：

首先对readme.txt做出修改：

    Git is a distributed version control system.
    Git is free software distributed under the GPL.
    Git has a mutable index called stage.
    Git tracks changes.

然后添加并查看状态：

    $ git add readme.txt
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            modified:   readme.txt

提交之前再次修改readme.txt：

    Git is a distributed version control system.
    Git is free software distributed under the GPL.
    Git has a mutable index called stage.
    Git tracks changes of files.

然后提交：

$ git commit -m "git tracks changes"
[master 43de9b6] git tracks changes
 1 file changed, 2 insertions(+), 1 deletion(-)

查看状态：

    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   readme.txt

    no changes added to commit (use "git add" and/or "git commit -a")

此时会发现有一次修改没有被提交。
使用`git diff HEAD -- readme.txt`命令查看工作区和版本库里面最新版本的区别：

    $ git diff HEAD -- readme.txt
    diff --git a/readme.txt b/readme.txt
    index db28b2c..9a8b341 100644
    --- a/readme.txt
    +++ b/readme.txt
    @@ -1,4 +1,4 @@
     Git is a distributed version control system.
     Git is free software distributed under the GPL.
     Git has a mutable index called stage.
    -Git tracks changes.
    \ No newline at end of file
    +Git tracks changes of files.
    \ No newline at end of file


可以发现第二次修改确实没有被提交。

这是因为我们的操作过程是这样的：

    第一次修改 --> git add --> 第二次修改 --> git commit

如果想要提交第二次修改可以这样：

    第一次修改 --> git add --> git commit --> 第二次修改 --> git add --> git commit

或者：
    
       第一次修改 --> git add --> 第二次修改 --> git add --> git commit

总的来说，如果不使用`git add`将工作区的文件存入暂存区，那么commit就不会进行提交。

**git diff命令**：

- 当暂存区中没有文件时，git diff比较的是，工作区中的文件与上次提交到版本库中的文件。
- 当暂存区中有文件时，git diff则比较的是，当前工作区中的文件与暂存区中的文件。
- git diff HEAD -- file，比较的是工作区中的文件与版本库中文件的差异。HEAD指向的是版本库中的当前版本，而file指的是当前工作区中的文件。

### 撤销修改

首先对readme.txt进行修改：

    Git is a distributed version control system.
    Git is free software distributed under the GPL.
    Git has a mutable index called stage.
    Git tracks changes.
    My stupid boss still prefers SVN.

然后查看git状态：

    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   readme.txt

    no changes added to commit (use "git add" and/or "git commit -a")

我们可以发现git告诉我们使用`git checkout -- <file>`可以丢弃工作区的修改。

这个命令`git checkout -- readme.txt`的意思是将readme.txt文件在**工作区**的修改全部撤销，这里面有两种情况：

- 一种是`readme.txt`自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
- 一种是`readme.txt`已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

这里在执行`git checkout -- readme.txt`命令后查看文件内容：

    $ cat readme.txt
    Git is a distributed version control system.
    Git is free software distributed under the GPL.
    Git has a mutable index called stage.
    Git tracks changes.

文件内容被复原。

另一种情形，假设现在我们把修改后的文件添加到了暂存区（`git add`）：

    Git is a distributed version control system.
    Git is free software distributed under the GPL.
    Git has a mutable index called stage.
    Git tracks changes.
    My stupid boss still prefers SVN.

查看状态：

    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            modified:   readme.txt

可以发现Git同样告诉我们使用命令`git reset HEAD <file>`可以把暂存区的修改撤销（unstaged），重新放回工作区：

    $ git reset HEAD readme.txt
    Unstaged changes after reset:
    M       readme.txt

再次查看状态：

    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   readme.txt

    no changes added to commit (use "git add" and/or "git commit -a")

可以发现当前暂存区是干净的，工作区仍有修改。

现在我们再撤销工作区的修改就可以了：

    $ git checkout -- readme.txt

最后查看状态：

    $ git status
    On branch master
    nothing to commit, working tree clean

查看文件：

    $ cat readme.txt
    Git is a distributed version control system.
    Git is free software distributed under the GPL.
    Git has a mutable index called stage.
    Git tracks changes.

一切均已恢复正常。

但是如果我已经提交了（`git commit`）错误的修改到版本库怎么办？这时只要使用回退版本回退到上一个正确版本就可以了。

### 删除文件

在Git中删除是一个修改操作。

先添加一个新文件test.txt到Git并且提交：

    $ touch test.txt

    $ git status
    On branch master
    Untracked files:
      (use "git add <file>..." to include in what will be committed)

            test.txt

    nothing added to commit but untracked files present (use "git add" to track)

    $ git add test.txt
    $ git commit -m "add test.txt"
    [master bdf1c32] add test.txt
     1 file changed, 0 insertions(+), 0 deletions(-)
     create mode 100644 test.txt

提交成功后，在文件管理器中删除这个文件，或使用`rm`命令。删除后，此时工作区和版本库就不一致了，查看状态：

    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add/rm <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            deleted:    test.txt

    no changes added to commit (use "git add" and/or "git commit -a")

Git会告诉我们哪些文件被删除了。现在我们有两个选择：

1. 确实要从版本库中删除该文件，此时使用命令`git rm`，并且`git commit`，确实地删除版本库内的该文件。
2. 误删除，此时使用命令`git checkout -- test.txt`，可以将误删的文件恢复到最新版本。

确实地从版本库中删除该文件：

    $ git rm test.txt
    rm 'test.txt'

    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            deleted:    test.txt

    $ git commit -m "remove test.txt"
    [master ba5e49d] remove test.txt
     1 file changed, 0 insertions(+), 0 deletions(-)
     delete mode 100644 test.txt

误删除，从版本库中将文件恢复：

    $ git checkout -- test.txt

执行后，工作区将出现test.txt在版本库中的最新版。

## 远程仓库

使用GitHub作为Git仓库托管。

由于本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以首先需要先生成SSH Key。

在用户主目录（这里是C:\Users\Tavish）下查找是否存在.ssh目录，如果有，再查看该目录下是否有**id_rsa**和**id_rsa.pub**这两个文件，如果没有.ssh目录或这两个文件则说明没有生成过SSH Key。那么首先我们要先生成SSH Key。

在Git Bash下，创建SSH Key：

    ssh-keygen -t rsa -C "youremail@example.com"

邮件地址替换为自己的地址，然后一路回车。

最终在主目录的.ssh目录下就会生成**id_rsa**和**id_rsa.pub**这两个文件。

- **id_rsa**：私钥
- **id_rsa.pub**：公钥

登录GitHub，打开Setting的SSH and GPG Keys页面，选择New SSH key。

填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容，Add SSH key。

>
为什么GitHub需要SSH Key呢？因为GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，而Git支持SSH协议，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。
当然，GitHub允许你添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。
最后友情提示，在GitHub上免费托管的Git仓库，任何人都可以看到喔（但只有你自己才能改）。所以，不要把敏感信息放进去。

### 添加远程库

现在假设我们已经有了一个本地的Git仓库，我们又想在GitHub创建一个Git仓库，并且使这两个仓库进行远程同步。这样，GitHub上的仓库既可以作为备份，又可以让其他人通过该仓库来协作，真是一举多得。

首先在Github上创建一个仓库，名字同样为learnGit。

![create_a_new_repository](images\create_a_new_repository.PNG)

目前，在GitHub上的这个learngit仓库还是空的，GitHub告诉我们，可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库：

![ssh_link_repository](images\ssh_link_repository.PNG)

根据GitHub的提示，在本地的learnGit仓库下运行命令：（输错了地址并执行了，可以使用`git remote rm origin`命令删除然后重新关联）

    $ git remote add origin git@github.com:Tavi3h/learnGit.git

添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库。

接下来将本地库的所有内容推送到远程库上：

    $ git push -u origin master
    Counting objects: 21, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (16/16), done.
    Writing objects: 100% (21/21), 1.71 KiB | 877.00 KiB/s, done.
    Total 21 (delta 6), reused 0 (delta 0)
    To github.com:Tavi3h/learnGit.git
     * [new branch]      master -> master
    Branch 'master' set up to track remote branch 'master' from 'origin'.

>
把本地库的内容推送到远程，用`git push`命令，实际上是把当前分支`master`推送到远程。
由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送到远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令。

推送成功后，可以立刻在GitHub页面中看到远程库的内容已经和本地一模一样：

![after_push](images\after_push.PNG)

从现在开始，只要本地做了提交，就可以通过命令：

    $ git push origin master

把本地master分支的最新修改推送至GitHub。

**SSH警告：**

当第一次使用Git的`clone`或`push`命令连接GitHub时会得到一个警告：

    The authenticity of host 'github.com (xx.xx.xx.xx)' can't be established.
    RSA key fingerprint is xx.xx.xx.xx.xx.
    Are you sure you want to continue connecting (yes/no)?

这是因为Git使用SSH连接，而SSH连接在第一次验证GitHub服务器的Key时，需要你确认GitHub的Key的指纹信息是否真的来自GitHub的服务器，输入`yes`回车即可。

Git会输出一个警告，告诉你已经把GitHub的Key添加到本机的一个信任列表里了：

    Warning: Permanently added 'github.com' (RSA) to the list of known hosts.

如果你实在担心有人冒充GitHub服务器，输入yes前可以对照[GitHub的RSA Key的指纹信息](https://help.github.com/articles/github-s-ssh-key-fingerprints/){:target="_blank"}是否与SSH连接给出的一致。

### 从远程库克隆

现在，假设我们从零开发，那么最好的方式是先创建远程库，然后，从远程库克隆。

首先，登陆GitHub，创建一个新的仓库，名字叫gitskills：

![create_repository_gitskills](images\create_repository_gitskills.PNG)

我们勾选**Initialize this repository with a README**，这样GitHub会自动为我们创建一个**README.md**文件。创建完毕后，可以看到**README.md**文件：

![repository_gitskills](images\repository_gitskills.PNG)

现在，远程库已经准备就绪，下一步是使用命令`git clone`克隆一个本地库：

    $ git clone git@github.com:Tavi3h/gitskills.git
    Cloning into 'gitskills'...
    remote: Counting objects: 3, done.
    remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
    Receiving objects: 100% (3/3), done.

然后进入**gitskills**目录看看，已经有**README.md**文件了：

    $ ls
    README.md

Git支持多种协议，默认的`git://`使用ssh，但也可以使用`https`协议。

## 分支管理

分支又什么用？
>
假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。
现在有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。
其他版本控制系统如SVN等都有分支管理，但是用过之后你会发现，这些版本控制系统创建和切换分支比蜗牛还慢，简直让人无法忍受，结果分支功能成了摆设，大家都不去用。
但Git的分支是与众不同的，无论创建、切换和删除分支，Git在1秒钟之内就能完成！无论你的版本库是1个文件还是1万个文件。

在**版本回退中**，我们已经得知，每次提交，Git都把它们传承一条时间线，这条时间线就是一个**分支**。到目前为止，我们只有一条时间线，在Git中，这个分支称为**主分支**。

### 创建与合并分支

一开始的时候，**master**分支是一条线，Git用**master**指向最新的提交，再用**HEAD**指向**master**，就就能确定当前分支，以及当前分支的提交点：

![HEAD_master_01](images\HEAD_master_01.png)

每次提交，**master**分支都会向前移动一步，这样随着不断的提交，**master**分支的线也越来越长：

![commit_D](images\commit_D.png)

当我们创建新的分支，例如**dev**时，Git新建了一个指针叫**Git**，指向**master**指向的提交，再把**HEAD**指向**dev**，就表示当前分支在**dev**上：

![create_branch_dev](images\create_branch_dev.PNG)

Git创建一个分支很快，因为除了增加一个**dev**指针，改变**HEAD**的指向，它不会改动工作区的文件。

不过，**从现在开始**，对工作区的修改和提交就是针对**dev**分支的了，比如再进行一次提交，此时**dev**指针向前移动一步，而**master**指针不变：

![commit_dev](images\commit_dev.PNG)

假如我们在**dev**上的工作完成了，就可以把**dev**合并到master上。Git是如何进行合并的呢？Git会把**master**指向**dev**的当前提交，这样就完成了合并：

![merge_dev_master](images\merge_dev_master.PNG)

合并完成后，甚至可以删除**dev**分支。删除**dev**分支就是把**dev**指针删掉，删除后，我们就只剩下一条**master**分支：

![del_dev](images\del_dev.PNG)

全过程如下：

![create_merge_del_dev](images\create_merge_del_dev.png)

首先，创建dev分支，然后进行切换：

    $ git checkout -b dev
    Switched to a new branch 'dev'

`git checkout -b dev`，表示创建并切换到dev分支，它相当于以下两条命令连用：

    $ git branch dev
    $ git checkout dev
    Switched to a new branch 'dev'

然后使用`git branch`命令查看当前分支：

    $ git branch
    * dev
      master

这个命令会列出所有分支，当前的分支前面会标有一个**\***号。

然后在**dev**分支上进行修改并提交，例如对readme.txt做出修改，加入一行：

    Creating a new branch is quick.

添加并提交：

    $ git add readme.txt
    $ git commit -m "branch test"
    [dev 8639dfa] branch test
     1 file changed, 2 insertions(+), 1 deletion(-)

现在**dev**分支上的工作完成，我们切换回**master**分支：

    $ git checkout master
    Switched to branch 'master'
    Your branch is up to date with 'origin/master'.

切换回master分支后，再查看readme.txt文件：

    $ cat readme.txt
    Git is a distributed version control system.
    Git is free software distributed under the GPL.
    Git has a mutable index called stage.
    Git tracks changes.

此时回发现刚才添加的内容消失了。因为那个提交是在**dev**分支上，而**master**分支此刻的提交点并没有变：

![backto_master](images\backto_master.PNG)

现在将**dev**分支的工作成果合并到**master**分支：

    $ git merge dev
    Updating bf8e449..8639dfa
    Fast-forward
     readme.txt | 3 ++-
     1 file changed, 2 insertions(+), 1 deletion(-)

`git merge`命令用于合并指定到当前分支。合并后，此时再查看readme.txt的内容：

    $ cat readme.txt
    Git is a distributed version control system.
    Git is free software distributed under the GPL.
    Git has a mutable index called stage.
    Git tracks changes.
    Creating a new branch is quick.

可以发现此时的内容和**dev**分支相同了。

注意到上面的**Fast-forward**信息，Git告诉我们，这次合并是“快进模式”，也就是直接把**master**指向**dev**的当前提交，所以合并速度非常快。当然，也不是每次合并都能**Fast-forward**。

合并完成后，就可以放心地删除**dev**分支了：

    $ git branch -d dev
    Deleted branch dev (was 8639dfa).

删除后，再次查看所有的分支，此时就只剩下**master**分支了：

    $ git branch
    * master

>
因为创建、合并和删除分支非常快，所以Git鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在**master**分支上工作效果是一样的，但过程更安全。

### 解决冲突

合并分支往往不是一帆风顺的，通常回发生冲突。

创建一个新的分支**feature1**，在这个分支上进行开发：

    $ git checkout -b feature1

修改**readme.txt**的最后一行：

    Git is a distributed version control system.
    Git is free software distributed under the GPL.
    Git has a mutable index called stage.
    Git tracks changes.
    Creating a new branch is quick AND simple.

添加并提交：

    $ git add readme.txt
    $ git commit -m "AND simple"
    [feature1 53d9e47] AND simple
     1 file changed, 1 insertion(+), 1 deletion(-)

切换到**master分支**：

    $ git checkout master
    Switched to branch 'master'
    Your branch is ahead of 'origin/master' by 1 commit.
      (use "git push" to publish your local commits)

Git还会自动提示我们当前**master**分支比**远程的master**分支要超前1个提交。（之前我们合并了**dev**分支，但是没有进行push）

在**master**分支上把**readme.txt**文件的最后一行改为：

    Creating a new branch is quick & simple.

添加并提交：

    $ git add readme.txt
    $ git commit -m "& simple"
    [master d49d59f] & simple
     1 file changed, 1 insertion(+), 1 deletion(-)

现在**master分支**和**feature1**分支各自都有新的提交，变成了这样：

![master_feature1_conflict](images\master_feature1_conflict.png)

这种情况下Git无法执行“快速合并”，只能视图把各自的修改合并起来，但这种合并可能会发生冲突：

    $ git merge feature1
    Auto-merging readme.txt
    CONFLICT (content): Merge conflict in readme.txt
    Automatic merge failed; fix conflicts and then commit the result.

发生冲突，Git告诉我们，**readme.txt**存在冲突，需要手动解决冲突后再提交。

使用`git status`查看状态也可以告诉我们发生冲突的文件：

    $ git status
    On branch master
    Your branch is ahead of 'origin/master' by 2 commits.
      (use "git push" to publish your local commits)

    You have unmerged paths.
      (fix conflicts and run "git commit")
      (use "git merge --abort" to abort the merge)

    Unmerged paths:
      (use "git add <file>..." to mark resolution)

            both modified:   readme.txt

    no changes added to commit (use "git add" and/or "git commit -a")

查看**readme.txt**的内容：

    $ cat readme.txt
    Git is a distributed version control system.
    Git is free software distributed under the GPL.
    Git has a mutable index called stage.
    Git tracks changes.
    <<<<<<< HEAD
    Creating a new branch is quick & simple.
    =======
    Creating a new branch is quick AND simple.
    >>>>>>> feature1

Git用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容。

我们手动对**readme.txt**的最后一行进行修改并保存：

    Creating a new branch is quick and simple.

再次添加并提交：

    $ git add readme.txt
    $ git commit -m "conflict fixed"
    [master 25f6fab] conflict fixed

现在**master**分支和**feature1**分支变成了下图所示：

![conflict_fixed](images\conflict_fixed.png)

使用带参数的`git log`查看分支的合并情况：

    $ git log --graph --pretty=oneline --abbrev-commit
    *   25f6fab (HEAD -> master) conflict fixed
    |\
    | * 53d9e47 (feature1) AND simple
    * | d49d59f & simple
    |/
    * 8639dfa branch test
    * bf8e449 (origin/master) create test2.txt
    * 5b512a9 create test.txt
    * ba5e49d remove test.txt
    * bdf1c32 add test.txt
    * 43de9b6 git tracks changes
    * 11a8d7e understand how stage works
    * 8f2bf58 append GPL
    * a1b9830 add distributed
    * fd08667 create a readme file

最后删除**feature1**分支：

    $ git branch -d feature1
    Deleted branch feature1 (was 53d9e47).

总的来说，当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。而解决冲突的方法就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

### 分支管理策略

通常情况下合并分支时，如果可能，Git会用**Fast-forward**模式，但在这种模式下，删除分支后，分支中的信息会被丢弃。

我们可以强制**禁用Fast-forward**模式，此时Git在merge时会生成一个新的commit。这样，我们在分支历史中就可以看到分支信息。

创建并切换到**dev**分支：

    $ git checkout -b dev
    Switched to a new branch 'dev'

修改readme.txt文件：

    Git is a distributed version control system.
    Git is free software distributed under the GPL.
    Git has a mutable index called stage.
    Git tracks changes.
    Creating a new branch is quick AND simple.
    Merge with --no-ff.

添加并提交：

    $ git add readme.txt
    $ git commit -m "add merge"
    [dev 3c89fca] add merge
     1 file changed, 1 insertion(+)

切换回**master**：

    $ git checkout master
    Switched to branch 'master'
    Your branch is ahead of 'origin/master' by 4 commits.
      (use "git push" to publish your local commits)

使用`--no-ff`参数对**dev**分支进行合并，`--no-ff`表示禁用**Fast-forward**：

    $ git merge --no-ff -m "merge with no-ff" dev
    Merge made by the 'recursive' strategy.
     readme.txt | 1 +
     1 file changed, 1 insertion(+)

因为合并时要创建一个新的commit，所以这里加上`-m`参数并填写commit描述。

合并后，我们查看一下分支历史：

    $ git log --graph --pretty=oneline --abbrev-commit
    *   bf04a4e (HEAD -> master) merge with no-ff
    |\
    | * 3c89fca (dev) add merge
    |/
    *   25f6fab conflict fixed
    |\
    | * 53d9e47 AND simple
    * | d49d59f & simple
    ...

可以看到，在不使用**Fast-forward**的时候，merge后就像这样：

![merge_with_no_ff](images\merge_with_no_ff.png)

使用这种普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而**Fast-forward**合并就看不出来曾经做过合并。

**分支策略：**

在实际开发中，我们应该按照几个基本原则进行分支管理：

1. **master**分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在这个分支上直接开发。
2. 开发工作应该在**dev**分支上，也就是说该分支是不稳定的。只有到了某个时候才将**dev**分支合并到**master**分支上。
3. 团队中的每个人都在**dev**分支上开发，且每个人都有自己的分支，时不时地向**dev**分支合并就可以了。

所以，团队合作的分支看起来像这样：

![team_branch](images\team_branch.png)

### Bug分支

>
软件开发中，bug就像家常便饭一样。有了bug就需要修复，在Git中，由于分支是如此的强大，所以，每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。

例如，当我们受到一个代号为101的bug时，很自然地，我们要创建一个分支**issue-101**来修复它。但是，假如这个时候我们在dev上正在进行的工作还没有提交怎么办？

示例：

    $ git status
    On branch dev
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

        new file:   hello.py

    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   readme.txt

并不是我们不想提交，而是工作只进行到一半，还没法提交，预计完成还需1天时间。但是，必须在两个小时内修复该bug，怎么办？

Git提供了一个**stash**功能，可以把当前工作现场存储起来，等以后恢复现场后继续工作：

    $ git stash
    Saved working directory and index state WIP on dev: bf04a4e merge with no-ff

现在，查看状态，工作区就是干净的（除非有文件没有被Git管理），因此此时可以放心地创建分支来修复bug。

首先确定要在哪个分支上修复bug，假定需要在**master**分支上修复，就从**master**创建临时分支：

    $ git checkout master
    Switched to branch 'master'
    Your branch is up to date with 'origin/master'.

    $ git checkout -b issue-101
    Switched to a new branch 'issue-101'

假设现在要修复的bug是把**readme.txt**中的“Git is free software ...”改为“Git is a free software ...”，然后添加提交：

    $ git add readme.txt
    $ git commit -m "fix bug 101"
    [issue-101 7ba721a] fix bug 101
     1 file changed, 1 insertion(+), 1 deletion(-)

修复完成后，切换回**master**分支，并进行合并，最后删除**issue-101**分支：

    $ git checkout master
    Switched to branch 'master'
    Your branch is up to date with 'origin/master'.
    
    $ git merge --no-ff -m "merged bug fix 101" issue-101
    Merge made by the 'recursive' strategy.
    readme.txt | 2 +-
     1 file changed, 1 insertion(+), 1 deletion(-)

Bug修复完成，现在回到dev分支继续工作：

    $ git checkout dev
    Switched to branch 'dev'

    $ git status
    On branch dev
    nothing to commit, working tree clean

此时工作区是干净的，那么刚才的工作现场去哪了？需要使用`git stash list`命令查看：

    $ git stash list
    stash@{0}: WIP on dev: bf04a4e merge with no-ff

工作现场还在，Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：

1. 使用`git stash apply`恢复，但是恢复后Stash中的内容并不删除，需要使用`git stash drop`来删除。
2. 另一种方式是使用`git stash pop`，这个命令在恢复的同时会将stash内容删除。

使用`git stash pop`：

    $ git stash pop
    On branch dev
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   readme.txt

    no changes added to commit (use "git add" and/or "git commit -a")
    Dropped refs/stash@{0} (b960709ae9bd49558259456c8084782479efe67d)

当然我们也可以恢复指定内容，先用`git stash list`查看，然后恢复指定的内容，例如：

    $ git stash apply stash@{0} 

### Feature分支

>
软件开发中，总有无穷无尽的新的功能要不断添加进来。
添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。

假设我们现在要开发一个代号为**Vulcan**的新功能，于是我们在**dev**分支上创建一个新的分支用于开发：

    $ git checkout dev

    $ git checkout -b feature-vulcan
    Switched to a new branch 'feature-vulcan'

经过一段时间我们开发完毕，于是添加新文件并提交：

    $ git add Vulcan.java

    $ git commit -m "add feature vulcan"
    [feature-vulcan b9764a5] add feature vulcan
     1 file changed, 0 insertions(+), 0 deletions(-)
     create mode 100644 Vulcan.java

回到dev分支，准备合并：

    $ git checkout dev
    Switched to branch 'dev'

一切顺利的情况下，feature分支和bug分支是类似的，合并，然后删除。

但是，假如现在突然接到接到上级命令，因经费不足，新功能必须取消！虽然白干了，但是这个包含机密资料的分支还是必须就地销毁：

    $ git branch -d feature-vulcan
    error: The branch 'feature-vulcan' is not fully merged.
    If you are sure you want to delete it, run 'git branch -D feature-vulcan'.

删除失败，Git提示为这个分支还没被合并，如果删除会丢失修改。

如果要强行删除，需要使用`-D`参数：

    $ git branch -D feature-vulcan
    Deleted branch feature-vulcan (was 79526b3).

即如果要丢弃一个提交了但没有被合并过的分支，可以通过`git branch -D <name>`强行删除。

### 多人协作

当你从远程仓库克隆时，实际上Git自动把本地的**master**分支和远程的**master**分支对应起来了，并且，远程仓库的默认名称是**origin**。

要查看远程库的信息，用`git remote`或加上参数`-v`显示更详细的信息：

    $ git remote
    origin

    $ git remote -v
    origin  git@github.com:Tavi3h/learnGit.git (fetch)
    origin  git@github.com:Tavi3h/learnGit.git (push)

上面显示了可以抓取和推送的**origin**的地址。如果没有推送权限，就看不到push的地址。

**推送分支：**

推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支**推送到远程库对应的远程分支上**：

    $ git push origin master

如果要推送其他分支，比如**dev**分支，就改成：

    $ git push origin dev

如果从本地推送的是一个新分支（远程库不存在该分支），则远程库会创建该分支。

>
但是，并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？
>
- **master**分支是主分支，因此要时刻与远程同步；
- **dev**分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
- bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
- feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。
>
总之，就是在Git中，分支完全可以在本地自己藏着玩，是否推送，视你的心情而定！

**抓取分支：**

多人协作时，大家都会往**master**和**dev**分支上推送各自的修改。

下面我们模拟另一个小伙伴，在同一个电脑的另一个目录（myCode\GitTMP）下进行克隆：

    $ cd e:
    $ cd myCode
    $ mkdir GitTMP
    $ cd GitTMP

    $ git clone git@github.com:Tavi3h/learnGit.git
    Cloning into 'learnGit'...
    remote: Counting objects: 47, done.
    remote: Compressing objects: 100% (20/20), done.
    remote: Total 47 (delta 25), reused 44 (delta 22), pack-reused 0
    Receiving objects: 100% (47/47), done.
    Resolving deltas: 100% (25/25), done.    

当你的小伙伴从远程库clone时，默认情况下，你的小伙伴只能看到本地的**master**分支。不信可以用`git branch`命令看看：

    $ git branch
    * master

现在，小伙伴要在**dev**分支上开发，就必须创建远程**origin**的**dev**分支到本地，可以这样创建本地**dev**分支：

    $ git checkout -b dev origin/dev
    Switched to a new branch 'dev'
    Branch 'dev' set up to track remote branch 'dev' from 'origin'.

现在，他就可以在**dev**上继续修改，然后时不时地把**dev**分支`push`到远程：

例如修改**dev**分支中的test2.txt，然后提交并`push`：

    $ git add test2.txt
    warning: LF will be replaced by CRLF in test2.txt.
    The file will have its original line endings in your working directory.

    $ git commit -m "Others Test dev branch"
    [dev 5f684b2] Others Test dev branch
     1 file changed, 1 insertion(+)

    $ git push origin dev
    Counting objects: 3, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 277 bytes | 277.00 KiB/s, done.
    Total 3 (delta 1), reused 0 (delta 0)
    To github.com:Tavi3h/learnGit.git
       9af2f0e..5f684b2  dev -> dev

现在你的小伙伴已经向**origin/dev**分支推送了他的提交，而碰巧你也要对同样的文件作修改并试图推送。

回到我们的仓库（myCode\learnGit），进入**dev**分支，对test2.txt做出修改并提交推送：

    $ git add test2.txt
    warning: LF will be replaced by CRLF in test2.txt.
    The file will have its original line endings in your working directory.

    $ git commit -m "MY Changes on Test2.txt"
    [dev 0a221cc] MY Changes on Test2.txt
     1 file changed, 1 insertion(+)

    $ git push origin dev
    To github.com:Tavi3h/learnGit.git
     ! [rejected]        dev -> dev (fetch first)
    error: failed to push some refs to 'git@github.com:Tavi3h/learnGit.git'
    hint: Updates were rejected because the remote contains work that you do
    hint: not have locally. This is usually caused by another repository pushing
    hint: to the same ref. You may want to first integrate the remote changes
    hint: (e.g., 'git pull ...') before pushing again.
    hint: See the 'Note about fast-forwards' in 'git push --help' for details.

推送失败了。因为你的小伙伴的最新提交和你试图推送的提交有冲突，Git已经提示了我们解决的办法，先把最新的提交从**origin/dev**抓下来，然后在本地合并，解决冲突，最后再推送：

    $ git pull
    remote: Counting objects: 3, done.
    remote: Compressing objects: 100% (1/1), done.
    remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
    Unpacking objects: 100% (3/3), done.
    From github.com:Tavi3h/learnGit
       9af2f0e..5f684b2  dev        -> origin/dev
    There is no tracking information for the current branch.
    Please specify which branch you want to merge with.
    See git-pull(1) for details.

        git pull <remote> <branch>

    If you wish to set tracking information for this branch you can do so with:

        git branch --set-upstream-to=origin/<branch> dev

失败了，Git提示我们要指定本地**dev**分支与远程**origin/dev**分支的链接，根据提示，设置**dev**和**origin/dev**的链接：

    $ git branch --set-upstream-to=origin/dev dev
    Branch 'dev' set up to track remote branch 'dev' from 'origin'.

再次进行`git pull`：

    $ git pull
    Auto-merging test2.txt
    CONFLICT (content): Merge conflict in test2.txt
    Automatic merge failed; fix conflicts and then commit the result.

这次`git pull`成功，但是合并有冲突，需要手动解决，解决的办法和**分支管理中的解决冲突的方法**完全一样。解决后，提交再进行推送：

    $ git add test2.txt

    $ git commit -m "fix test2.txt conflict"
    [dev fc72a30] fix test2.txt conflict

    $ git push origin dev
    Counting objects: 6, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (4/4), done.
    Writing objects: 100% (6/6), 589 bytes | 589.00 KiB/s, done.
    Total 6 (delta 2), reused 0 (delta 0)
    To github.com:Tavi3h/learnGit.git
       5f684b2..fc72a30  dev -> dev

>
因此，多人协作的工作模式通常是这样的：
>
1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

如果git pull提示**no tracking information**，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`。

### Rebase

>
在上一节我们看到了，多人在同一个分支上协作时，很容易出现冲突。即使没有冲突，后push的童鞋不得不先`pull`，在本地合并，然后才能`push`成功。

多次合并后的历史记录：

    $ git log --graph --pretty=oneline --abbrev-commit
    * 578edd0 (HEAD -> dev, origin/dev) second changes on test.txt
    * 46d4304 first changes on test.txt
    *   fc72a30 fix test2.txt conflict
    |\
    | * 5f684b2 Others Test dev branch
    * | 0a221cc MY Changes on Test2.txt
    |/
    * 9af2f0e working in dev branch
    *   bf04a4e merge with no-ff
    |\
    | * 3c89fca add merge
    |/
    *   25f6fab conflict fixed
    |\
    | * 53d9e47 AND simple
    * | d49d59f & simple
    |/
    * 8639dfa branch test

为什么Git的提交历史不能是一条干净的直线？其实是可以做到的！

Git有一种称为rebase的操作。

目前我们已经与远程分支同步，现在我们对test2.txt进行两次提交，然后再查看历史：

    $ git log --graph --pretty=oneline --abbrev-commit
    * e51c261 (HEAD -> dev) changes2
    * a52499c changes1
    * 578edd0 (origin/dev) second changes on test.txt
    * 46d4304 first changes on test.txt
    *   fc72a30 fix test2.txt conflict
    |\
    | * 5f684b2 Others Test dev branch
    * | 0a221cc MY Changes on Test2.txt
    |/
    * 9af2f0e working in dev branch
    *   bf04a4e merge with no-ff
    |\
    | * 3c89fca add merge
    |/
    *   25f6fab conflict fixed
    |\
    | * 53d9e47 AND simple
    * | d49d59f & simple
    |/
    * 8639dfa branch test

注意到Git用**(HEAD -> dev)**和**(origin/dev)**标识出当前分支的**HEAD**和远程**origin**的位置分别为**e51c261 changes2**和**578edd0 second changes on test.txt**，本地分支比远程分支快两个提交。

现在我们尝试推送本地分支：

    $ git push origin dev
    To github.com:Tavi3h/learnGit.git
     ! [rejected]        dev -> dev (fetch first)
    error: failed to push some refs to 'git@github.com:Tavi3h/learnGit.git'
    hint: Updates were rejected because the remote contains work that you do
    hint: not have locally. This is usually caused by another repository pushing
    hint: to the same ref. You may want to first integrate the remote changes
    hint: (e.g., 'git pull ...') before pushing again.
    hint: See the 'Note about fast-forwards' in 'git push --help' for details.

推送失败了，这说明有人先于我们推送了远程分支。根据经验，先`pull`一下：

    $ git pull
    remote: Counting objects: 3, done.
    remote: Compressing objects: 100% (2/2), done.
    remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
    Unpacking objects: 100% (3/3), done.
    From github.com:Tavi3h/learnGit
       578edd0..e9151ba  dev        -> origin/dev
    Auto-merging test2.txt
    CONFLICT (content): Merge conflict in test2.txt
    Automatic merge failed; fix conflicts and then commit the result.

然后手工解决冲突并提交：

    $ git commit -m "fixed conflict test2.txt"

查看状态：

    $ git status
    On branch dev
    Your branch is ahead of 'origin/dev' by 3 commits.
      (use "git push" to publish your local commits)

    nothing to commit, working tree clean

此时加上刚才合并的提交，我们的本地分支比远程分支超前3个提交。

再次查看历史：

    $ git log --graph --pretty=oneline --abbrev-commit
    *   ecf838c (HEAD -> dev) fixed conflict test2.txt
    |\
    | * e9151ba (origin/dev) test2.txt changes from GitTMP
    * | e51c261 changes2
    * | a52499c changes1
    |/
    * 578edd0 second changes on test.txt
    * 46d4304 first changes on test.txt
    *   fc72a30 fix test2.txt conflict
    |\
    | * 5f684b2 Others Test dev branch
    * | 0a221cc MY Changes on Test2.txt
    |/
    * 9af2f0e working in dev branch
    *   bf04a4e merge with no-ff
    |\
    | * 3c89fca add merge
    |/
    *   25f6fab conflict fixed
    |\
    | * 53d9e47 AND simple
    * | d49d59f & simple
    |/
    * 8639dfa branch test

> 
对强迫症童鞋来说，现在事情有点不对头，提交历史分叉了。如果现在把本地分支push到远程，有没有问题？
有！
什么问题？
不好看！

这个时候，rebase就派上了用场。我们输入命令`git rebase`试试：

    $ git rebase

再次查看历史：

    $ git log --graph --pretty=oneline --abbrev-commit
    * bacac37 (HEAD -> dev) changes2
    * 691d9b0 changes1
    * e9151ba (origin/dev) test2.txt changes from GitTMP
    * 578edd0 second changes on test.txt
    * 46d4304 first changes on test.txt
    *   fc72a30 fix test2.txt conflict
    |\
    | * 5f684b2 Others Test dev branch
    * | 0a221cc MY Changes on Test2.txt
    |/
    * 9af2f0e working in dev branch
    *   bf04a4e merge with no-ff
    |\
    | * 3c89fca add merge
    |/
    *   25f6fab conflict fixed
    |\
    | * 53d9e47 AND simple
    * | d49d59f & simple
    |/
    * 8639dfa branch test

现在分叉的提交变成一条直线了。原理是对“挪动”本地提交的位置。这就是rebase操作的特点：把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了。

最后`push`本地分支到远程：

    $ git push origin dev
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (9/9), done.
    Writing objects: 100% (9/9), 901 bytes | 901.00 KiB/s, done.
    Total 9 (delta 4), reused 0 (delta 0)
    To github.com:Tavi3h/learnGit.git
       e9151ba..b232dd6  dev -> dev

最后查看历史：

    * b232dd6 (HEAD -> dev, origin/dev) fixed conflict test2.txt
    * bacac37 changes2
    * 691d9b0 changes1
    * e9151ba test2.txt changes from GitTMP
    ...

## 标签管理

>
发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

>
Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。
Git有commit，为什么还要引入tag？
“请把上周一的那个版本打包发布，commit号是6a5819e...”
“一串乱七八糟的数字不好找！”
如果换一个办法：
“请把上周一的那个版本打包发布，版本号是v1.2”
“好的，按照tag v1.2查找commit就行！”
所以，tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起。

### 创建标签

在Git中打标签非常简单，首先切换到需要打标签的分支上：

    $ git checkout master
    Switched to branch 'master'
    Your branch is up to date with 'origin/master'.

使用命令`git tag <name>`打上标签：

    $ git tag v1.0

使用命令`git tag`可以查看所有标签：

    $ git tag
    v1.0

默认标签是打在最新提交的commit上的。当让也可以通过查看历史的方法将标签打在之前的提交上：

    $ git log --pretty=oneline --abbrev-commit
    3a3c50a (HEAD -> master, tag: v1.0, origin/master) Create a .java file
    419163e Merge branch 'dev'
    9af2f0e working in dev branch
    f1a381c merged bug fix 101
    7ba721a fix bug 101
    bf04a4e merge with no-ff
    3c89fca add merge
    25f6fab conflict fixed
    d49d59f & simple
    53d9e47 AND simple
    8639dfa branch test
    bf8e449 create test2.txt
    5b512a9 create test.txt
    ba5e49d remove test.txt
    bdf1c32 add test.txt
    43de9b6 git tracks changes
    11a8d7e understand how stage works
    8f2bf58 append GPL
    a1b9830 add distributed
    fd08667 create a readme file

例如要对**3c89fca add merge**打标签：

    $ git tag v0.9 3c89fca

再次查看所有标签：

    $ git tag
    v0.9
    v1.0

注意：**标签不是按时间顺序列出，而是按字母排序的**。可以用`git show <tagname>`查看标签信息：

    $ git show v0.9
    commit 3c89fca875823fcebc0022523d98453c8c7a9425 (tag: v0.9)
    Author: Tavish <bit_zjm@hotmail.com>
    Date:   Fri Jun 8 19:46:55 2018 +0800

        add merge

    diff --git a/readme.txt b/readme.txt
    index d4f86ac..f5d09c2 100644
    --- a/readme.txt
    +++ b/readme.txt
    @@ -3,3 +3,4 @@ Git is free software distributed under the GPL.
     Git has a mutable index called stage.
     Git tracks changes.
     Creating a new branch is quick AND simple.
    +Merge with --no-ff.

还可以常见带有说明的标签，用`-a`指定标签名，`-m`指定说明信息：

    $ git tag -a v0.1 -m "version 0.1 released" 8f2bf58

使用`git show <tagname>`可以看到说明信息：

    $ git show v0.1
    tag v0.1
    Tagger: Tavish <bit_zjm@hotmail.com>
    Date:   Sat Jun 9 14:12:01 2018 +0800

    version 0.1 released

    commit 8f2bf582e207205c016e2871289029576426eacf (tag: v0.1)
    Author: Tavish <bit_zjm@hotmail.com>
    Date:   Thu Jun 7 09:16:14 2018 +0800

        append GPL

    diff --git a/readme.txt b/readme.txt
    index 9247db6..99e0e11 100644
    --- a/readme.txt
    +++ b/readme.txt
    @@ -1,2 +1,2 @@
     Git is a distributed version control system.
    -Git is free software.
    +Git is free software distributed under the GPL.
    \ No newline at end of file

注意：**标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签**。

### 操作标签

如果标签打错了，可以使用`-d`进行删除：

    $ git tag -d v0.1
    Deleted tag 'v0.1' (was 8653b73)

因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。

如果要推送某个标签到远程，使用命令`git push origin <tagname>`：

    $ git push origin v1.0
    Total 0 (delta 0), reused 0 (delta 0)
    To github.com:Tavi3h/learnGit.git
     * [new tag]         v1.0 -> v1.0

如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除：

    $ git tag -d v1.0
    Deleted tag 'v1.0' (was 3a3c50a)

然后从远程删除，使用的命令也是`push`但格式不同：

    $ git push origin --delete tag v1.0
    To github.com:Tavi3h/learnGit.git
     - [deleted]         v1.0

## 使用GitHub

>
我们一直用GitHub作为免费的远程仓库，如果是个人的开源项目，放到GitHub上是完全没有问题的。其实GitHub还是一个开源协作社区，通过GitHub，既可以让别人参与你的开源项目，也可以参与别人的开源项目。
>
在GitHub出现以前，开源项目开源容易，但让广大人民群众参与进来比较困难，因为要参与，就要提交代码，而给每个想提交代码的群众都开一个账号那是不现实的，因此，群众也仅限于报个bug，即使能改掉bug，也只能把diff文件用邮件发过去，很不方便。
>
但是在GitHub上，利用Git极其强大的克隆和分支功能，广大人民群众真正可以第一次自由参与各种开源项目了。

如何参与一个开源项目呢？

比如人气极高的bootstrap项目，这是一个非常强大的CSS框架，我们可以访问它的[项目主页](https://github.com/twbs/bootstrap){:target="_blank"}，点击**Fork**就在自己的账号下克隆了一个bootstrap仓库，然后到自己的账号下`clone`：

    git clone git@github.com:Tavi3h/bootstrap.git

一定要从自己的账号下clone仓库，这样你才能推送修改。如果从bootstrap的作者的仓库地址克隆，因为没有权限，我们将不能推送修改。

bootstrap的官方仓库**twbs/bootstrap**、你在GitHub上克隆的仓库**my/bootstrap**，以及你自己克隆到**本地电脑的仓库**，他们的关系就像下图显示的那样：

![bootstrap](images\bootstrap.png)

如果我们想修复bootstrap的一个bug，或者新增一个功能，立刻就可以开始干活，干完后，往自己的仓库推送。

如果我们希望bootstrap的官方库能接受你的修改，那么可以在GitHub上发起一个pull request。当然，对方是否接受你的pull request就不一定了。

## 使用码云

关于[码云](https://gitee.com/){:target="_blank"}的介绍和使用参见原始教程中的[使用码云](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00150154460073692d151e784de4d718c67ce836f72c7c4000){:target="_blank"}部分。

## 自定义Git

Git有很多可配置项。

比如，让Git显示颜色，会让输出命令看起来更醒目。

### 忽略特殊文件

>
有些时候，你必须把某些文件放到Git工作目录中，但又不能提交它们，比如保存了数据库密码的配置文件啦，等等，每次`git status`都会显示Untracked files ...，有强迫症的童鞋心里肯定不爽。
>
好在Git考虑到了大家的感受，这个问题解决起来也很简单，在Git工作区的根目录下创建一个特殊的.gitignore文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件。
>
不需要从头写.gitignore文件，GitHub已经为我们准备了各种配置文件，只需要组合一下就可以使用了。所有配置文件可以直接在线浏览：[A collection of useful .gitignore templates](https://github.com/github/gitignore){:target="_blank"}。

忽略文件的原则如下：

- 忽略操作系统自动生成的文件，比如缩略图等；
- 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；
- 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

举例来说：

假设你在Windows下进行Python开发，Windows会自动在有图片的目录下生成隐藏的缩略图文件，如果有自定义目录，目录下就会有**Desktop.ini文件**，因此你需要忽略Windows自动生成的垃圾文件：

    # Windows:
    Thumbs.db
    ehthumbs.db
    Desktop.ini

然后，继续忽略Python编译产生的.pyc、.pyo、dist等文件或目录：

    # Python:
    *.py[cod]
    *.so
    *.egg
    *.egg-info
    dist
    build

最后加上自己定义的文件，最终得到一个完整的.gitignore文件：

    # Windows:
    Thumbs.db
    ehthumbs.db
    Desktop.ini

    # Python:
    *.py[cod]
    *.so
    *.egg
    *.egg-info
    dist
    build

    # My configurations:
    db.ini
    deploy_key_rsa

最后一步就是把.gitignore也提交到Git，就完成了！当然检验.gitignore的标准是git status命令是不是说**working directory clean**。

使用Windows时要注意，如果你在资源管理器里新建一个.gitignore文件，它会非常弱智地提示你必须输入文件名，但是在文本编辑器里“保存”或者“另存为”就可以把文件保存为.gitignore了。

有些时候，你想添加一个文件到Git，但发现添加不了，原因是这个文件被.gitignore忽略了：

    $ git add App.class
    The following paths are ignored by one of your .gitignore files:
    App.class
    Use -f if you really want to add them.

如果你确实想添加该文件，可以用`-f`强制添加到Git：

    $ git add -f App.class

或者你发现，可能是.gitignore写得有问题，需要找出来到底哪个规则写错了，可以用`git check-ignore`命令检查：

    $ git check-ignore -v App.class
    .gitignore:3:*.class    App.class

Git会告诉我们，.gitignore的第3行规则忽略了该文件，于是我们就可以知道应该修订哪个规则。

### 配置别名

我们可以为命令起一个别名。

例如使用`git st`表示`git status`：

    $ git config --global alias.st status

这样以后`st`就表示`status`。

同样的，也可以为`checkout`、`commit`、`branch`等起别名：

    $ git config --global alias.co checkout
    $ git config --global alias.ci commit
    $ git config --global alias.br branch

这里`--global`参数是全局参数，也就是这些命令在这台电脑的所有Git仓库下都有用。

之前在撤销修改中，我们使用命令`git reset HEAD file`将暂存区的修改撤销（**unstage**），重新放回工作区。既然这个操作称为unstage，我们可以配置一个unstage别名：

    $ git config --global alias.unstage 'reset HEAD'

下次使用命令：

    $ git unstage readme.txt

实际执行的就是：

    $ git reset HEAD readme.txt

例如可以配置一个`git last`让其显示最后一次提交的信息：

    $ git config --global alias.last 'log -1'

>
甚至还有人丧心病狂地把`lg`配置成了：

    $ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

**配置文件：**

配置Git的时候，加上`--global`是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。

每个仓库的Git配置文件都放在.git/config文件中：

    $ cat .git/config
    [core]
            repositoryformatversion = 0
            filemode = false
            bare = false
            logallrefupdates = true
            symlinks = false
            ignorecase = true
    [gui]
            wmstate = normal
            geometry = 841x483+130+130 189 218
    [remote "origin"]
            url = git@github.com:Tavi3h/learnGit.git
            fetch = +refs/heads/*:refs/remotes/origin/*
    [branch "master"]
            remote = origin
            merge = refs/heads/master
    [branch "dev"]
            remote = origin
            merge = refs/heads/dev
    [branch "test-branch"]
            remote = origin
            merge = refs/heads/test-branch

别名的设置就在[alias]中，要删除别名，直接删掉对应行即可。

而当前用户的Git配置文件放在用户主目录下的一个隐藏文件.gitconfig中：

    $ cd

    $ cat .gitconfig
    [user]
            name = Tavish
            email = bit_zjm@hotmail.com
    [sendpack]
            sideband = false

对别名的配置也可以直接修改这个文件。

### 搭建Git服务器

搭建Git服务器参见原始教程的[搭建Git服务器](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000){:target="blank"}部分。

