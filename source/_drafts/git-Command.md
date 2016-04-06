一直都是在用eclipse或者IDEA的界面来用公司给的git，github上面的代码一直用github for desktop来管理。
以偷懒为己任的我从来也没有想过该怎么敲命令。

但是家里电脑想把代码拉下来看看的时候，却发现windows上什么转码之类的破事儿把所有代码都搞傻了。
git认为我的所有刚刚拉下来的代码全部都是新的，旧的全部不认识了。
这可怎么是好啊。

自己github上面一直都是乱的一坨，正好这两天一直在用git bash来更新HEXO，一直以来的感觉就是开发的哥们们敲命令行各种push分支，merge分支什么的看上去好厉害...

自己赶紧来学习一下Git的命令，省的以后真的用起来啥都不会到处丢人

先把手册找出来翻翻 [Git User Guide](https://git-scm.com/)


1. 初始化
===
## 1.1 remote源是已经在使用中的状态
按照网上看到的教程，基本上就是从检出开始，假设已经配置好了Git源
```bash
mkdir TestRepo
cd TestRepo
git clone git://github.com/evinoca/TestRepo.git
```
对，一般我就会这些...可以看出平常太依靠图形UI是多么可怕

## 1.2 如果remote库是空白库
```bash
mkdir TestRepo
cd TestRepo
git init #初始化git
git remote add origin git@github.com:evinoca/TestRepo.git
```
## 1.3 新建文件
然后如果需要新建一个文件并让git追踪这个文件
```bash
touch testfile.txt
git add testfile.txt
git commit -m "create a test file"
git push origin master
```
就行了，git命令总体来说很是很好理解的

## 1.4 删除文件
光是删除文件会在commit的时候出现untrack提示，需要手动让git不在追踪这个文件
```bash
rm testfile.txt
git remove testfile.txt
git commit -m "removed testfile.txt" #参数-m 使用后面的字符串来当做提交的内容描述，省的vi弹出那么大一个文件让看
git push -u origin master #这里origin就是个小名，随自己爱好起都行，master是默认分支
```
其中push命令的参数
```bash
git push <remote> <branch>
```
# 2. 分支处理
按照上面的命令，默认会在master源上进行操作。使用Git的主要目的就是分支代码管理，所以一般怎么着也得建立一个dev分支来写东西，当完成并测试完成后再切换回主分支，把dev分支的变化合并回主分支上

```bash
git checkout -b dev #使用-b会创建并切换至目标分支上
touch testfile2.txt
git add testfile2.txt #也可以用git add . 或者 git add -A 来添加当前目录所有文件
git commit -m "add testfile2.txt on dev branch"
git checkout master
git merge -m "merge from dev" dev
git push origin master
```
听说默认git使用Fast forward模式，所以更好是使用--no-ff模式，避免丢失分支信息

```bash
git merge --no-ff -m "merge with no-ff" dev

```
使用git log命令看看分支到底是什么情况

```bash
git log --graph --pretty=oneline --abbrev-commit

$ git log --graph --pretty=oneline --abbrev-commit
*   ad48a17 merge
|\
| * 564fc94 add file3
* |   a6c790b Merge pull request #1 from evinoca/dev
|\ \
| |/
| * f108c42 add file2
|/
* affcf6d add
```

在上面的情况下，本地分支dev实际上在库上是没有的。代码的修改会在本地分支dev上保存，合并后保存到本地master上。
如果想把活儿带走，还需要把dev分支推到远端。

```bash
git checkout dev
git push origin dev
```

# Git常用操作命令
最后把找到的常用命令合集放在这儿备用，转自[Git常用操作命令收集](http://rongjih.blog.163.com/blog/static/335744612010112562833316/)

Git常用操作命令收集：
1) 远程仓库相关命令
检出仓库：$ git clone git://github.com/jquery/jquery.git
检出仓库：$ git clone -o github -b 2.0.0 git://github.com/jquery/jquery.git jquery2
       ->直接检出分支 2.0.0，指定远程仓库名称为 github （默认为 origin）、检出到文件夹 jquery2 (默认为 jquery)
查看远程仓库：$ git remote -v
添加远程仓库：$ git remote add [name] [url]
删除远程仓库：$ git remote rm [name]
修改远程仓库：$ git remote set-url --push [name] [newUrl]
拉取远程仓库：$ git pull [remoteName] [localBranchName]
推送远程仓库：$ git push [remoteName] [localBranchName]
强制推送更改：$ git push --force origin master

* 如果想把本地的某个分支test提交到远程仓库，并作为远程仓库的master分支，或者作为另外一个名叫test的分支，如下：
$ git push origin test:master         // 提交本地test分支作为远程的master分支
$ git push origin test:test              // 提交本地test分支作为远程的test分支

2）分支(branch)操作相关命令
查看本地分支：$ git branch
查看远程分支：$ git branch -r （如果还是看不到就先 git fetch origin 先）
创建本地分支：$ git branch [name] ----注意新分支创建后不会自动切换为当前分支
切换分支：$ git checkout [name]
创建新分支并立即切换到新分支：$ git checkout -b [name]
直接检出远程分支：$ git checkout -b [name] [remoteName] (如：git checkout -b myNewBranch origin/dragon)
删除分支：$ git branch -d [name] ---- -d选项只能删除已经参与了合并的分支，对于未有合并的分支是无法删除的。如果想强制删除一个分支，可以使用-D选项
合并分支：$ git merge [name] ----将名称为[name]的分支与当前分支合并
合并最后的2个提交：$ git rebase -i HEAD~2 ---- 数字2按需修改即可（如果需提交到远端$ git push -f origin master 慎用！）
创建远程分支(本地分支push到远程)：$ git push origin [name]
删除远程分支：$ git push origin :heads/[name] 或 $ git push origin :[name] 
修改本地分支名称：git branch -m <old_branch_name> <new_branch_name>  
直接修改远程分支名称(不修改本地的)：git push <remote> <remote>/<old_name>:refs/heads/<new_name> :<old_name>
修改本地及其远程分支的名称：
        1. git branch -m <old_name> <new_name>
        2. git push <remote> --set-upstream new_name
        3. git push <remote> :old_name
        对于其它客户端的仓库，需要执行如下两个命令获取最新的修改：
        4. git fetch <remote>
        5. git remote prune <remote>

* 创建空的分支：(执行命令之前记得先提交你当前分支的修改，否则会被强制删干净没得后悔)
         方法1：
               $ git checkout --orphan <branch_name>
               $ git rm -rf *
       方法2：
               $ git symbolic-ref HEAD refs/heads/[name]
               $ rm .git/index
               $ git clean -fdx

3）版本(tag)操作相关命令
查看版本：$ git tag
创建版本：$ git tag [name]
删除版本：$ git tag -d [name]
查看远程版本：$ git tag -r
创建远程版本(本地版本push到远程)：$ git push origin [name]
删除远程版本：$ git push origin :refs/tags/[name]
合并远程仓库的tag到本地：$ git pull origin --tags
上传本地tag到远程仓库：$ git push origin --tags
创建带注释的tag：$ git tag -a [name] -m 'yourMessage'

4) 子模块(submodule)相关操作命令
添加子模块：$ git submodule add [url] [path]
    如：$ git submodule add git://github.com/soberh/ui-libs.git src/main/webapp/ui-libs
