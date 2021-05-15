# 常用 Git 命令清单

> 参考资料如下：
>
> [常用 Git 命令清单](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)
>
> [Git教程](https://www.yiibai.com/git)
>
> [Git系列之Refs 与 Reflog](https://segmentfault.com/a/1190000007996197)
>
> [使用git stash命令保存和恢复进度](https://blog.csdn.net/daguanjia11/article/details/73810577)



一般来说，日常使用只要记住下图 6 个命令，就可以了。但是熟练使用，恐怕要记住 60～100 个命令。

![Git](/Users/bytednce/Documents/ByteDance/Git/git.png)

下面是常用 Git 命令清单。几个专用名词的译名如下：

- Workspace：工作区
- Index / Stage：暂存区
- Repository：仓库区（或本地仓库）
- Remote：远程仓库

## 一、新建代码库

```bash
# 在当前目录新建一个Git代码库
$ git init

# 新建一个目录，将其初始化为Git代码库
$ git init [project-name]

# 下载一个项目和它的整个代码历史
$ git clone [url]

# 下载一个项目最新的一次commit。应用场景：网络不佳且待下载项目较大
$ git clone --depth=1 [url]
```

## 二、配置

Git的设置文件为 `.gitconfig`，它可以在用户主目录下（全局配置），也可以在项目目录下（项目配置）。

```bash
# 显示当前的Git配置
$ git config --list

# 编辑Git配置文件
$ git config -e [--global]

# 设置提交代码时的用户信息
$ git config [--global] user.name "[name]"
$ git config [--global] user.email "[email address]"
```

说明：设置提交代码时的用户信息时，可选参数包含 `--global`、`--system`、`--local`。

- `--global` 表示给整个计算机一次性设置
- `--system` 表示给当前用户一次性设置
- `--local` 表示给当前项目一次性设置

上述三个配置优先级从低到高，即高优先级配置会覆盖低优先级配置。

## 三、增加/删除文件

```bash
# 添加指定文件到暂存区
$ git add [file1] [file2] ...

# 添加指定目录到暂存区，包括子目录
$ git add [dir]

# 添加当前目录的所有文件到暂存区，会过滤掉.gitignore配置文件中指定忽略的文件或目录
$ git add .

# 添加每个变化前，都会要求确认。对于同一个文件的多处变化，可以实现分次提交
$ git add -p

# 删除工作区文件，并且将这次删除放入暂存
$ git rm [file1] [file2] ...

# 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [file]

# 改名文件，并且将这个改名放入暂存区
$ git mv [file-original] [file-renamed]

# 撤销暂存区的修改，即撤销git add操作，保留工作区的修改
$ git reset [file]
```

说明：

- 使用 `git rm` 来删除文件，同时还会将这个删除操作记录下来，`git commit` 之后仓库区中相应文件也就被删除了；而使用 `rm` 来删除文件，仅仅是删除了物理文件，没有将其从 `git` 的记录中删除，使用 `git checkout [file]` 还能恢复文件。
  - `git rm` 删除过的文件，执行 `git commit` 提交时，会自动将删除该文件的操作提交上去。
  - 用 `rm` 命令直接删除的文件，执行 `git commit` 提交时，则不会将删除该文件的操作提交上去。不过不要紧，即使已经通过 `rm` 将某个文件删除掉了，也可以再通过 `git rm` 命令重新将该文件从 git 的记录中删除掉。
  
- `git mv [file-original] [file-renamed]` 相当于执行了三条命令：

  ```bash
  $ mv [file-original] [file-renamed]
  $ git rm [file-original]
  $ git add [file-renamed]
  ```

## 四、代码提交

```bash
# 提交暂存区到仓库区
$ git commit -m [message]

# 提交暂存区的指定文件到仓库区
$ git commit [file1] [file2] ... -m [message]

# 直接提交工作区自上次commit之后的变化到仓库区
# 相当于add和commit两步操作合二为一，前提是文件不是第一次被track
$ git commit -a

# 提交时显示所有diff信息
$ git commit -v

# 使用一次新的commit，替代上一次提交
# 如果代码没有任何新变化，则用来改写上一次commit的提交信息
$ git commit --amend -m [message]

# 重做上一次commit，并包括指定文件的新变化
# 指定文件必须是已经被追踪的，不能是新建的
$ git commit --amend [file1] [file2] ...
```

## 五、分支

```bash
# 列出所有本地分支
$ git branch

# 列出所有远程分支
$ git branch -r

# 列出所有本地分支和远程分支
$ git branch -a

# 新建一个分支，但依然停留在当前分支
$ git branch [branch-name]

# 新建一个分支，并切换到该分支
$ git checkout -b [branch]

# 新建一个分支，指向指定commit(SHA1值)
$ git branch [branch] [commit]

# 新建一个分支，与指定的远程分支建立追踪关系
$ git branch --track [branch] [remote-branch]

# 切换到指定分支，并更新工作区。切换前当前工作区和暂存区需要保持clean
$ git checkout [branch-name]

# 切换到上一个分支
$ git checkout -

# 建立追踪关系，在现有分支与指定的远程分支之间
$ git branch --set-upstream [branch] [remote-branch]

# 合并指定分支到当前分支
$ git merge [branch]

# 选择一个commit，合并进当前分支
$ git cherry-pick [commit]

# 删除本地分支，如果分支有未合并的更改，则不会删除
$ git branch -d [branch-name]

# 删除本地分支，即使它有未合并的更改
$ git branch -D [branch-name]

# 删除远程分支
$ git push origin --delete [branch-name]
$ git push origin :[branch-name]

# 删除本地的远程跟踪分支
$ git branch -dr [origin/branch-name]
```

## 六、标签

```bash
# 列出所有tag
$ git tag

# 新建一个tag在当前 commit
$ git tag [tag]

# 新建一个tag在指定commit
$ git tag [tag] [commit]

# 删除本地tag
$ git tag -d [tag]

# 删除远程tag
$ git push origin :refs/tags/[tagName]

# 查看tag信息
$ git show [tag]

# 提交指定tag，并不会在远程仓库创建新的commit
$ git push [origin] [tag]

# 提交所有tag，并不会在远程仓库创建新的commit
$ git push [origin] --tags

# 新建一个分支，指向某个tag
$ git checkout -b [branch] [tag]
```

## 七、查看信息

```bash
# 显示有变更的文件
$ git status

# 显示当前分支的版本历史
$ git log

# 显示commit历史，以及每次commit发生变更的文件（stat: 统计）
$ git log --stat

# 根据关键词搜索提交历史（代码文件中的关键词）
$ git log -S [keyword]

# 显示某个文件的版本历史，包括文件改名
$ git log --follow [file]
$ git whatchanged [--stat] [file]

# 显示指定文件相关的每一次diff
$ git log -p [file]

# 显示过去5次提交
$ git log -5 --pretty --oneline

# 显示所有提交过的用户，按提交次数排序
$ git shortlog -sn

# 显示指定文件是什么人在什么时间修改过
$ git blame [file]

# 显示暂存区和工作区的差异
$ git diff

# 显示暂存区和上一个commit的差异
$ git diff --cached [file]

# 显示工作区与当前分支最新commit之间的差异
$ git diff HEAD

# 显示两个分支之间的差异
$ git diff [branch1] [branch2]

# 显示两个commit之间的差异
$ git diff [commit1] [commit2]

# 显示今天你写了多少行代码
$ git diff --shortstat "@{0 day ago}"

# 显示某次提交的元数据和内容变化
$ git show [commit]

# 显示某次提交发生变化的文件
$ git show --name-only [commit]

# 显示某次提交时，某个文件的内容
$ git show [commit]:[filename]

# 显示当前分支的最近几次提交
$ git reflog
```

注意：当某一分支的版本历史信息较多时，`git log` 会分页展示信息。若不想继续查看历史信息，在末行的命令行模式 `:` 下输入 `q`，然后 `enter` 即可。

## 八、远程同步

```bash
# 下载远程仓库的所有变动
$ git fetch [remote]

# 显示所有远程仓库
$ git remote -v

# 显示某个远程仓库的信息
$ git remote show [remote]

# 增加一个新的远程仓库，并命名
$ git remote add [shortname] [url]

# 取回远程仓库的变化，并与本地分支合并
$ git pull [remote] [branch]

# 上传本地指定分支到远程仓库指定分支
$ git push [remote] [local_branch] : [remote_branch]

# 上传本地指定分支到远程仓库
$ git push [remote] [branch]

# 强行推送当前分支到远程仓库，即使有冲突
$ git push [remote] --force

# 推送所有分支到远程仓库
$ git push [remote] --all
```

## 九、撤销

```bash
# 恢复暂存区的指定文件到工作区
$ git checkout [file]

# 恢复某个commit的指定文件到暂存区和工作区
$ git checkout [commit] [file]

# 恢复暂存区的所有文件到工作区
$ git checkout .

# 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
$ git reset [file]

# 重置暂存区与工作区，与上一次commit保持一致
$ git reset --hard

# 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
$ git reset [commit]

# 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
$ git reset --hard [commit]

# 重置当前HEAD为指定commit，但保持暂存区和工作区不变
$ git reset --keep [commit]

# 新建一个commit，用来撤销指定commit
# 后者的所有变化都将被前者抵消，并且应用到当前分支
$ git revert [commit]

# 将未提交的变化暂存，稍后再恢复。应用场景：需要切换到其他分支且当前分支存在未commit的代码。

# 保存当前工作区和暂存区的工作进度
$ git stash [save 'message']

# 显示保存进度的列表
$ git stash list

# 恢复最近的进度（工作区和暂存区）到工作区，带pop的恢复命令均会删除恢复的进度
$ git stash pop

# 恢复指定的进度到工作区和暂存区（原来在暂存区的进度依然恢复到暂存区），其中stashId为0, 1, 2...
$ git stash pop --index [stashId]

# 恢复指定的进度到工作区（原来在暂存区的进度也恢复到工作区）
$ git stash pop stash@{stashId}

# 恢复最近的进度到工作区，不删除进度
$ git stash apply

# 恢复指定的进度到工作区和暂存区，不删除进度
$ git stash apply --index [stashId]

# 删除进度，未指定stashId时删除最近的一次进度
$ git stash drop [stashId]

# 删除所有进度
$ git stash clear
```

