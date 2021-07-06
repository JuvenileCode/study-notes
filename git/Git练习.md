##  Git练习

### 插入Linux命令

- `which`  - **查找应用程序安装目录，通过PATH环境变量来查找**
- `whereis` - **用来查找，与which不同通过文件索引数据库查找，范围更广**
- `echo "create txt file > settings.properties"` 快速创建文件



### Git 基本命令

- Git三种工作环境（1、索引区   2、工作区   3、版本库[本地仓库/远程仓库]）
- 创建Git版本库
  - 进入当前文件夹执行`init`命令
- `git status` 查看当前版本库文件情况
-  `git add`(git add test.txt) 将文件纳入到Git的缓存区中
- `git rm --cached`(git rm --cached test.txt) 将文件从缓冲区删除
- `git commit` 将文件提交到本地版本库中
- `git reset HEAD`(git rest HEAD t.txt) 回退到上一个版本，也可以指定回退版本
- `git log` 查看提交日志 `git log --graph` 带图形查看
- `git reflog` 查看git操作日志，在版本回退有用
- `git checkout`(git checkout -- test.txt)丢弃上次修改
- `git rm`(git rm test2.txt) 删除文件且纳入缓存区，若想恢复文件则：
  - `git reset HEAD test2.txt` 将文件从暂存区恢复至工作区
  - `git checkout -- test2.txt`讲工作区的修改丢弃掉
- `git mv`(git mv test.txt test2.txt) 将文件重命名
- `git commit --amend -m '修正提交的日志' ` 修正上一次提交的备注
- `git config --global alias.br branch` 定义git命令别名 `alias`  `--global`git作用域,多个命令用``引用

### 标签与diff

`git tag` 列出本地所有标签

 - Git标签：分轻量级标签和带有附注标签
   - `git tag v1.0.1` 创建一个轻量级标签
   - `git tag -a v1.02 -m 'release'`创建一个带有附注的标签
   - `git tag` 列出所有标签、`git tag -l 'v*'` 查找标签
   - `git tag -d 标签名` 删除标签
- `git show v1.0` 查看标签
- `git blame test.txt` 查看文件修改详细信息
- diff：文件差异性
  - `git diff` 比较索引区和工作区文件差别
  - `git diff HEAD` 比较工作区和最新提交的区别 
  - `git diff --cached` 比较索引区和最新提交的区别（也可以指定提交ID）

### .gitignore

> .gitignore 用来配置忽略的文件，[配置规则参考](https://segmentfault.com/a/1190000017138049)
> **mac来删除当前目录.DS_Store文件 **`find . -name ".DS_Store" -depth -exec rm {} \;`

### 本地分支

**基本操作**

- `git branch` 查看当前仓库分支
- `git branch ` (git branch new_branch) 创建新的分支
- `git checkout`(git checkout new_branch) 切换分支
  - `git checkout -b`（git checkout -b  new_branch_2） 创建并切换到创建的新分支
  - `git checkout -- test.txt` 丢弃掉相对于暂存区最后一次添加的文件内容
- `git branch -d`(git branch -d new_branch)删除分支
- `git merge`(git merge new_branch) 将new_branch分支内容合并到当前分支
- 版本回退操作
  - `git reset --hard HEAD^` 回退上一个版本
  - `git reset --hard HEAD~2` 回退上2个版本，由`~2`指定
  - `git reset --hanrd comm_id` 回退到指定的版本

### 远程操作

- 初始化将本地内容推送到远程仓库
  1. `git remote add origin https://github.com/gitlecture/gitlecture.git`
  2. `git push -u origin master`  按操作提示输入远程仓库认证
- `git clone https://git...` 从远程仓库拉取代码
- `git remote show` 列出本项目所关联的所有远程仓库，`git remote show name` 详情
- `git push`  本地仓库推送到远程仓库
- `git branch -a` 查看本地及远程所有分支 `git branch -av` 显示最后一次提交信息
- 本地新建分支且推送到远程 `git push --set-upstream origin test`
- 远程拉取分和本地分支建立关系 `git checkout -b test origin/test`
- 删除远程分支
  - `git push origin :deleteBranch` 
  - `git push origin --delete deleteBranch`
- `git push origin 标签名` 将指定标签信息推送到远程
- `git push orinin --tags` 将本地未推送到远程的所有标签推送到远程
- `git push origin :refs/tags/标签名` 删除远程的标签 ，或者：`git push origin --delete tag 标签名`
- `pull` 是拉取所有，拉取指定标签 `git fetch origin tag 标签名`
- `git log origin/远程分支` 查看远程分支的日志

### Git submodule

- Git裸库：用于存放或者同步给仓库文件（不再上面进行开发） ：
  - `git init --bare`

> git submodule 子模块，允许一个git仓库引用另一个git仓库

- `git submodule add 远程仓库地址 本地存放地址`  初始：建一个依赖仓库放入本地git开发库
- `git submodule foreach git pull` 更新所有submodule模块信息

**一个新的使用者clone带有submodule仓库**

先clone项目，在初始化子模块：

1. `git submodule init`  初始化子模块
2. `git submodule update --recursive`   更新子模块文件

在clone时候将子模块信息拉取下来：

- `git clone 远程仓库地址 --recursive`

### Git subtree

> git subtree 应用场景与 git submodule一样，git subtree优化双向修改

- git subtree初始化关联另一个远程仓库

  1. `git remote add subtree-origin 远程仓库地址` 

  2. `git subtree add --prefix=subtree subtree-origin master`

- `git subtree pull --prefix=subtree subtree-origin master`主仓库subtree代码pull 
- `git subtree push --prefix=subtree subtree-origin master` 主仓库修改subtree然后提交，先push subtree子模块，然后执行上述命令推送

### Git cherry-pick

> 将A分支的修改应用到B分支

- `git cherry-pick commitId`  将某一个分支的commitId应用到所在分支

### Git rebase

> rebase :变基 ，在某种程度来说，rebase和merge可以完成类似的工作，不过二者工作方式有着显著的差异。rebase会修改分支提交历史，一般只在自己本地分支使用，与其他人共享分支不推荐使用。

- `git rebase 分支名`  rebase合并分支
- `git rebase --continue`  rebase解决冲突继续执行

- `git brbase --abort` 终止当前的rebase操作，重回之前的状态



