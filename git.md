#Git命令记录:

* **git init** 把当前(cmd)所在的位置初始化为一个仓库

* **git add readme.txt** 添加一个文件(还没有提交),此命令可以使用通配符，另外git add -A表示提交当前目录下的所有文件。

* **git commit -m "提交信息"** 你懂的

* **git status**命令可以随时查看当前目录的状态。

* **git diff ReadMe.txt(文件名)**命令可以查看这个文件做出了哪些修改。

* **git log**命令可以查看修改的历史记录。可以加上--pretty=oneline参数好看点。

* 版本修改:
  * 回退比较简单，只需执行**git reset --hard head^**,head指当前版本，后面加一个^就代表回退一个版本，回退两个就是head^^，如果特别多，比如100，那么可以head`2
  * 向前回退到新的版本:这个必须使用版本号，可以找找当前cmd窗口中有没有所需版本的版本号，如果有直接执行:**git reset --hard 版本号**。注意版本号不需要全部输入，只要可以区分出来就可以。如果当前cmd没有，可以执行**git reflog**命令找。

* 修改撤销:
  * 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令**git checkout -- file**。
  * 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令**git reset HEAD file**，就回到了场景1，第二步按场景1操作。
  * 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

* 删除文件:
  可以直接在资源管理器中删除或者是使用rm命令，此时git库还是有记录，有两种方式保持同步:

  - git库也删除:**git rm test.txt**即可
  - 再次恢复到本地:**git checkout test.txt**

* .gitignore无效:

  需要先删除缓存: **git rm -r --cached .**，再次提交即可。

#远程仓库:

##获取ssh key:
如果用户主目录(C:\Users\xsdwe_000\.ssh)有没有id_rsa和id_rsa.pub这两个文件，如果没有，可以使用命令**ssh-keygen -t rsa -C "邮件地址"**创建SSH key，完成后还需要在github注册这个ssh key，即把id_rsa.pub的内容复制到文本框。
设置用户名和邮箱:
`git config --global user.name "skywalker"`
`git config --global user.email "xsdwem7@hotmail.com"`

##查看本地仓库关联的远程仓库:
执行**git remote**可加-v参数

##本地仓库关联github远程仓库:
* 首先创建一个github仓库，比如一个叫origin的仓库，记录下SSH地址，此处就是*git@github.com:seaswalker/origin.git*,特别注意下，eclipse插件使用的是Https地址，而此处是SSH地址。

* 在本地仓库下执行**git remote add origin git@github.com:seaswalker/origin.git**即可关联完成。此命令执行完后，没有输出。

* 本地内容全部推到github:**git push -u origin master**。-u参数只需要第一次push的时候加上，目的是建立本地master分支和远程仓库master的联系。

* 如果远程服务器有本地没有的内容，那么会提示:
  ![push错误](images/push_error.png)
  那么需要先执行一次**pull origin master**命令(当然仓库的名字可能不是origin，branch名字也不是master)，更新本地仓库。

* 上面所有的命令都是SSH，但是也可以使用https。

##远程仓库clone到本地:
执行命令**git clone git@github.com:seaswalker/origin.git**即可。

#分支(branch):

* 查看分支：**git branch**

* 创建分支：**git branch name**

* 切换分支：**git checkout name**

* 创建+切换分支： **git checkout -b name**

* 合并某分支到当前分支： **git merge name**

* 删除分支： **git branch -d name**，如果d是大写，那么是强制删除。

* 查看分支合并图: **git log --graph**。

* 默认的合并分支使用"Fast Forward"模式，在此模式下，删除分支后，分支的信息会被丢失，可以禁用此模式，此时就相当于一个commit，所以也需要一个commit信息，命令如下:**git merge --no-ff -m "commit info" XXX**

* "快照"功能，可以用于以下场景:当前正在dev分支开发，但是发现了一个bug需要修复，当前的分支还没有完成无法提交，这就需要创建一个快照，使用命令**git stash**即可。stash:储藏之意。
  快照的恢复:
  1. 可以使用**git stash list**命令查看快照列表，**git stash apply**命令即可回复到快照，但是此时快照没有删除，还需执行**git stash drop**命令。
  2. 直接执行**git stash pop**命令即可在回复最新的快照的同时删除此快照。

* 默认情况下，clone只会把master分支clone下来，如果想要使用其它的分支，可以使用命令:**git checkout -b dev origin/dev**，意思就是根据远程仓库的dev分支建立本地的dev分支。

#标签:

##创建:

* 使用**git tag tagname**就可以为当前分支的最后一次commit创建一个tag，比如*git tag V1.0*

* 如果想要为一个过去的commit创建一个tag，那么应该首先使用**git log --pretty=oneline**找出那次提交的id，然后使用**git tag tagname commitid**即可。

* 创建带有说明信息的tag:**git tag -a tagname -m 说明信息**即可。

* 使用PGP签名创建一个tag:**git tag -s tagname -m 说明信息**即可。

##查看:

* 查看标签**git show tagname**命令。

* **git tag**命令可以查看所有的标签。

##修改:

* **git push origin tagname**可以推送一个本地标签。

* 命令**git push origin --tags**可以推送全部未推送过的本地标签。

* 命令**git tag -d tagname**可以删除一个本地标签。

* 命令**git push origin :refs/tags/tagname**可以删除⼀个远程标签。

#忽略文件:

在仓库的**根目录**建立.gitignore文件，里面的文件名都会被忽略，这个文件可以参考[https://github.com/github/gitignore](https://github.com/github/gitignore)里面有各种语言的此文件写法。

#命令别名:

比如命令**git config --global alias.st status**就实现了st代替status输入git st就等同于git status。

#搭建git服务器:

对着75页敲就行了。

#最后，git官网送上:

>[http://git-scm.com](http://git-scm.com)

2015年7月24日 09:44:09
