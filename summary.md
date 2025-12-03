# gitskills

[廖雪峰Git教程](https://liaoxuefeng.com/books/git/customize/ignore/index.html)

### 创建版本库

- `git init`  初始化
- `git add file.txt` 把文件添加到仓库
- `git commit -m "wrote....."`  提交

可以多次add  最后一次commit即可

### 版本回退

- `git log`  查看日志  可以加`--pretty=oneline`参数

在Git中 用`HEAD`表示当前版本 上个版本是`HEAD^` 以此类推 上上个版本就是`HEAD^^` 往上100个版本就是`HEAD~100`

- 要将当前版本回退 `git reset --head HEAD^`
  - `--hard 回退到上个版本的已提交的状态`
  - `--soft` 回退到上个版本的未提交的状态
  - `--mixed`  回退到上个版本已添加但未提交的状态

- 如果回退旧版本后又想回到新版本  `git reflog`  使用commit id  `git reset --hard commid id`

### 工作区和暂存区

`git status` 查看状态>>>>>>> dfe4548ab2842122a3d0960f86587458ab8dc215

工作区有一个隐藏目录.git 是Git的版本库 里面有个暂存区 还有Git的第一个分支master(main) 以及指向master的指针HEAD

`git add` 实际上是吧文件修改添加到暂存区  `git commit`提交修改把暂存区的内容提交到分支

使用`git diff HEAD -- readme.txt` 查看工作区和版本库里最新版本的区别

#### 撤销修改

使用git status可以给出状态和命令提示

1. 只在工作区修改了  `git restore -- file.txt`即可
2. 使用了`git add` 这种情况我们需要用 `git restore --staged README.md` 回退到第一种情况
3. 使用了 `git add  git commit -m ""`  这种情况只能版本回退`git reset --hard HEAD^`

#### 删除文件

假如`git add git commit -m ""`了一个文件 然后`rm`了

`git status`会发现哪些文件被删除 两个选择

- 意识从版本库中删除该文件 `git rm file.txt` 并且 `git commit -m ""`
- 恢复 `git checkout -- text.txt` 或者 `git restore -- text.txt`

#### 远程仓库

- 添加远程仓库 `$ git remote add origin git@github.com:username/learngit.git`添加后，远程库的名字就是`origin`，这是Git默认的叫法，也可以改成别的，但是`origin`这个名字一看就知道是远程库。
- 第一次推送 `git push -u origin master`  之后`git push origin master`
- 如果添加的时候地址写错了，或者就是想删除远程库，可以用`git remote rm <name>`命令。使用前，建议先用`git remote -v`查看远程库信息

### 分支管理

#### 创建和合并

- 查看分支：`git branch`

- 创建分支：`git branch <name>`

- 切换分支：`git checkout <name>`或者`git switch <name>`

- 创建+切换分支：`git checkout -b <name>`或者`git switch -c <name>`

- 合并某分支到当前分支：`git merge <name>`

- 删除分支：`git branch -d <name>`

#### 解决冲突

假如先创建一个分支feature1 然后在feature1和 master中 都对同一个文件做了不同的修改 然后`git merge`会有冲突

需要手动解决冲突  **使用 `git status`可以告诉冲突的文件**

Git用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容

```
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature1
```

#### 分支管理策略

通常，合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

使用`--no-ff`参数

`git merge --no-ff -m "merge with no-ff" dev`

`git log --graph --pretty=oneline --abbrev-commit`  查看分支历史

不使用`Fast forward`模式，merge后就像这样：

```
                                 HEAD
                       
>>>>>>> dfe4548ab2842122a3d0960f86587458ab8dc215
           │
                                  ▼
                                master
                                  │
                                  ▼
                                ┌───┐
                         ┌─────▶│   │
┌───┐    ┌───┐    ┌───┐  │      └───┘
│   │───▶│   │───▶│   │──┤        ▲
└───┘    └───┘    └───┘  │  ┌───┐ │
                         └─▶│   │─┘
                            └───┘
                              ▲
                              │
                             dev
```

#### BUG分支

假如目前正在dev分支上进行工作，但突然急需修复一个bug  需要创建一个新的分支 但目前分支的工作尚未完成

- 使用 `git stash` 把当前现场储存起来 以后恢复

- 然后看在哪个分支上修复bug  如果在master上修复 就从master上创建分支 

- 修复并合并以及删除分支后 使用`git stash list` 查看工作现场

- 一是用`git stash apply`恢复，但是恢复后，`stash`内容并不删除，你需要用`git stash drop`来删除；

  另一种方式是用`git stash pop`，恢复的同时把`stash`内容也删了

还有一个问题是 我们在master分支上修复了bug 但dev分支并没有 同样的bug，要在`dev`上修复，我们只需要把`4c805e2 fix bug 101`这个提交所做的修改“复制”到`dev`分支。注意：我们只想复制`4c805e2 fix bug 101`这个提交所做的修改，并不是把整个`master`分支merge过来。

需要用 `git cherry-pick 4c805e2`

#### Feature分支

每添加一个新的功能 创建一个新的feature分支  开发完后合并 最后删除 若开发完不想合并 可以使用命令强行删除

`git branch -D feature-vulcan`

#### 多人协作

- 推送分支  `git push origin master`  其他分支 `git push origin dev`
- `git clone`  后一般只有主分支  需要创建远程的 orhttps://liaoxuefeng.com/books/git/customize/ignore/index.htmligin 的 dev 分支到本地 `git switch -c dev origin/dev`

当两个人同时对dev分支的文件就行修改 产生冲突  先git pull 然后解决冲突 最后 git push 如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建 会有提示

#### Rebase

把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了。

`git rebase`

### 标签管理

#### 创建标签

- 命令`git tag <tagname>`用于新建一个标签，默认为`HEAD`，也可以指定一个commit id  `git tag v0.9 commit id`
- `git show v0.9` 查看标签信息
- 命令`git tag -a <tagname> -m "blablabla..."`可以指定标签信息；
- 命令`git tag`可以查看所有标签。

#### 操作标签

- 命令`git push origin <tagname>`可以推送一个本地标签；
- 命令`git push origin --tags`可以推送全部未推送过的本地标签；
- 命令`git tag -d <tagname>`可以删除一个本地标签；
- 命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签。

### 忽略特殊文件

[Github/gitignore](https://github.com/github/gitignore)

忽略文件的原则是：

1. 忽略操作系统自动生成的文件，比如缩略图等；
2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的`.class`文件；
3. 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

可以通过[GitIgnore Online Generator](https://michaelliao.github.io/gitignore-online-generator/)在线生成`.gitignore`文件并直接下载。

后一个问题：`.gitignore`文件放哪？答案是放Git仓库根目录下，但其实一个Git仓库也可以有多个`.gitignore`文件，`.gitignore`文件放在哪个目录下，就对哪个目录（包括子目录）起作用。

```
myproject          <- Git仓库根目录
├── .gitigore      <- 针对整个仓库生效的.gitignore
├── LICENSE
├── README.md
├── docs
│   └── .gitigore  <- 仅针对docs目录生效的.gitignore
└── source
```

### 配置别名

配置Git的时候，加上`--global`是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。

配置文件放哪了？每个仓库的Git配置文件都放在`.git/config`文件中：

而当前用户的Git配置文件放在用户主目录下的一个隐藏文件`.gitconfig`中：

### 搭建服务器

pass

