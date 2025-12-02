# gitskills

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

`git status` 查看状态

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

#### Feature分支

#### 多人协作

#### Rebase