初始化子模块：$ git submodule init  ----只在首次检出仓库时运行一次就行
更新子模块：$ git submodule update ----每次更新或切换分支后都需要运行一下
删除子模块：（分4步走哦）
 1) $ git rm --cached [path]
 2) 编辑“.gitmodules”文件，将子模块的相关配置节点删除掉
 3) 编辑“ .git/config”文件，将子模块的相关配置节点删除掉
 4) 手动删除子模块残留的目录

5）忽略一些文件、文件夹不提交
在仓库根目录下创建名称为“.gitignore”的文件，写入不需要的文件夹名或文件，每个元素占一行即可，如
target
bin
*.db

6）后悔药
删除当前仓库内未受版本管理的文件：$ git clean -f
恢复仓库到上一次的提交状态：$ git reset --hard
回退所有内容到上一个版本：$ git reset HEAD^
回退a.py这个文件的版本到上一个版本：$ git reset HEAD^ a.py
回退到某个版本：$ git reset 057d 
将本地的状态回退到和远程的一样：$ git reset –hard origin/master  
向前回退到第3个版本：$ git reset –soft HEAD~3
修改最后的提交日志：$ git commit --amend
修改最后的提交日期为当前时间：$ git commit --amend  --date="$(date -R)"

7）Git一键推送多个远程仓库
编辑本地仓库的.git/config文件：
[remote "all"]
    url = git@github.com:dragon/test.git
    url = git@gitcafe.com:dragon/test.git
这样，使用git push all即可一键Push到多个远程仓库中。

8）缓存认证信息
$ git config credential.helper cache

9）查看提交日志
》查看文件中的每一行的作者、最新的变更提交和提交时间
$ git blame [fileName]
Git常用操作命令 - rongjih - 拥有自己的梦想，跟随心的召唤
》查看仓库历史记录
有三个应该知道的选项。
--oneline - 压缩模式，在每个提交的旁边显示经过精简的提交哈希码和提交信息，以一行显示。
--graph - 图形模式，使用该选项会在输出的左边绘制一张基于文本格式的历史信息表示图。如果你查看的是单个分支的历史记录的话，该选项无效。
--all - 显示所有分支的历史记录
把这些选项组合起来之后如下：
Git常用操作命令 - rongjih - 拥有自己的梦想，跟随心的召唤
使用 $ git log --oneline --graph --name-status 既可以看到简介的日志信息，也可以看到改了哪些文件，一举两得：
Git常用操作命令 - rongjih - 拥有自己的梦想，跟随心的召唤

