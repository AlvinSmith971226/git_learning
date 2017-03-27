# git学习笔记
====================
## 创建版本库
+ 初始化一个Git仓库，使用git init命令。
+ 添加文件到Git仓库，分两步：
第一步，使用命令git add <file>，注意，可反复多次使用，添加多个文件；
第二步，使用命令git commit，完成。


## 时光穿梭机
要随时掌握工作区的状态，使用git status命令。
如果git status告诉你有文件被修改过，用git diff可以查看修改内容。
#### 版本回退
HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset --hard commit_id。
穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。  
要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。
#### 工作区和暂存区
前面讲了我们把文件往Git版本库里添加的时候，是分两步执行的：
第一步是用git add把文件添加进去，实际上就是把文件修改添加到暂存区；
第二步是用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支。
因为我们创建Git版本库时，Git自动为我们创建了唯一一个master分支，所以，现在，git commit就是往master分支上提交更改。
你可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。
所以，git add命令实际上就是把要提交的所有修改放到暂存区（Stage），然后，执行git commit就可以一次性把暂存区的所有修改提交到分支。
#### 管理修改
Git跟踪并管理的是修改，而非文件。每次修改，如果不add到暂存区，那就不会加入到commit中。
#### 撤销修改
git checkout -- file可以丢弃工作区的修改：
命令git checkout -- readme.txt意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：
一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
总之，就是让这个文件回到最近一次git commit或git add时的状态。

+ git checkout -- file命令中的--很重要，没有--，就变成了“切换到另一个分支”的命令，我们在后面的分支管理中会再次遇到git checkout命令

+ 用命令git reset HEAD file可以把暂存区的修改撤销掉（unstage），重新放回工作区
git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本。
总结：
+ 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。
+ 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。

+ 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库

#### 删除文件
+ ```$ rm test.txt```
一般情况下，你通常直接在文件管理器中把没用的文件删了，或者用rm命令删了：
现在你有两个选择，一是确实要从版本库中删除该文件，那就用命令git rm删掉，并且git commit，文件就从版本库中被删除了。
另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：$ git checkout -- test.txt
git checkout其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。
命令git rm用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容。


## 远程仓库
####“有了远程仓库，妈妈再也不用担心我的硬盘了。”——Git点读机
#### 添加远程库
+ 要关联一个远程库，使用命令git remote add origin git@server-name:path/repo-name.git；
+ 关联后，使用命令git push -u origin master第一次推送master分支的所有内容；
+ 此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；
#### 从远程库clone
`$ git clone git@github.com:michaelliao/gitskills.git`
+ 要克隆一个仓库，首先必须知道仓库的地址，然后使用git clone命令克隆。
+ Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快

## 分支管理
#### 创建与合并分支
+ Git鼓励大量使用分支：
+ 查看分支：```git branch```
+ 创建分支：```git branch <name>```
+ 切换分支：```git checkout <name>```
+ 创建+切换分支：```git checkout -b <name>```
+ 合并某分支到当前分支：```git merge <name>```
+ 删除分支：```git branch -d <name>```

#### 解决冲突
+ 当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。
+ 用git log --graph命令可以看到分支合并图。

#### 分支管理策略
+ Git分支十分强大，在团队开发中应该充分应用。
通常，合并分支时，如果可能，Git会用Fast forward模式，但这种模式下，删除分支后，会丢掉分支信息。
+ 如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。```$ git merge --no-ff -m "merge with no-ff" dev```

#### bug分支
+ 修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；
+ 当手头工作没有完成时，先把工作现场```git stash```一下，然后去修复bug，修复后，再```git stash pop```，回到工作现场。

#### feature分支
+ 开发一个新feature，最好新建一个分支；
+ 如果要丢弃一个没有被合并过的分支，可以通过```git branch -D <name>```强行删除。

#### 多人协作
+ 查看远程库信息，使用```git remote -v```；
+ 本地新建的分支如果不推送到远程，对其他人就是不可见的；
+ 从本地推送分支，使用```git push origin branch-name```，如果推送失败，先用git pull抓取远程的新提交；
+ 在本地创建和远程分支对应的分支，使用```git checkout -b branch-name origin/branch-name```，本地和远程分支的名称最好一致；
+ 从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。
+ 建立本地分支和远程分支的关联，使用```git branch --set-upstream branch-name origin/branch-name```；


## 标签管理
#### 创建标签
+ 命令git tag <name>用于新建一个标签，默认为HEAD，也可以指定一个commit id；

``git tag -a <tagname> -m "blablabla..."``可以指定标签信息；

``git tag -s <tagname> -m "blablabla..."``可以用PGP签名标签；

命令git tag可以查看所有标签
#### 操作标签
命令```git push origin <tagname>```可以推送一个本地标签；

命令`git push origin --tags`可以推送全部未推送过的本地标签；

命令``git tag -d <tagname>``可以删除一个本地标签；

命令``git push origin :refs/tags/<tagname>``可以删除一个远程标签。

## 使用GitHub
``git clone git@github.com:michaelliao/bootstrap.git``

在GitHub上，可以任意Fork开源仓库；

自己拥有Fork后的仓库的读写权限；

可以推送pull request给官方仓库来贡献代码

## 自定义git

#### 忽略特殊文件
忽略某些文件时，需要编写.gitignore；

.gitignore文件本身要放到版本库里，并且可以对.gitignore做版本管理！

#### 配置别名

# 最后
+ 该笔记记录自[廖大大的官方网站](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000),感谢廖大大！
+ 后面有时间再继续深入学习 -)