10）有选择的合并 - 这个功能最赞，没有之一
cherry-pick 可以从不同的分支中捡出一个单独的commit，并把它和你当前的分支合并。如果你以并行方式在处理两个或以上分支，你可能会发现一个在全部分支中都有的bug。如果你在一个分支中解决了它，你可以使用cherry-pick命令把它commit到其它分支上去，而不会弄乱其他的文件或commit。
$ git cherry-pick [commitHash]

11）Stash未提交的更改
正在修改某个bug或者某个特性，又突然被要求展示工作。而现在所做的工作还不足以提交，这个阶段还无法进行展示（不能回到更改之前）。在这种情况下， git stash可以帮到忙了。stash在本质上会取走所有的变更并存储它们以备将来使用。
$ git stash
检查stash列表：$ git stash list
想解除stash并且恢复未提交的变更，就进行apply stash：$ git stash apply
如果只想留有余地进行apply stash，给apply添加特定的标识符：$ git stash apply stash@{0}

12）多次修改后拆分提交 - 暂存文件的部分改动
一般情况下，创建一个基于特性的提交是比较好的做法，意思是每次提交都必须代表一个新特性的产生或者是一个bug的修复。如果你修复了两个bug，或是添加了多个新特性但是却没有提交这些变化会怎样呢？在这种情况下，你可以把这些变化放在一次提交中。但更好的方法是把文件暂存(Stage)然后分别提交。
例如你对一个文件进行了多次修改并且想把他们分别提交。这种情况下，可以在 add 命令中加上 -p 参数
$ git add -p [fileName]

13）压缩多个Commit
用rebase命令把多个commit压缩成一个
git rebase -i HEAD~[number_of_commits]
如果你想要压缩最后两个commit，你需要运行下列命令：
git rebase -i HEAD~2
Docs: 7.6 Git 工具 - 重写历史 、3.6 Git 分支 - 变基

14）差异查看
$ git diff --name-status HEAD~2 HEAD~3 <-- 获得两个版本间所有变更的文件列表
$ git diff HEAD HEAD~1 <-- 查看最近两个提交之间的差异
$ git diff HEAD HEAD~2 <-- 查看第1个与第3个提交之间的差异
^ - 代表父提交，^n 表示第n个父提交，^相当于^1 git寻根：^和~的区别 - 分析得很到位
~ - 代表连续的提交，~n相当于连续的第n个提交
$ git diff master..test <-- 比较两个分支之间的差异
$ git diff master...test <-- 比较master、test的共有父分支和 test 分支之间的差异
$ git diff test <-- 比较当前工作目录与 test 分支的差异
$ git diff HEAD <-- 比较当前工作目录与上次提交的差异
$ git diff HEAD -- ./lib  <-- 比较当前工作目录下的lib目录与上次提交的差异
$ git diff --stat  <-- 统计一下有哪些文件被改动，有多少行被改动
$ git diff --cached  <-- 查看下次提交时要提交的内容(staged,添加到索引中)

15）Git for Windows 中文乱码问题 (1.9.4-preview20140611)
》git log 显示的文件名乱码
执行 "git config –global core.quotepath false"可以解决之。core.quotepath设为false，就不会对0×80以上的字符进行quote，中文就显示正常。
修正前：
Git常用操作命令 - rongjih - 拥有自己的梦想，跟随心的召唤
修正后：
Git常用操作命令 - rongjih - 拥有自己的梦想，跟随心的召唤
》ls命令显示的中文名乱码
改用"ls --show-control-chars"命令代替单纯的"ls"命令即可。
或者编辑.../Git/etc/git-completion.bash，新增一行 alias ls="ls –show-control-chars"

# 参考
Reference:
1. [Git 命令参数及用法详解](http://www.open-open.com/lib/view/open1335879873983.html)
2. [Git详解之一 Git起步 ](http://www.open-open.com/lib/view/open1328069609436.html)
3. [Git详解之二 Git基础](http://www.open-open.com/lib/view/open1328069733264.html)
4. [Git详解之三 Git分支](http://www.open-open.com/lib/view/open1328069889514.html)
5. [Git详解之四 服务器上的Git ](http://www.open-open.com/lib/view/open1328069988843.html)
6. [Git详解之五 分布式Git ](http://www.open-open.com/lib/view/open1328070090108.html)
7. [Git详解之六 Git工具](http://www.open-open.com/lib/view/open1328070367499.html)
8. [Git详解之七 自定义Git ](http://www.open-open.com/lib/view/open1328070404827.html)
9. [Git详解之八 Git与其他系统](http://www.open-open.com/lib/view/open1328070454218.html)
10. [Git详解之九 Git内部原理](http://www.open-open.com/lib/view/open1328070620202.html)





